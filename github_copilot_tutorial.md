# How To Github Copilot

**What is Github Copilot:**

- AI-powered coding assistant integrated into VS Code
- Provides code suggestions, explanations, and automated editing
- Uses large language models to understand context and generate relevant code
- Supports multiple modes: Chat (Ask), Edit, Plan, and Agent for different workflows

## MODES

### Ask

**Description:**

- Opens a chat interface to ask questions about code, concepts, or get help
- Best for learning, understanding code, getting explanations, or brainstorming
- Does not modify files directly - provides answers in chat

```markdown
How does the authentication flow work in this project?
What's the difference between useEffect and useLayoutEffect?
Suggest a database schema for a blog application
```

### Edit

**Description:**

- Directly modifies selected code or files based on your instructions
- Best for refactoring, adding features to existing code, or quick fixes
- Select code first, then describe what changes you want

```markdown
Add error handling to this function
Convert this class component to a functional component with hooks
Refactor this to use async/await instead of promises
```

### Plan

**Description:**

- Creates a step-by-step implementation plan before executing
- Best for complex features requiring multiple file changes
- Review and approve each step before execution

```markdown
Plan how to add user authentication with JWT to this Express app
Create a plan to implement dark mode across the application
Plan the migration from REST to GraphQL
```

### Agent

**Description:**

- Autonomous mode that can read files, run terminal commands, and make edits
- Best for complex multi-step tasks requiring research and execution
- Can explore codebase, install packages, run tests, and iterate on solutions

```markdown
Fix all failing tests in this project
Add a new API endpoint for user profiles with validation and tests
Set up ESLint and Prettier for this project and fix all linting errors
```

## COMMAND (/)

### /clear

**Description:**

- Clears the current chat history and starts fresh
- Useful when changing topics or when context becomes cluttered

```markdown
/clear
```

### /doc

**Description:**

- Generates documentation for selected code
- Creates JSDoc, docstrings, or inline comments based on language

```markdown
/doc (with function selected)
/doc Add detailed parameter descriptions
```

### /generate

**Description:**

- Generates new code based on your description
- Creates functions, classes, components, or entire files

```markdown
/generate a React hook for handling form validation
/generate an Express middleware for rate limiting
```

### /fix

**Description:**

- Analyzes and fixes errors in selected code or current file
- Addresses linting errors, type errors, and logical issues

```markdown
/fix (with error code selected)
/fix Fix the TypeScript errors in this file
```

### /optimize

**Description:**

- Improves code performance, readability, or efficiency
- Suggests better algorithms, patterns, or refactoring

```markdown
/optimize (with code selected)
/optimize Reduce memory usage in this function
```

### /tests

**Description:**

- Generates unit tests for selected code or file
- Creates test cases with common testing frameworks (Jest, Pytest, etc.)

```markdown
/tests (with function selected)
/tests Generate edge case tests for this validation function
```

### /explain

**Description:**

- Provides detailed explanation of selected code
- Breaks down logic, patterns, and purpose line by line

```markdown
/explain (with code selected)
/explain How does this regex pattern work?
```

### /help

**Description:**

- Shows available commands and how to use them
- Provides tips for better prompts and Copilot usage

```markdown
/help
/help How do I use references?
```

### /list

**Description:**

- Lists available tools, agents, or resources
- Shows what capabilities are available in current context

```markdown
/list tools
/list agents
```

### /delegate

**Description:**

- Spawns a sub-agent to handle complex, isolated tasks
- Useful for research-heavy or multi-step operations

```markdown
/delegate Find all usages of deprecated API and suggest replacements
/delegate Research best practices for implementing OAuth2
```

### /search

**Description:**

- Searches codebase or web for relevant information
- Finds code patterns, definitions, or documentation

```markdown
/search Where is the user authentication implemented?
/search Find all API endpoints that handle payments
```

### /new

**Description:**

- Creates a new project or workspace from scratch
- Sets up project structure, configs, and boilerplate

```markdown
/new Create a React TypeScript project with Vite
/new Set up a FastAPI project with SQLAlchemy
```

### /newNotebook

**Description:**

- Creates a new Jupyter notebook with specified content
- Sets up data analysis, ML experiments, or tutorials

```markdown
/newNotebook Create a notebook for exploratory data analysis
/newNotebook Set up a PyTorch training notebook
```

### /setupTests

**Description:**

- Configures testing framework for your project
- Installs dependencies, creates config files, and sample tests

```markdown
/setupTests Set up Jest for this React project
/setupTests Configure Pytest with coverage for Python
```

### /savePrompt

**Description:**

- Saves current prompt as a reusable template
- Creates custom instructions for repeated use

```markdown
/savePrompt Save this code review prompt as "review-template"
/savePrompt Create a prompt for API endpoint generation
```

## REFERENCE (#)

Use `#` to attach specific context to your prompts:

| Reference | Description | Example |
|-----------|-------------|---------|
| `#file:filename` | Reference a specific file | `#file:auth.ts explain this` |
| `#sym:symbolName` | Reference a function/class/variable | `#sym:handleLogin how is this used?` |
| `#selection` | Reference currently selected code | `#selection refactor this` |
| `#editor` | Reference visible code in editor | `#editor what does this do?` |
| `#terminalLastCommand` | Reference last terminal command | `#terminalLastCommand why did this fail?` |
| `#terminalSelection` | Reference selected terminal output | `#terminalSelection explain this error` |
| `#problems` | Reference current errors/warnings | `#problems fix all issues` |
| `#codebase` | Search entire codebase | `#codebase find payment logic` |
| `#fetch:url` | Fetch content from URL | `#fetch:https://api.example.com/docs` |

```markdown
#file:userController.ts add input validation to all endpoints
#sym:calculateTotal optimize this function for performance
#problems fix all TypeScript errors
```

## MENTIONING (@)

### @cli

**Description:**

- Generates CLI commands for your specific task
- Supports various tools: git, npm, docker, kubectl, etc.

```markdown
@cli How do I rebase the last 3 commits?
@cli Create a docker command to run postgres with volume
```

### @cloud

**Description:**

- Helps with cloud services (Azure, AWS, GCP)
- Generates infrastructure code, deployment configs

```markdown
@cloud How do I deploy this app to Azure App Service?
@cloud Create a Terraform config for AWS Lambda
```

### @github

**Description:**

- Searches GitHub repos, issues, PRs, and code
- Finds examples, solutions, and documentation

```markdown
@github Find examples of implementing rate limiting in Express
@github Search for how NextAuth handles session management
```

### @terminal

**Description:**

- Interacts with terminal output and commands
- Helps debug errors, suggests fixes for failed commands

```markdown
@terminal Why did my last command fail?
@terminal How do I run the dev server?
```

### @vscode

**Description:**

- Helps with VS Code settings, extensions, and features
- Configures editor, keybindings, and workspace settings

```markdown
@vscode How do I enable format on save?
@vscode Recommend extensions for Python development
```

### @workspace

**Description:**

- Provides context about entire workspace/project
- Searches across all files, understands project structure

```markdown
@workspace What database is this project using?
@workspace Find all API endpoints in this project
@workspace How is error handling done in this codebase?
```

---

## TIPS FOR EFFECTIVE PROMPTS

1. **Be specific**: "Add validation for email and password" > "Add validation"
2. **Provide context**: Use `#file:` or `@workspace` to give relevant context
3. **Iterate**: Start broad, then refine with follow-up questions
4. **Use examples**: "Format like the existing UserController" helps maintain consistency
5. **Combine references**: `#file:schema.ts @workspace create a migration for this schema`