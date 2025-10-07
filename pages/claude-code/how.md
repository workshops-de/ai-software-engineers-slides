---
layout: section
---

# How to use Claude Code?

Practical GitHub Actions automation and prompt patterns

---
layout: default
---

# Claude Code GitHub Actions Setup 🔑

<div class="grid grid-cols-3 gap-6">
<div class="p-4 bg-blue-50 rounded-lg">

### 🚀 Quick Setup
- **Claude CLI**: `/install-github-app`
- **GitHub App**: One-click install
- **API Key**: Auto-configured
- **Workflow**: Generated automatically

**Best for:** Getting started fast

</div>
<div class="p-4 bg-green-50 rounded-lg">

### ⚙️ Manual Setup
- **GitHub App**: https://github.com/apps/claude
- **Repository Secrets**: Manual config
- **Workflow Files**: Custom creation
- **CLAUDE.md**: Behavior customization

**Best for:** Custom configurations

</div>
<div class="p-4 bg-purple-50 rounded-lg">

### 🏢 Enterprise Setup
- **Bedrock/Vertex**: Cloud AI providers
- **Custom Authentication**: OIDC/IAM roles
- **Private Networks**: VPC configurations
- **Compliance**: SOC2/GDPR ready

**Best for:** Large organizations

</div>
</div>

---
layout: two-cols-header
layoutClass: gap-16
---

# Getting Started: Basic Workflow 🚀

::left::

## Installation Steps

```bash
# 1. Open Claude Code terminal
claude

# 2. Install GitHub integration
/install-github-app

# 3. Follow prompts:
# - Select repository
# - Install Claude GitHub App  
# - Configure API key
# - Create workflow file
```

## Generated Workflow
```yaml
# .github/workflows/claude.yml
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
```

::right::

## First Interaction
```markdown
<!-- In any GitHub issue -->
We have a performance problem with the user dashboard.
It takes 5+ seconds to load.

@claude Please analyze the performance bottleneck 
and suggest optimizations
```

**Claude's Response:**
- Analyzes codebase automatically
- Identifies performance issues
- Creates optimized solution
- Submits pull request with fixes
- Includes performance benchmarks

---
layout: default
---

# Effective Claude Prompting 🎯

## 1. Context-Rich Prompts
```markdown
@claude This React component handles user authentication.
The login form should validate email format before API calls.

Current issue: Users can submit invalid emails causing API errors.

Please:
1. Add client-side email validation
2. Show user-friendly error messages  
3. Prevent unnecessary API calls
4. Maintain existing UX flow
```

## 2. Specific Task Instructions
```markdown
@claude Refactor the payment processing function to:
- Use async/await instead of Promise chains
- Add proper error handling with try/catch
- Include TypeScript types for all parameters
- Write unit tests covering success and failure cases
- Update JSDoc documentation
```

---
layout: default
---

# Advanced Workflow Patterns 📋

## Multi-Event Automation
```yaml
name: Comprehensive Claude Workflow
on:
  issues:
    types: [opened, labeled]
  pull_request:
    types: [opened, synchronize, closed]
  schedule:
    - cron: '0 9 * * 1'  # Monday mornings

jobs:
  issue-handler:
    if: github.event_name == 'issues'
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Analyze this issue and:
            1. Classify type (bug/feature/question)
            2. Estimate complexity (1-5)
            3. Add appropriate labels
            4. Create implementation plan if needed

  pr-reviewer:
    if: github.event_name == 'pull_request'
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "/review --security --performance --style"
          
  weekly-maintenance:
    if: github.event_name == 'schedule'
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Weekly codebase health check:
            - Review open issues and PRs
            - Check dependency vulnerabilities
            - Analyze code quality trends
            - Generate maintenance report
```

---
layout: two-cols-header
layoutClass: gap-16
---

# CLAUDE.md Configuration 🔧

::left::

## Project Behavior Setup
```markdown
<!-- CLAUDE.md -->
# Claude Configuration

## Project Context
TypeScript React application with Node.js backend
- Frontend: React 18, TypeScript, Tailwind CSS
- Backend: Express.js, PostgreSQL, Prisma ORM
- Testing: Jest, React Testing Library

## Coding Standards
- Use TypeScript strict mode
- Follow Airbnb ESLint configuration
- Prefer functional components with hooks
- Write comprehensive JSDoc comments

## Review Guidelines
- Security: Check for XSS, SQL injection
- Performance: Identify N+1 queries, memory leaks
- Accessibility: Verify WCAG 2.1 compliance
- Testing: Require 80% code coverage

## Auto-fix Permissions
- Fix ESLint errors automatically
- Update package dependencies (patch only)
- Format code with Prettier
- Generate missing TypeScript types
```

