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
layout: two-cols-header
layoutClass: gap-16
---

# Pattern 2: Scheduled Automation 🔄

::left::

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

::right::

## Proactive Agent Benefits
- **Daily code health checks**
- **Automated security scanning**  
- **Documentation updates**
- **Dependency maintenance**
- **Performance monitoring**

---
layout: two-cols-header
layoutClass: gap-16
---

# Pattern 3: Multi-Stage Workflows 🤝

::left::

```yaml

jobs:
  analyze-requirement:
    if: contains(github.event.issue.labels.*.name, 'feature')
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "Analyze feature request and create implementation plan"
          
  implement-feature:
    needs: analyze-requirement
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "Implement feature with tests and documentation"
```

::right::

**Multi-stage benefits**: Analysis → Planning → Implementation → Review

---
layout: default
---

# Trigger Patterns 🎛️

<div class="grid grid-cols-3 gap-4">
<div class="p-4 bg-blue-50 rounded-lg">

### Issue-Based 
```yaml
on:
  issues:
    types: [opened, labeled]
```

**Triggers**: @claude mentions, labels

**Use for**: Bug reports, feature requests, questions  
**Agent action**: Analyze, classify, plan, potentially auto-implement

</div>
<div class="p-4 bg-green-50 rounded-lg">

### PR-Based
```yaml
on:
  pull_request:
    types: [opened, synchronize]
```

**Triggers**: New PRs, code changes

**Use for**: Code reviews, quality checks, security scans  
**Agent action**: Review code, suggest improvements, approve/block

</div>
<div class="p-4 bg-purple-50 rounded-lg">

### Manual
```yaml
on:
  workflow_dispatch:
    inputs:
      task: [refactor, document, optimize]
```

**Triggers**: On-demand execution

**Use for**: Maintenance, optimization, documentation updates  
**Agent action**: Perform specific tasks based on user selection

</div>
</div>

---
layout: default
---

# Error Handling & Fallbacks 🛡️

```yaml
steps:
  - name: Claude Analysis
    id: claude
    continue-on-error: true
    uses: anthropics/claude-code-action@v1
    with:
      anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
      
  - name: Fallback Notification
    if: failure()
    run: echo "⚠️ Claude failed. Manual review requested."
```

**Key principles**: Graceful failures, fallback notifications, continue-on-error