The Wayback Machine - https://web.archive.org/web/20240823050616/https://www.cursor.com/blog/instant-apply

![Author image](https://web.archive.org/web/20240823050616im_/https://www.cursor.com/_next/image?url=%2Fauthors%2Faman.jpg&w=128&q=75)

Posted by Aman

May 14, 2024

[Back to Blog Index](https://web.archive.org/web/20240823050616/https://www.cursor.com/blog)

[Root](https://web.archive.org/web/20240823050616/https://www.cursor.com/blog/instant-apply#root)

›

[Evaluating Prompted Rewrites](https://web.archive.org/web/20240823050616/https://www.cursor.com/blog/instant-apply#evaluating-prompted-rewrites)

·

[Speed Measurements](https://web.archive.org/web/20240823050616/https://www.cursor.com/blog/instant-apply#speed-measurements)

·

[Diff Models](https://web.archive.org/web/20240823050616/https://www.cursor.com/blog/instant-apply#diff-models)

·

[Training](https://web.archive.org/web/20240823050616/https://www.cursor.com/blog/instant-apply#training)

·

[Future Directions](https://web.archive.org/web/20240823050616/https://www.cursor.com/blog/instant-apply#future-directions)

·

[Bonus Challenge](https://web.archive.org/web/20240823050616/https://www.cursor.com/blog/instant-apply#bonus-challenge)

Frontier models such as GPT-4o struggle on large edits, with problems of laziness, inaccuracy, and high-latency.

This is a weakness visible in coding agents.
Accurately editing hundreds of lines can take multiple model calls, at times trapping the agent in an infinite loop. Even small, isolated edits are plagued with bugs:

‌

Figure 1: SWE-Agent attempts a simple edit seven times before giving up due to a consistent syntactic error.

Worst of all, existing models are slow at large edits, breaking the programmer out of flow.

We've trained a specialized model on an important version of the full-file code edit task called **fast apply**.

Difficult code edits can be broken down into two stages: **planning**, and **applying**.

In Cursor, the planning phase takes the form of a chat interface with a powerful frontier model.
Applying the change to the current file should be straightforward and _instant_.

![](https://web.archive.org/web/20240823050616im_/https://www.cursor.com/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Flazy_rewrite.eebac342.png&w=3840&q=75)

Figure 2: A toy example of a change we want to 'apply'. It cannot easily be copy/pasted since it sketches out the change over a full class.

Our fast-apply model surpasses GPT-4 and GPT-4o performance and pushes the pareto frontier on the accuracy / latency curve.
We achieve speeds of **~1000 tokens/s** (around 3500 char/s) on our 70b model using a speculative-decoding variant tailored for code-edits, called **speculative edits**.

This means a **~13x** speedup over vanilla inference using Llama-3-70b and a ~9x speedup over our previous GPT-4 speculative edits deployment.

‌

Figure 3: On the top is our new 70b model with speculative edits. Below is GPT-4 turbo also with speculative edits. In this case and for smaller files, the speedup is closer to 4-5x

By default, we have language models generate the **fully rewritten file** conditioned on the _current file_, the _conversation history_, and the _current code block_.

In this post, we explain how we trained and evaluated our new model.
We show why we rewrite the file instead of using diffs and how speculative edits give us such ridiculous speedups.

## Evaluating Prompted Rewrites

We construct an evaluation set of ~450 full-file edits of files under 400 lines, then measure the performance of several prompted models using Claude-3 Opus as a grader.

Across tens of curated examples, Opus-based grading shows more agreement with our ratings than GPT-4-Turbo or GPT-4o.

![](https://web.archive.org/web/20240823050616im_/https://www.cursor.com/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fguidelines.8e5f6be0.png&w=2048&q=75)

Figure 4: The Priompt component for grading guidelines

These scores likely bias towards outputs of Claude models.
But the scores also match our qualitative assessments of the models.

![](https://web.archive.org/web/20240823050616im_/https://www.cursor.com/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fzero_shot_evals.ccdf3b0b.png&w=2048&q=75)

Figure 5: Surpisingly, claude-3-sonnet outperforms gpt-4-turbo. And gpt-4o performs similarly to gpt-4-turbo.

We hypothesize Claude's superior performance is an artifact of post-training.
The claude models output thousands of LOC in assistant messages, while GPT-4 omits code and indicates missing regions with `...` or comments.

GPT-4's poor performance can also be attributed to **unrelated changes**.
GPT-4 deletes commented out code and unnecessary newlines.
It has a tendency to "fix/clean up" unrelated code.

## Speed Measurements

We measure speed as:

speed=Num Rewritten CharsLatency for Rewrite in seconds\\text{speed} = \\frac{\\text{Num Rewritten Chars}}{\\text{Latency for Rewrite in seconds}}

This has the advantages of:

1. Normalizes speed across different tokenizers
2. Presents a single number we care about across various prompt/generated token lengths (vs counting both TTFT and generation char/s)
3. Gives a strong lower-bound on generation-speed since the latency includes TTFT. For most tokenizers and text, a token is 3-4 characters, so dividing char/s by 4 gives a tokens/s lower bound.

![](https://web.archive.org/web/20240823050616im_/https://www.cursor.com/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fprompted_speed.3c1e0cbb.png&w=2048&q=75)

Figure 6: Top and to the right is better. opus, sonnet, gpt-4o, and haiku are on the pareto frontier

When we include speedups from gpt-4-turbo speculative edits:

![](https://web.archive.org/web/20240823050616im_/https://www.cursor.com/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fprompted_speed_spec.f7a39690.png&w=2048&q=75)

Figure 7: With speculative edits, gpt-4-turbo performs similarly to gpt-4o. Speculative edits are not yet possible with gpt-4o, otherwise gpt-4o-spec would be the speed frontrunner.

## Diff Models

Why do we have the model rewrite the entire file, instead of suggesting diffs?

We found that language models struggle with diff-formatted edits for a few possible reasons:

**Thinking in Fewer Tokens** \- With more output tokens, the model has more forward passes to determine the correct solution.1
Diffs force the model to think in fewer tokens.

**Diffs are Out of Distribution** -
In pretraining and especially post-training, models have likely seen more full-files of code than diffs of code.

**Outputting Line Numbers** \- If a tokenizer treats a sequence of numbers (i.e. 123) as a single token, it must make up its mind about the correct line number for the diff
on a single (often the first) output token. Furthermore, models are notoriously bad at counting line numbers.

Motivated by the diff format from [Aider](https://web.archive.org/web/20240823050616/https://aider.chat/docs/unified-diffs.html), we eliminate the line number problem.
Instead of a standard diff format, the model proposes diff hunks as search/replace blocks:

```diff
@@ ... @@
 function binarySearch(arr, x) {
-    let low = 0, high = arr.length - 1;
-    while (low <= high) {
-        let mid = Math.floor((low + high) / 2);
-        if (arr[mid] === x) {
-            return mid;
-        }
-        low += 1;
-    }
-    return -1;
+    let low = 0, high = arr.length - 1;
+    while (low <= high) {
+        let mid = Math.floor((low + high) / 2);
+        if (arr[mid] === x) {
+            return mid;
+        } else if (arr[mid] < x) {
+            low = mid + 1;
+        } else {
+            high = mid - 1;
+        }
+    }
+    return -1;
 }

```

We replace all lines starting with `-` or ` ` with lines starting with `+` or ` `.
The diffs have redundant `-` s and `+` s so the diff-parsing system is robust to minor model failures.2

Most models fail to output accurate diffs, with the exception of Claude Opus.

![](https://web.archive.org/web/20240823050616im_/https://www.cursor.com/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fopus_diff_speed.7da2e86e.png&w=2048&q=75)

Figure 8: claude-3-opus-diff is Claude Opus prompted with a modified version
of Aider's diff format. It beats gpt-4-turbo-spec on speed and accuracy.
gpt-4o-diff is omitted from the graph as it achieves a median speed of 2476
char/s, but performs far worse than claude-3-haiku (4.18 avg eval score)

## Training

The claude-3-opus-diff apply model is faster and more accurate than gpt-4-turbo-spec, but is still **too slow**.

It is not possible to build speculative edits into any of anthropic's models, so we need to train and deploy a performant custom model.

### Synthetic Data

We begin with a small number of "fast-apply" prompts and an abundance of [cmd-k](https://web.archive.org/web/20240823050616/https://www.cursor.com/features#cmd-k) prompts.

![](https://web.archive.org/web/20240823050616im_/https://www.cursor.com/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fcmdk.634f210b.png&w=2048&q=75)

Figure 9: An example \[cmd-k\](/features#cmd-k) prompt and the corresponding generation. Cmd-k lets you instruct the model to make an edit in a selected region.

A cmd-k prompt loosely resembles the data we need for fast-apply.
It contains an edit instruction, and a code selection in the current file.

For each edit instruction, we have GPT-4 produce a chat response given the current file, then have a language model "apply" the change.
We use the small dataset of "real" apply inputs to produce additional higher-quality apply datapoints.
Finally, we concatenate these datasets together in an 80/20 mix to get our finetuning data.

![](https://web.archive.org/web/20240823050616im_/https://www.cursor.com/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fpipeline_transparent.943113fb.png&w=3840&q=75)

Figure 10: Our data pipeline for generating the fast-apply finetuning dataset. The fully synthetic data is lower quality. For instance, in the example above, the selection range (which we throw away) is crucial for an accurate edit.

### Model Training

We train the Deepseek Coder Instruct and Llama 3 model families. To improve our finetuning dataset, we:

1. We downsample small files since they were over-represented in our train set (<100 LOC).
2. We downsample the number of training examples per filename.
3. We downsample datapoints that resulted in no-ops.3

![](https://web.archive.org/web/20240823050616im_/https://www.cursor.com/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fwandb.cfc03004.png&w=3840&q=75)

Figure 11: We use bits-per-byte to normalize loss across different tokenizers. The Deepseek model uses a more agressive filtering strategy by filename.

We find that our best model (llama-3-70b-ft) almost matches claude-3-opus-diff and outperforms gpt-4-turbo and gpt-4o.

![](https://web.archive.org/web/20240823050616im_/https://www.cursor.com/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fllama3_ft_speed.8f12e7fd.png&w=2048&q=75)

Figure 12: Llama-3-70b-ft outperforms gpt-4-turbo-spec in performance

All three finetuned models outperform gpt-4-turbo on evals, but we could _feel_ the difference between the finetuned deepseek-33b and llama-3-70b.4
Llama-3-70b felt better than other finetuned models and gpt-4-turbo.
The other finetuned models felt not-quite-useful enough, often less useful than GPT-4.

### Speculative edits

Our biggest win comes from our custom speculative decoding algorithm called “speculative edits”.
It is equivalent to a full-file-rewrite, while being up to 9x faster.

With code edits, we have a strong prior on the draft tokens at any point in time, so we can speculate on future tokens using a deterministic algorithm rather than a draft model.

We've worked with [Fireworks](https://web.archive.org/web/20240823050616/https://fireworks.ai/) to deploy our fast-apply model with strong speculative edits support.
They have a fantastic inference engine and built out api support for our custom speculation logic.

This makes the gain from speculative edits larger for Llama-3 than GPT-4, meaning a 4-5x speedup over the next fastest model.

![](https://web.archive.org/web/20240823050616im_/https://www.cursor.com/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fllama3_spec_speed.6daa892c.png&w=2048&q=75)

Figure 13: Speculative edits give our finetuned model a \*\*massive\*\* speedup.

## Future Directions

**Long Context Training** \- We are working on long-context training to rewrite files up to 2500 lines long.
Naive linear scaling of RoPE position ids works poorly -- as do current community long-context finetunes of Llama 3 70b.

**Knowledge Distillation** \- We'also like to distill the "fast apply" abilities of the current model into a smaller one, particularly llama-3-8b.
The lower latencies from smaller models will matter more for larger files.

**Even Better Accuracy** \- Some form of on-policy RL using data from the newly rolled-out model should give us additional performance wins.

Past its usefulness in chat, fast-apply is crucial building block for more sophisticated code-generation systems.
As models get more capable at reasoning/planning, there are increasing benefits to a low-latency apply!

* * *

If you haven't already, you should try out this feature in [Cursor](https://web.archive.org/web/20240823050616/https://www.cursor.com/)! It's a small
example of the polish and depth that goes into the product.

This blog post is a reasonable sample of the applied research we do at Anysphere.5
We build application-specific inference speedups like speculative edits, train and evaluate task-specific models, and ship these in useful features to users.

We're hiring research engineers and software engineers! You can learn more about [Anysphere here](https://web.archive.org/web/20240823050616/https://www.anysphere.inc/).

## Bonus Challenge

We've sketched out how speculative edits work, but have not explained the full algorithm in detail.

As an exercise, try implementing speculative edits with openai's API.
(hint: for most openai accounts, this is only possible with davinci-002 or babbage-002, so your solution should just use those models)

If you're up for a harder challenge, we'd recommend an implementation of speculative edits in vllm.
For the most difficult version, implement this in TensorRT-LLM.

I'd love to see your solutions! Feel free to send them [over twitter](https://web.archive.org/web/20240823050616/https://twitter.com/amanrsanger).