::right::

## Workflow-Specific Prompts
```yaml
# Specialized workflows
- uses: anthropics/claude-code-action@v1
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    prompt: |
      SECURITY AUDIT MODE:
      
      Perform comprehensive security review:
      - Scan for OWASP Top 10 vulnerabilities
      - Check authentication/authorization flows
      - Validate input sanitization
      - Review dependency security
      - Generate security report
      
      Create security fixes as separate PR if issues found.
    claude_args: "--max-turns 10"
```

---
layout: default
---

# Slash Commands & Built-in Features ⚡

## Available Slash Commands
```yaml
# In workflow or @claude mentions:
/review                    # Comprehensive code review
/review --security         # Security-focused review
/review --performance      # Performance analysis
/test                      # Generate test cases
/document                  # Create/update documentation
/refactor                  # Code improvement suggestions
/fix                       # Auto-fix identified issues
```

## Usage Examples
```markdown
<!-- In PR comment -->
@claude /review --security --performance

<!-- In issue -->
@claude /test --coverage=80 --edge-cases

<!-- In issue with specific file -->
@claude /document src/api/user-service.js --format=jsdoc
```

---
layout: default
---

# Error Handling & Fallbacks 🛡️

## Robust Workflow Setup
```yaml
jobs:
  claude-with-fallback:
    runs-on: ubuntu-latest
    steps:
      - name: Primary Claude Analysis
        id: claude
        continue-on-error: true
        uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          claude_args: "--max-turns 5 --timeout 300"
          
      - name: Fallback Notification
        if: steps.claude.outcome == 'failure'
        uses: actions/github-script@v6
        with:
          script: |
            const comment = `⚠️ Claude analysis encountered an issue.
            
            **Possible causes:**
            - API rate limits reached
            - Complex codebase analysis timeout  
            - Network connectivity issues
            
            **Next steps:**
            - Try again in a few minutes
            - Use more specific prompts
            - Contact support if issues persist
            
            Manual review has been requested.`;
            
            github.rest.issues.createComment({
              owner: context.repo.owner,
              repo: context.repo.repo,  
              issue_number: context.issue.number,
              body: comment
            });
            
      - name: Success Metrics
        if: steps.claude.outcome == 'success'
        run: |
          echo "✅ Claude analysis completed successfully"
          echo "Response time: ${{ steps.claude.outputs.duration || 'N/A' }}"
```

---
layout: default
---

# Performance Optimization ⚡

## Efficient Workflow Configuration
```yaml
- uses: anthropics/claude-code-action@v1
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    claude_args: |
      --max-turns 5
      --model claude-3-5-sonnet-20241022  
      --timeout 180
      --allowed-tools read_file,create_file,edit_file
    # Optimize for speed and cost
```

## Cost Management
```yaml
# Only run on important events
on:
  pull_request:
    types: [opened]  # Not on every push
  issues:
    types: [opened]
  schedule:
    - cron: '0 9 * * 1'  # Weekly, not daily

# Use conditional logic
jobs:
  claude:
    if: |
      contains(github.event.issue.body, '@claude') ||
      contains(github.event.pull_request.body, '@claude') ||
      contains(github.event.issue.labels.*.name, 'needs-ai-review')
```

## Performance Tips
- **Specific prompts** reduce processing time
- **Targeted file analysis** vs full codebase scans
- **Appropriate max-turns** limits (3-10 typically)
- **Schedule intensive tasks** during off-hours

---
layout: fact
---

# Claude Code Superpowers

<div class="text-xl">

🧠 **Full Codebase Understanding** - Analyzes entire project context  
⚡ **Instant Activation** - Simple @claude mentions trigger automation  
🎯 **Configurable Behavior** - CLAUDE.md customization  
🔄 **Multi-turn Conversations** - Complex problem solving  
🛡️ **Built-in Security** - GitHub permissions & token management  
📊 **Rich Integrations** - Works with existing tools & workflows  

</div>

<div class="text-center mt-8 text-lg">
Professional AI automation without custom code
</div>

---
layout: center
class: text-center
---

# 🎯 Next: Advanced Scenarios

## What's possible with sophisticated Claude workflows?

<div class="text-sm mt-8 opacity-75">
From basic automation to enterprise-grade systems
</div>