## Select your cookie preferences

We use essential cookies and similar tools that are necessary to provide our site and services. We use performance cookies to collect anonymous statistics, so we can understand how customers use our site and make improvements. Essential cookies cannot be deactivated, but you can choose “Customize” or “Decline” to decline performance cookies.

If you agree, AWS and approved third parties will also use cookies to provide useful site features, remember your preferences, and display relevant content, including relevant advertising. To accept or decline all non-essential cookies, choose “Accept” or “Decline.” To make more detailed choices, choose “Customize.”

AcceptDeclineCustomize

## Customize cookie preferences

We use cookies and similar tools (collectively, "cookies") for the following purposes.

### Essential

Essential cookies are necessary to provide our site and services and cannot be deactivated. They are usually set in response to your actions on the site, such as setting your privacy preferences, signing in, or filling in forms.

### Performance

Performance cookies provide anonymous statistics about how customers navigate our site so we can improve site experience and performance. Approved third parties may perform analytics on our behalf, but they cannot use the data for their own purposes.

Allowed

### Functional

Functional cookies help us provide useful site features, remember your preferences, and display relevant content. Approved third parties may set these cookies to provide certain site features. If you do not allow these cookies, then some or all of these services may not function properly.

Allowed

### Advertising

Advertising cookies may be set through our site by us or our advertising partners and help us deliver relevant marketing content. If you do not allow these cookies, you will experience less relevant advertising.

Allowed

