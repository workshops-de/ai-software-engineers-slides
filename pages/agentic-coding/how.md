---
layout: section
---

# How to implement Agentic Coding?

No-code automation with Claude Code GitHub Actions

---
layout: default
---

# The Agentic Development Stack 📚

<div class="grid grid-cols-3 gap-6">
<div>

## 🤖 AI Layer
- **Claude Code**: Advanced reasoning
- **GitHub Actions**: Event triggers
- **@claude mentions**: Simple activation
- **CLAUDE.md**: Behavior config

</div>
<div>

## 🔧 Integration Layer  
- **GitHub API**: Repository access
- **Webhooks**: Real-time events
- **PR/Issue comments**: Communication
- **Workflow dispatch**: Manual triggers

</div>
<div>

## 🏗️ Platform Layer
- **GitHub**: Repository & Actions
- **Claude Code Action**: Pre-built automation
- **Anthropic API**: AI processing
- **No custom code**: Pure configuration

</div>
</div>

---
layout: two-cols-header
layoutClass: gap-16
---

# Pattern 1: Event-Driven Claude

::left::

## Issue → @claude → Solution

```yaml
name: Claude Code
on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]
jobs:
  claude:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          # Responds to @claude mentions automatically
```

**Usage in Issue:**
```
Bug: Login form validation fails

@claude Please analyze and fix this login validation issue
```

::right::

## What Claude Does
- **Analyzes** the issue context
- **Reads** relevant code files
- **Identifies** the root cause
- **Creates** a pull request with fix
- **Tests** the solution
- **Documents** the changes

*All triggered by a simple @claude mention!*

---
layout: default
---

# Pattern 2: Scheduled Automation 🔄

```yaml
name: Daily Code Review
on:
  schedule:
    - cron: "0 9 * * *"
jobs:
  daily-review:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Review yesterday's commits and create a summary report.
            Identify any potential issues or improvements.
            Create an issue with your findings if needed.
          claude_args: "--max-turns 3"
```

## Proactive Agent Benefits
- **Daily code health checks**
- **Automated security scanning**  
- **Documentation updates**
- **Dependency maintenance**
- **Performance monitoring**

---
layout: default
---

# Pattern 3: Multi-Stage Workflows 🤝

```yaml
name: Feature Development Pipeline
on:
  issues:
    types: [opened, labeled]

jobs:
  analyze-requirement:
    if: contains(github.event.issue.labels.*.name, 'feature')
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Analyze this feature request and create:
            1. Technical specification
            2. Implementation plan
            3. Test strategy
            4. Documentation requirements
          claude_args: "--max-turns 5"
          
  implement-feature:
    needs: analyze-requirement
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Implement the feature based on the analysis above.
            Create a complete PR with tests and documentation.
          claude_args: "--max-turns 10"
```

---
layout: two-cols-header
layoutClass: gap-16
---

# Simple Setup: Quick Start

::left::

**Step 1: Install Claude GitHub App**
```bash
# In your Claude Code terminal
/install-github-app
```

**Step 2: Add Repository Secret**
- Go to Repository Settings → Secrets
- Add `ANTHROPIC_API_KEY` with your Claude API key

**Step 3: Create Basic Workflow**
```yaml
# .github/workflows/claude.yml
name: Claude Code
on:
  issue_comment:
    types: [created]
jobs:
  claude:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

::right::

**Step 4: Configure Claude's Behavior**
```markdown
<!-- CLAUDE.md -->
# Claude Configuration

## Coding Standards
- Use TypeScript for all new code
- Follow ESLint configuration
- Write tests for new functions
- Update documentation

## Review Criteria
- Security: Check for vulnerabilities
- Performance: Identify bottlenecks
- Maintainability: Ensure clean code
- Testing: Verify test coverage

## Auto-fix Guidelines
- Fix lint errors automatically
- Update dependencies safely
- Regenerate documentation
- Create meaningful commit messages
```

---
layout: default
---

# Advanced Claude Prompting 🎯

## Effective Workflow Prompts

```yaml
- uses: anthropics/claude-code-action@v1
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    prompt: |
      ROLE: Senior Full-Stack Engineer
      
      CONTEXT: This is a React/Node.js e-commerce application
      
      TASK: Review this PR for:
      1. Security vulnerabilities
      2. Performance implications
      3. Code quality issues
      4. Test coverage gaps
      
      CONSTRAINTS:
      - Follow our CLAUDE.md guidelines
      - Ensure backward compatibility
      - Maintain existing API contracts
      
      OUTPUT: Detailed review comments with specific suggestions
    claude_args: "--max-turns 5 --model claude-3-5-sonnet-20241022"
```

---
layout: default
---

# Trigger Patterns 🎛️

## Issue-Based Automation
```yaml
on:
  issues:
    types: [opened, labeled]
jobs:
  smart-triage:
    if: |
      contains(github.event.issue.body, '@claude') ||
      contains(github.event.issue.labels.*.name, 'auto-fix')
```

## PR-Based Reviews
```yaml
on:
  pull_request:
    types: [opened, synchronize]
jobs:
  claude-review:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "/review --security --performance"
```

## Manual Triggers
```yaml
on:
  workflow_dispatch:
    inputs:
      task:
        type: choice
        options: [refactor, document, optimize, security-audit]
jobs:
  manual-task:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          prompt: "Perform ${{ github.event.inputs.task }} on this codebase"
```

---
layout: default
---

# Error Handling & Fallbacks 🛡️

```yaml
jobs:
  claude-with-fallback:
    runs-on: ubuntu-latest
    steps:
      - name: Try Claude Analysis
        id: claude
        continue-on-error: true
        uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          claude_args: "--max-turns 3"
          
      - name: Fallback to Manual Review
        if: failure()
        uses: actions/github-script@v6
        with:
          script: |
            github.rest.issues.createComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.issue.number,
              body: '⚠️ Claude analysis failed. Manual review requested.'
            })
            
      - name: Success Notification
        if: success()
        run: echo "✅ Claude analysis completed successfully"
```

---
layout: fact
---

# 🎯 Quick Win: First Agent in 5 minutes

<div class="text-xl">

1. **Install Claude App** via `/install-github-app` (2 min)
2. **Add API Key** to repository secrets (1 min)  
3. **Create basic workflow** file (1 min)
4. **Test with @claude** mention in an issue (1 min)

</div>

<div class="mt-8 p-4 bg-blue-100 rounded-lg text-center">
<strong>Result:</strong> Instant AI automation with zero custom code! 
</div>

---
layout: center 
class: text-center
---

# 🚀 Ready for Claude Code Specifics?

## Now let's dive deep into Claude Code features

<div class="text-sm mt-8 opacity-75">
From basic patterns to advanced workflows
</div>