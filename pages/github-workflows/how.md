---
layout: section
---

# How to Build GitHub Workflows?

Practical patterns and step-by-step implementation

---
layout: default
---

# Workflow Development Process 🔄

<div class="grid grid-cols-4 gap-4">
<div class="p-4 bg-blue-100 rounded-lg text-center">

### 1. Plan
- Define triggers
- Map dependencies
- Set success criteria

</div>
<div class="p-4 bg-green-100 rounded-lg text-center">

### 2. Build
- Write YAML
- Test locally
- Add error handling

</div>
<div class="p-4 bg-yellow-100 rounded-lg text-center">

### 3. Test
- Small iterations
- Debug with logs
- Validate outputs

</div>
<div class="p-4 bg-purple-100 rounded-lg text-center">

### 4. Scale
- Add complexity
- Optimize performance
- Monitor metrics

</div>
</div>

---
layout: default
---

# Pattern 1: Basic CI/CD Workflow 🏗️

```yaml
name: CI/CD Pipeline
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  lint-and-test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
          
      - name: Install dependencies
        run: npm ci
        
      - name: Run linter
        run: npm run lint
        
      - name: Run tests
        run: npm test -- --coverage
        
      - name: Upload coverage
        uses: codecov/codecov-action@v4
        if: success()
        
  build:
    needs: lint-and-test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm run build
      
      - name: Upload build artifacts
        uses: actions/upload-artifact@v4
        with:
          name: dist-files
          path: dist/
```

---
layout: default
---

# Pattern 2: Agentic Code Review Workflow 🤖

```yaml
name: Claude PR Review
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  claude-review:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "/review --security --performance --style"
```

**Key benefits**: No custom code, instant setup, comprehensive analysis

---
layout: two-cols-header
layoutClass: gap-16
---

# Pattern 3: Smart Deployment Pipeline 🚀

::left::

```yaml
name: Smart Deploy
on:
  push:
    branches: [main]
  workflow_dispatch:
    inputs:
      force_deploy:
        type: boolean
        description: 'Force deployment'
        default: false

jobs:
  analyze-changes:
    runs-on: ubuntu-latest
    outputs:
      deploy-type: ${{ steps.analysis.outputs.type }}
      risk-level: ${{ steps.analysis.outputs.risk }}
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 2
          
      - name: Analyze Changes
        id: analysis
        run: |
          # Simple change analysis
          CHANGED_FILES=$(git diff --name-only HEAD~1)
          
          if echo "$CHANGED_FILES" | grep -E "(package\.json|requirements\.txt)"; then
            echo "type=dependency" >> $GITHUB_OUTPUT
            echo "risk=medium" >> $GITHUB_OUTPUT
          elif echo "$CHANGED_FILES" | grep -E "(\.md|\.txt)"; then
            echo "type=documentation" >> $GITHUB_OUTPUT  
            echo "risk=low" >> $GITHUB_OUTPUT
          else
            echo "type=code" >> $GITHUB_OUTPUT
            echo "risk=high" >> $GITHUB_OUTPUT
          fi
```

::right::

```yaml
  deploy-staging:
    needs: analyze-changes
    runs-on: ubuntu-latest
    environment: staging
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to Staging
        run: echo "Deploying to staging..."
        
  integration-tests:
    needs: deploy-staging
    runs-on: ubuntu-latest
    steps:
      - name: Run Integration Tests
        run: |
          # Comprehensive tests for high-risk changes
          if [ "${{ needs.analyze-changes.outputs.risk-level }}" = "high" ]; then
            npm run test:integration:full
          else
            npm run test:integration:smoke
          fi
          
  deploy-production:
    needs: [analyze-changes, integration-tests]
    if: >
      success() && (
        inputs.force_deploy ||
        needs.analyze-changes.outputs.risk-level != 'high'
      )
    runs-on: ubuntu-latest
    environment: production
    steps:
      - name: Production Deployment
        run: echo "Deploying to production..."
```

---
layout: default
---

# Pattern 4: Issue Auto-Management 📋

```yaml
name: Issue Management  
on:
  issues:
    types: [opened, labeled]

jobs:
  classify-issue:
    if: github.event.action == 'opened'
    runs-on: ubuntu-latest
    permissions:
      issues: write
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "Classify issue type and add appropriate labels"
```

**Auto-management**: Classify, label, assign, plan, track progress

---
layout: default
---

# Pattern 5: Security Automation 🔒

```yaml
name: Security Scan
on:
  schedule:
    - cron: '0 6 * * 1'  # Weekly
  pull_request:
    paths: ['package*.json', 'requirements*.txt']

jobs:
  security-audit:
    runs-on: ubuntu-latest
    permissions:
      security-events: write
      issues: write
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "Run security audit and create issues for vulnerabilities"
```

**Security features**: Dependency scanning, vulnerability detection, automated issue creation

---
layout: default
---

# Advanced: Workflow Optimization 🎛️

```yaml
name: Claude Workflow Optimizer
on:
  schedule:
    - cron: '0 3 * * 0'  # Weekly optimization

jobs:
  optimize-workflows:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "Analyze workflow performance and suggest optimizations"
```

**Optimization features**: Performance analysis, cost reduction, efficiency improvements

---
layout: default
---

# Debugging Workflows 🐛

## Essential Debug Patterns

<div class="grid grid-cols-3 gap-4">
<div class="p-4 bg-red-50 rounded-lg">

### Environment Debug
```yaml
- name: Debug Info
  run: echo "OS: ${{ runner.os }}"
```

</div>
<div class="p-4 bg-blue-50 rounded-lg">

### Matrix Testing
```yaml
strategy:
  matrix:
    node: [18, 20, 22]
  fail-fast: false
```

</div>
<div class="p-4 bg-green-50 rounded-lg">

### Conditional Steps
```yaml
- name: On Failure
  if: failure()
  run: echo "Debug info"
```

</div>
</div>

---
layout: fact
---

# 🎯 Workflow Best Practices

<div class="text-xl">

✅ **Start simple** - Begin with basic workflows  
✅ **Fail fast** - Quick feedback on errors  
✅ **Use caching** - Speed up repeated builds  
✅ **Secure secrets** - Never expose credentials  
✅ **Test locally** - Use act or similar tools  
✅ **Monitor costs** - Optimize runner usage  

</div>

<div class="text-center mt-8 text-lg">
Master the basics before adding AI complexity
</div>

---
layout: center
class: text-center
---

# 🚀 Ready for Advanced Scenarios?

## What's possible with GitHub Workflows?

<div class="text-sm mt-8 opacity-75">
From basic automation to agentic orchestration
</div>
