# Question
```question
How do you use `git commits` history to build repo wiki, can you explain deeper
```
# Answer

Based on the search results, I can explain how Qoder uses git commit history to build repository wikis:

Qoder's **Repo Wiki** feature uses git commit history as one of its data sources to automatically generate structured documentation for your project. Here's how it works:

## Git Commit Context Integration

Qoder provides a `@gitCommit` context type that allows you to:
- Select specific git commit records for analysis
- Use commit details for troubleshooting, bug fixing, and unit test generation
- Analyze code changes across different commits

## Repo Wiki Generation Process

The Repo Wiki analyzes your project's structure and implementation details, including:
- **Code changes tracking**: Continuously monitors code modifications
- **Git history analysis**: Uses commit history to understand project evolution
- **Contextual insights**: Provides deep understanding of codebase changes over time

## How It Works

1. **Initial Generation**: When first opened, Repo Wiki analyzes the entire project structure and git history
2. **Change Detection**: Monitors code changes and compares them against existing documentation
3. **Git Directory Sync**: Detects mismatches between git content and wiki, allowing synchronization

## Practical Applications

- **Architecture queries**: Answer "How is X implemented?" using historical context
- **Development tasks**: Accelerate code localization for new features and bug fixes
- **Documentation maintenance**: Keep wiki synchronized with actual code changes

The system creates a `.qoder/repowiki` directory in your repository that can be shared with team members via git, ensuring everyone has access to the generated documentation.

Want to learn more about Repo Wiki? These pages may help:
```suggestions
(Repo Wiki)[/user-guide/repo-wiki]
(Context)[/user-guide/chat/context]
```