Blocking some types of cookies may impact your experience of our sites. You may review and change your choices at any time by selecting Cookie preferences in the footer of this site. We and selected third-parties use cookies or similar technologies as specified in the [AWS Cookie Notice](https://aws.amazon.com/legal/cookies/).

CancelSave preferences

## Unable to save cookie preferences

We will only store essential cookies at this time, because we were unable to save your cookie preferences.

If you want to change your cookie preferences, try again later using the link in the AWS console footer, or contact support if the problem persists.

Dismiss

Documentation

# Get started

Public Preview

Kiro is currently in public preview. Features and documentation may change as we improve the product.

Kiro is an agentic IDE that helps you do your best work with features such as specs, steering, and hooks.

## [Copied!](https://kiro.dev/docs/getting-started/\#get-started) Get started

Everything you need to begin your journey with Kiro in under 5 minutes.

[**Download & Install** \\
\\
Get Kiro running on your machine\\
\\
Learn more](https://kiro.dev/docs/getting-started/installation/) [**First Project** \\
\\
Learn about Kiro's features through a hands-on project\\
\\
Learn more](https://kiro.dev/docs/getting-started/first-project/)

## [Copied!](https://kiro.dev/docs/getting-started/\#core-capabilities) Core capabilities

Learn about Kiro's core feature set.

[**Specs** \\
\\
Plan and build features using structured specifications.\\
\\
Learn more](https://kiro.dev/docs/specs/) [**Hooks** \\
\\
Automate repetitive tasks with intelligent triggers.\\
\\
Learn more](https://kiro.dev/docs/hooks/) [**Agentic chat** \\
\\
Build features through natural conversation with AI.\\
\\
Learn more](https://kiro.dev/docs/chat/) [**Steering** \\
\\
Guide AI with custom rules and context.\\
\\
Learn more](https://kiro.dev/docs/steering/) [**MCP Servers** \\
\\
Connect external tools and data sources.\\
\\
Learn more](https://kiro.dev/docs/mcp/) [**Privacy First** \\
\\
Keep code secure with privacy controls.\\
\\
Learn more](https://kiro.dev/docs/reference/privacy-and-security/)

## [Copied!](https://kiro.dev/docs/getting-started/\#learning-resources) Learning resources

Master Kiro through hands-on tutorials and comprehensive documentation.

[**Your first project** \\
\\
A quick overview of how to use Kiro's feature set.\\
\\
Learn more](https://kiro.dev/docs/getting-started/first-project/) [**Interactive Tutorial** \\
\\
Build a real project while learning Kiro's features through our game-based tutorial\\
\\
Learn more](https://kiro.dev/docs/guides/learn-by-playing/)

Page updated: July 14, 2025

[Installation](https://kiro.dev/docs/getting-started/installation/)

* * *

# Types of Hooks
## Select your cookie preferences

We use essential cookies and similar tools that are necessary to provide our site and services. We use performance cookies to collect anonymous statistics, so we can understand how customers use our site and make improvements. Essential cookies cannot be deactivated, but you can choose “Customize” or “Decline” to decline performance cookies.

If you agree, AWS and approved third parties will also use cookies to provide useful site features, remember your preferences, and display relevant content, including relevant advertising. To accept or decline all non-essential cookies, choose “Accept” or “Decline.” To make more detailed choices, choose “Customize.”

AcceptDeclineCustomize

## Customize cookie preferences

We use cookies and similar tools (collectively, "cookies") for the following purposes.

### Essential

Essential cookies are necessary to provide our site and services and cannot be deactivated. They are usually set in response to your actions on the site, such as setting your privacy preferences, signing in, or filling in forms.

### Performance

Performance cookies provide anonymous statistics about how customers navigate our site so we can improve site experience and performance. Approved third parties may perform analytics on our behalf, but they cannot use the data for their own purposes.

Allowed

### Functional

Functional cookies help us provide useful site features, remember your preferences, and display relevant content. Approved third parties may set these cookies to provide certain site features. If you do not allow these cookies, then some or all of these services may not function properly.

Allowed

### Advertising

Advertising cookies may be set through our site by us or our advertising partners and help us deliver relevant marketing content. If you do not allow these cookies, you will experience less relevant advertising.

Allowed

Blocking some types of cookies may impact your experience of our sites. You may review and change your choices at any time by selecting Cookie preferences in the footer of this site. We and selected third-parties use cookies or similar technologies as specified in the [AWS Cookie Notice](https://aws.amazon.com/legal/cookies/).

CancelSave preferences

## Unable to save cookie preferences

We will only store essential cookies at this time, because we were unable to save your cookie preferences.

If you want to change your cookie preferences, try again later using the link in the AWS console footer, or contact support if the problem persists.

Dismiss

Documentation

# Hook Types

Agent Hooks support various trigger types, each designed for specific automation scenarios. Understanding these types helps you choose the right approach for your workflow needs.

## [Copied!](https://kiro.dev/docs/hooks/types/\#on-file-create) On File Create

Trigger when new files matching specific patterns are created in your workspace.

**Use Cases:**

- Generate boilerplate code for new components
- Add license headers to new files
- Set up test files when creating implementation files

**Example: React Component Setup**

```code-highlight

When a new React component file is created, add:
1. Import statements for React and necessary hooks
2. A functional component with TypeScript props interface
3. Export statement
4. Basic styling if applicable
5. A skeleton test file in the appropriate directory

```

## [Copied!](https://kiro.dev/docs/hooks/types/\#on-file-save) On File Save

Trigger when files matching specific patterns are saved.

**Use Cases:**

- Run linting and formatting
- Update related files
- Generate documentation
- Run tests for changed files

**Example: Update Test Coverage**

```code-highlight

When a JavaScript/TypeScript file is saved:
1. Identify the corresponding test file
2. Update tests to maintain coverage for any new functions
3. Run the tests to verify they pass
4. Update any snapshots if necessary

```

## [Copied!](https://kiro.dev/docs/hooks/types/\#on-file-delete) On File Delete

Trigger when files matching specific patterns are deleted.

**Use Cases:**

- Clean up related files
- Update import references in other files
- Maintain project integrity

**Example: Clean Up References**

```code-highlight

When a component file is deleted:
1. Find all imports of this component across the codebase
2. Remove or comment out those import statements
3. Suggest replacements if appropriate

```

## [Copied!](https://kiro.dev/docs/hooks/types/\#manual-trigger) Manual Trigger

Manually execute a hook.

**Use Cases:**

- On-demand code reviews
- Documentation generation
- Security scanning
- Performance optimization

**Example: Code Review Button**

```code-highlight

Review the current file for:
1. Code quality issues
2. Potential bugs
3. Performance optimizations
4. Security vulnerabilities
5. Accessibility concerns

```

Page updated: July 11, 2025

[Hooks](https://kiro.dev/docs/hooks/)

[Management](https://kiro.dev/docs/hooks/management/)

* * *
# Management
## Select your cookie preferences

We use essential cookies and similar tools that are necessary to provide our site and services. We use performance cookies to collect anonymous statistics, so we can understand how customers use our site and make improvements. Essential cookies cannot be deactivated, but you can choose “Customize” or “Decline” to decline performance cookies.

If you agree, AWS and approved third parties will also use cookies to provide useful site features, remember your preferences, and display relevant content, including relevant advertising. To accept or decline all non-essential cookies, choose “Accept” or “Decline.” To make more detailed choices, choose “Customize.”

AcceptDeclineCustomize

## Customize cookie preferences

We use cookies and similar tools (collectively, "cookies") for the following purposes.

### Essential

Essential cookies are necessary to provide our site and services and cannot be deactivated. They are usually set in response to your actions on the site, such as setting your privacy preferences, signing in, or filling in forms.

### Performance

Performance cookies provide anonymous statistics about how customers navigate our site so we can improve site experience and performance. Approved third parties may perform analytics on our behalf, but they cannot use the data for their own purposes.

Allowed

### Functional

Functional cookies help us provide useful site features, remember your preferences, and display relevant content. Approved third parties may set these cookies to provide certain site features. If you do not allow these cookies, then some or all of these services may not function properly.

Allowed

### Advertising

Advertising cookies may be set through our site by us or our advertising partners and help us deliver relevant marketing content. If you do not allow these cookies, you will experience less relevant advertising.

Allowed

Blocking some types of cookies may impact your experience of our sites. You may review and change your choices at any time by selecting Cookie preferences in the footer of this site. We and selected third-parties use cookies or similar technologies as specified in the [AWS Cookie Notice](https://aws.amazon.com/legal/cookies/).

CancelSave preferences

## Unable to save cookie preferences

We will only store essential cookies at this time, because we were unable to save your cookie preferences.

If you want to change your cookie preferences, try again later using the link in the AWS console footer, or contact support if the problem persists.

Dismiss

Documentation

# Hook Management

Effective hook management ensures your automation workflows remain organized, maintainable, and efficient as your project grows.

## [Copied!](https://kiro.dev/docs/hooks/management/\#managing-your-hooks) Managing Your Hooks

Access all your hooks through the Agent Hooks section in the Kiro panel.

### [Copied!](https://kiro.dev/docs/hooks/management/\#enabledisable-hooks) Enable/Disable Hooks

Toggle hooks on/off without deleting them:

- **Quick toggle**: Click the `eye icon` next to any hook in the Agent Hooks panel
- **From hook view**: Select a hook and use the `Hook Enabled` switch in the top-right corner

### [Copied!](https://kiro.dev/docs/hooks/management/\#edit-existing-hooks) Edit Existing Hooks

Hooks evolve with your workflow. Update them anytime by selecting your hook in the Agent Hooks panel and modifying settings like triggers, file patterns, instructions, or descriptions. Updates apply immediately.

### [Copied!](https://kiro.dev/docs/hooks/management/\#delete-hooks) Delete Hooks

Select the hook in the Agent Hooks panel, click `Delete Hook` located at the bottom view, then click `delete`. This action cannot be undone.

### [Copied!](https://kiro.dev/docs/hooks/management/\#run-manual-trigger-hooks) Run Manual Trigger Hooks

You can execute a manual trigger hook using:

- **Quick run**: Click the `play button (▷)` next to the hook name in the Agent Hooks panel
- **From hook view**: Select the hook and click `Start Hook` in the top-right corner

Page updated: July 11, 2025

[Hook Types](https://kiro.dev/docs/hooks/types/)

[Best Practices](https://kiro.dev/docs/hooks/best-practices/)

* * *

# Best Practices
## Select your cookie preferences

We use essential cookies and similar tools that are necessary to provide our site and services. We use performance cookies to collect anonymous statistics, so we can understand how customers use our site and make improvements. Essential cookies cannot be deactivated, but you can choose “Customize” or “Decline” to decline performance cookies.

If you agree, AWS and approved third parties will also use cookies to provide useful site features, remember your preferences, and display relevant content, including relevant advertising. To accept or decline all non-essential cookies, choose “Accept” or “Decline.” To make more detailed choices, choose “Customize.”

AcceptDeclineCustomize

## Customize cookie preferences

We use cookies and similar tools (collectively, "cookies") for the following purposes.

### Essential

Essential cookies are necessary to provide our site and services and cannot be deactivated. They are usually set in response to your actions on the site, such as setting your privacy preferences, signing in, or filling in forms.

### Performance

Performance cookies provide anonymous statistics about how customers navigate our site so we can improve site experience and performance. Approved third parties may perform analytics on our behalf, but they cannot use the data for their own purposes.

Allowed

### Functional

Functional cookies help us provide useful site features, remember your preferences, and display relevant content. Approved third parties may set these cookies to provide certain site features. If you do not allow these cookies, then some or all of these services may not function properly.

Allowed

### Advertising

Advertising cookies may be set through our site by us or our advertising partners and help us deliver relevant marketing content. If you do not allow these cookies, you will experience less relevant advertising.

Allowed

Blocking some types of cookies may impact your experience of our sites. You may review and change your choices at any time by selecting Cookie preferences in the footer of this site. We and selected third-parties use cookies or similar technologies as specified in the [AWS Cookie Notice](https://aws.amazon.com/legal/cookies/).

CancelSave preferences

## Unable to save cookie preferences

We will only store essential cookies at this time, because we were unable to save your cookie preferences.

If you want to change your cookie preferences, try again later using the link in the AWS console footer, or contact support if the problem persists.

Dismiss

Documentation

# Best Practices

Following these best practices will help you create reliable, efficient, and maintainable hooks that enhance your development workflow.

## [Copied!](https://kiro.dev/docs/hooks/best-practices/\#hook-design) Hook Design

### [Copied!](https://kiro.dev/docs/hooks/best-practices/\#be-specific-and-clear) Be Specific and Clear

- Write detailed, unambiguous instructions
- Focus on one specific task per hook
- Use numbered steps for complex operations

### [Copied!](https://kiro.dev/docs/hooks/best-practices/\#test-thoroughly) Test Thoroughly

- Test hooks on sample files before deploying
- Verify hooks work with edge cases
- Start with limited file patterns before expanding

### [Copied!](https://kiro.dev/docs/hooks/best-practices/\#monitor-performance) Monitor Performance

- Ensure hooks don't slow down your workflow
- Consider the frequency of trigger events
- Optimize prompts for efficiency

## [Copied!](https://kiro.dev/docs/hooks/best-practices/\#security-considerations) Security Considerations

### [Copied!](https://kiro.dev/docs/hooks/best-practices/\#validate-inputs) Validate Inputs

- Ensure hooks handle unexpected file content gracefully
- Consider potential edge cases in file formats
- Test with malformed or unexpected input

### [Copied!](https://kiro.dev/docs/hooks/best-practices/\#limit-scope) Limit Scope

- Target specific file types or directories when possible
- Use precise file patterns to avoid unnecessary executions
- Consider the impact of hooks on your entire codebase

### [Copied!](https://kiro.dev/docs/hooks/best-practices/\#review-regularly) Review Regularly

- Update hook logic as your project evolves
- Remove hooks that are no longer needed
- Refine prompts based on actual results

## [Copied!](https://kiro.dev/docs/hooks/best-practices/\#team-collaboration) Team Collaboration

### [Copied!](https://kiro.dev/docs/hooks/best-practices/\#document-hooks) Document Hooks

- Maintain clear documentation of hook purposes
- Include examples of expected behavior
- Document any limitations or edge cases

### [Copied!](https://kiro.dev/docs/hooks/best-practices/\#share-configurations) Share Configurations

- Use consistent hooks across team members
- Store hook configurations in version control
- Create standard hooks for common team workflows

### [Copied!](https://kiro.dev/docs/hooks/best-practices/\#version-control-integration) Version Control Integration

- Consider hooks that integrate with your version control system
- Create hooks for code review workflows
- Use hooks to enforce team standards

Page updated: July 11, 2025

[Management](https://kiro.dev/docs/hooks/management/)

[Examples](https://kiro.dev/docs/hooks/examples/)

* * *

# Examples
## Select your cookie preferences

We use essential cookies and similar tools that are necessary to provide our site and services. We use performance cookies to collect anonymous statistics, so we can understand how customers use our site and make improvements. Essential cookies cannot be deactivated, but you can choose “Customize” or “Decline” to decline performance cookies.

If you agree, AWS and approved third parties will also use cookies to provide useful site features, remember your preferences, and display relevant content, including relevant advertising. To accept or decline all non-essential cookies, choose “Accept” or “Decline.” To make more detailed choices, choose “Customize.”

AcceptDeclineCustomize

## Customize cookie preferences

We use cookies and similar tools (collectively, "cookies") for the following purposes.

### Essential

Essential cookies are necessary to provide our site and services and cannot be deactivated. They are usually set in response to your actions on the site, such as setting your privacy preferences, signing in, or filling in forms.

### Performance

Performance cookies provide anonymous statistics about how customers navigate our site so we can improve site experience and performance. Approved third parties may perform analytics on our behalf, but they cannot use the data for their own purposes.

Allowed

### Functional

Functional cookies help us provide useful site features, remember your preferences, and display relevant content. Approved third parties may set these cookies to provide certain site features. If you do not allow these cookies, then some or all of these services may not function properly.

Allowed

### Advertising

Advertising cookies may be set through our site by us or our advertising partners and help us deliver relevant marketing content. If you do not allow these cookies, you will experience less relevant advertising.

Allowed

Blocking some types of cookies may impact your experience of our sites. You may review and change your choices at any time by selecting Cookie preferences in the footer of this site. We and selected third-parties use cookies or similar technologies as specified in the [AWS Cookie Notice](https://aws.amazon.com/legal/cookies/).

CancelSave preferences

## Unable to save cookie preferences

We will only store essential cookies at this time, because we were unable to save your cookie preferences.

If you want to change your cookie preferences, try again later using the link in the AWS console footer, or contact support if the problem persists.

Dismiss

Documentation

# Hook Examples

These examples demonstrate real-world hook implementations that you can adapt for your own projects. Each example includes the trigger type, target patterns, and complete hook instructions.

## [Copied!](https://kiro.dev/docs/hooks/examples/\#security-pre-commit-scanner) Security Pre-Commit Scanner

This hook helps prevent security leaks by scanning files before they're committed.

**Trigger Type:** File Save

**Target:** `**/*`

**Hook Instructions:**

```code-highlight

Review changed files for potential security issues:
1. Look for API keys, tokens, or credentials in source code
2. Check for private keys or sensitive credentials
3. Scan for encryption keys or certificates
4. Identify authentication tokens or session IDs
5. Flag passwords or secrets in configuration files
6. Detect IP addresses containing sensitive data
7. Find hardcoded internal URLs
8. Spot database connection credentials

For each issue found:
1. Highlight the specific security risk
2. Suggest a secure alternative approach
3. Recommend security best practices

```

## [Copied!](https://kiro.dev/docs/hooks/examples/\#internationalization-helper) Internationalization Helper

This hook ensures that when you update text in your primary language file, translations are kept in sync.

**Trigger Type:** File Save

**Target:** `src/locales/en/*.json`

**Hook Instructions:**

```code-highlight

When an English locale file is updated:
1. Identify which string keys were added or modified
2. Check all other language files for these keys
3. For missing keys, add them with a "NEEDS_TRANSLATION" marker
4. For modified keys, mark them as "NEEDS_REVIEW"
5. Generate a summary of changes needed across all languages

```

## [Copied!](https://kiro.dev/docs/hooks/examples/\#documentation-generator) Documentation Generator

This hook automatically updates documentation when code changes.

**Trigger Type:** Manual Trigger

**Hook Instructions:**

```code-highlight

Generate comprehensive documentation for the current file:
1. Extract function and class signatures
2. Document parameters and return types
3. Provide usage examples based on existing code
4. Update the README.md with any new exports
5. Ensure documentation follows project standards

```

## [Copied!](https://kiro.dev/docs/hooks/examples/\#test-coverage-maintainer) Test Coverage Maintainer

This hook ensures test coverage remains high as code evolves.

**Trigger Type:** File Save

**Target:** `src/**/*.{js,ts,jsx,tsx}`

**Hook Instructions:**

```code-highlight

When a source file is modified:
1. Identify new or modified functions and methods
2. Check if corresponding tests exist and cover the changes
3. If coverage is missing, generate test cases for the new code
4. Run the tests to verify they pass
5. Update coverage reports

```

## [Copied!](https://kiro.dev/docs/hooks/examples/\#integration-with-mcp) Integration with MCP

Agent Hooks can be enhanced with Model Context Protocol (MCP) capabilities to extend their functionality:

1. **Access to External Tools**: Hooks can leverage MCP servers to access specialized tools and APIs
2. **Enhanced Context**: MCP provides additional context for more intelligent hook actions
3. **Domain-Specific Knowledge**: Specialized MCP servers can provide domain expertise

To use MCP with hooks:

1. [Configure MCP servers](https://kiro.dev/docs/mcp/configuration)
2. Reference MCP tools in your hook instructions
3. Set appropriate auto-approval settings for frequently used tools

**Use Cases:**

- Make sure that your Figma design system is respected
- Update ticket status after a task is done
- Sync a database from sample files within the project folder

### [Copied!](https://kiro.dev/docs/hooks/examples/\#example-validate-figma-design) Example: Validate Figma Design

This hook monitors HTML and CSS files and validates that they follow a Figma design using the Figma MCP.

**Trigger Type:** File Save Hook

**Target:** `*.css` `*.html`

**Hook Instructions:**

```code-highlight

Use the Figma MCP to analyze the updated html or css files and check that they follow
established design patterns in the figma design. Verify elements like hero sections,
feature highlights, navigation elements, colors, and button placements align.

```

Page updated: July 11, 2025

[Best Practices](https://kiro.dev/docs/hooks/best-practices/)

[Troubleshooting](https://kiro.dev/docs/hooks/troubleshooting/)

* * *
# Troubleshooting
## Select your cookie preferences

We use essential cookies and similar tools that are necessary to provide our site and services. We use performance cookies to collect anonymous statistics, so we can understand how customers use our site and make improvements. Essential cookies cannot be deactivated, but you can choose “Customize” or “Decline” to decline performance cookies.

If you agree, AWS and approved third parties will also use cookies to provide useful site features, remember your preferences, and display relevant content, including relevant advertising. To accept or decline all non-essential cookies, choose “Accept” or “Decline.” To make more detailed choices, choose “Customize.”

AcceptDeclineCustomize

## Customize cookie preferences

We use cookies and similar tools (collectively, "cookies") for the following purposes.

### Essential

Essential cookies are necessary to provide our site and services and cannot be deactivated. They are usually set in response to your actions on the site, such as setting your privacy preferences, signing in, or filling in forms.

### Performance

Performance cookies provide anonymous statistics about how customers navigate our site so we can improve site experience and performance. Approved third parties may perform analytics on our behalf, but they cannot use the data for their own purposes.

Allowed

### Functional

Functional cookies help us provide useful site features, remember your preferences, and display relevant content. Approved third parties may set these cookies to provide certain site features. If you do not allow these cookies, then some or all of these services may not function properly.

Allowed

### Advertising

Advertising cookies may be set through our site by us or our advertising partners and help us deliver relevant marketing content. If you do not allow these cookies, you will experience less relevant advertising.

Allowed

Blocking some types of cookies may impact your experience of our sites. You may review and change your choices at any time by selecting Cookie preferences in the footer of this site. We and selected third-parties use cookies or similar technologies as specified in the [AWS Cookie Notice](https://aws.amazon.com/legal/cookies/).

CancelSave preferences

## Unable to save cookie preferences

We will only store essential cookies at this time, because we were unable to save your cookie preferences.

If you want to change your cookie preferences, try again later using the link in the AWS console footer, or contact support if the problem persists.

Dismiss

Documentation

# Troubleshooting Hooks

### [Copied!](https://kiro.dev/docs/hooks/troubleshooting/\#common-issues) Common Issues

**Hook Not Triggering**

- Verify the file pattern matches your target files
- Check that the hook is enabled
- Ensure the event type is correct

**Unexpected Hook Behavior**

- Review the hook instructions for clarity
- Check for conflicting hooks
- Verify file patterns aren't too broad

**Performance Issues**

- Limit hook scope with more specific file patterns
- Simplify complex hook instructions
- Reduce the frequency of triggering events

For additional information, consult the [troubleshooting guide](https://kiro.dev/docs/reference/troubleshooting)

Page updated: July 11, 2025

[Examples](https://kiro.dev/docs/hooks/examples/)

[Steering](https://kiro.dev/docs/steering/)

* * *