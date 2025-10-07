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
name: AI Code Review
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  ai-review:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    
    steps:
      - name: Checkout PR
        uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Get full history for diff
          
      - name: Setup environment
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          
      - name: Install dependencies
        run: |
          npm init -y
          npm install @anthropic-ai/sdk @octokit/rest
          
      - name: Run AI Analysis
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          PR_NUMBER: ${{ github.event.pull_request.number }}
          BASE_SHA: ${{ github.event.pull_request.base.sha }}
          HEAD_SHA: ${{ github.event.pull_request.head.sha }}
        run: |
          cat > review.js << 'EOF'
          const { Anthropic } = require('@anthropic-ai/sdk');
          const { Octokit } = require('@octokit/rest');
          const { execSync } = require('child_process');

          async function reviewPR() {
            const anthropic = new Anthropic({ apiKey: process.env.ANTHROPIC_API_KEY });
            const octokit = new Octokit({ auth: process.env.GITHUB_TOKEN });
            
            // Get diff
            const diff = execSync(`git diff ${process.env.BASE_SHA}..${process.env.HEAD_SHA}`).toString();
            
            const response = await anthropic.messages.create({
              model: 'claude-3-sonnet-20240229',
              max_tokens: 2000,
              messages: [{
                role: 'user',
                content: `Review this code diff and provide constructive feedback:\n\n${diff}`
              }]
            });
            
            const review = response.content[0].text;
            
            await octokit.rest.issues.createComment({
              owner: '${{ github.repository_owner }}',
              repo: '${{ github.event.repository.name }}',
              issue_number: process.env.PR_NUMBER,
              body: `## 🤖 AI Code Review\n\n${review}\n\n---\n*Powered by Claude*`
            });
          }
          
          reviewPR().catch(console.error);
          EOF
          
          node review.js
```

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
  issue_comment:
    types: [created]

jobs:
  classify-issue:
    if: github.event.action == 'opened'
    runs-on: ubuntu-latest
    permissions:
      issues: write
    
    steps:
      - name: Classify Issue
        uses: actions/github-script@v7
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        with:
          script: |
            const { Anthropic } = require('@anthropic-ai/sdk');
            
            const anthropic = new Anthropic({ 
              apiKey: process.env.ANTHROPIC_API_KEY 
            });
            
            const issue = context.payload.issue;
            
            const response = await anthropic.messages.create({
              model: 'claude-3-sonnet-20240229',
              max_tokens: 500,
              messages: [{
                role: 'user',
                content: `Classify this GitHub issue:
                
                Title: ${issue.title}
                Body: ${issue.body}
                
                Return only the classification: bug, feature, question, documentation, or enhancement`
              }]
            });
            
            const classification = response.content[0].text.trim().toLowerCase();
            const labels = [];
            
            switch(classification) {
              case 'bug':
                labels.push('bug', 'needs-investigation');
                break;
              case 'feature':
                labels.push('enhancement', 'needs-design');
                break;
              case 'question':
                labels.push('question', 'needs-response');
                break;
              default:
                labels.push('needs-triage');
            }
            
            await github.rest.issues.addLabels({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: issue.number,
              labels: labels
            });
```

---
layout: default
---

# Pattern 5: Dependency Security Scanning 🔒

```yaml
name: Security Scan
on:
  schedule:
    - cron: '0 6 * * 1'  # Weekly Monday 6 AM
  pull_request:
    paths: ['package*.json', 'requirements*.txt', 'Cargo.toml']

jobs:
  security-audit:
    runs-on: ubuntu-latest
    permissions:
      security-events: write
      issues: write
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Run Security Audit
        run: |
          # Node.js audit
          if [ -f "package.json" ]; then
            npm audit --audit-level moderate --json > npm-audit.json || true
          fi
          
          # Python audit  
          if [ -f "requirements.txt" ]; then
            pip install safety
            safety check --json > safety-audit.json || true
          fi
          
      - name: Process Security Results
        uses: actions/github-script@v7
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        with:
          script: |
            const fs = require('fs');
            
            let findings = [];
            
            // Process npm audit
            if (fs.existsSync('npm-audit.json')) {
              const npmAudit = JSON.parse(fs.readFileSync('npm-audit.json'));
              if (npmAudit.vulnerabilities) {
                findings = findings.concat(
                  Object.entries(npmAudit.vulnerabilities)
                    .filter(([_, vuln]) => vuln.severity === 'high' || vuln.severity === 'critical')
                    .map(([name, vuln]) => `${name}: ${vuln.title}`)
                );
              }
            }
            
            if (findings.length > 0) {
              const issueBody = `## 🚨 Security Vulnerabilities Found
              
              ${findings.map(finding => `- ${finding}`).join('\n')}
              
              Please review and update dependencies.
              
              *Automated security scan*`;
              
              await github.rest.issues.create({
                owner: context.repo.owner,
                repo: context.repo.repo,
                title: 'Security vulnerabilities detected',
                body: issueBody,
                labels: ['security', 'critical']
              });
            }
```

---
layout: default
---

# Advanced: Dynamic Workflow Generation 🎛️

```yaml
name: Dynamic Workflow Generator
on:
  workflow_dispatch:
    inputs:
      project_type:
        type: choice
        options: [nodejs, python, rust, go]
        description: 'Project type'
      features:
        type: string
        description: 'Comma-separated features (testing,linting,security)'

jobs:
  generate-workflow:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Generate Custom Workflow
        env:
          PROJECT_TYPE: ${{ github.event.inputs.project_type }}
          FEATURES: ${{ github.event.inputs.features }}
        run: |
          cat > .github/workflows/generated-pipeline.yml << EOF
          name: Generated Pipeline for $PROJECT_TYPE
          on: [push, pull_request]
          
          jobs:
          EOF
          
          # Add jobs based on project type and features
          if [[ "$PROJECT_TYPE" == "nodejs" ]]; then
            cat >> .github/workflows/generated-pipeline.yml << 'EOF'
            build:
              runs-on: ubuntu-latest
              steps:
                - uses: actions/checkout@v4
                - uses: actions/setup-node@v4
                  with:
                    node-version: '20'
                - run: npm ci
                - run: npm run build
          EOF
          fi
          
          if [[ "$FEATURES" == *"testing"* ]]; then
            cat >> .github/workflows/generated-pipeline.yml << 'EOF'
            test:
              runs-on: ubuntu-latest
              steps:
                - uses: actions/checkout@v4
                - run: npm test
          EOF
          fi
          
          git add .github/workflows/generated-pipeline.yml
          git commit -m "Generated workflow for $PROJECT_TYPE with features: $FEATURES"
          git push
```

---
layout: default
---

# Debugging Workflows 🐛

## 1. Debug Logging
```yaml
steps:
  - name: Debug Environment
    run: |
      echo "Runner OS: ${{ runner.os }}"
      echo "GitHub Event: ${{ github.event_name }}"
      echo "Ref: ${{ github.ref }}"
      env
    
  - name: Debug with Step Output
    id: debug
    run: |
      echo "debug-info=This is debug information" >> $GITHUB_OUTPUT
      echo "::debug::Debug message"
      echo "::warning::Warning message"
      echo "::error::Error message"
      
  - name: Use Debug Output
    run: echo "Debug info: ${{ steps.debug.outputs.debug-info }}"
```

## 2. Matrix Debugging
```yaml
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest]
    node: [18, 20]
  fail-fast: false  # Continue other jobs if one fails

steps:
  - name: Debug Matrix
    run: |
      echo "OS: ${{ matrix.os }}"
      echo "Node: ${{ matrix.node }}"
```

## 3. Conditional Steps
```yaml
steps:
  - name: Debug on Failure
    if: failure()
    run: |
      echo "Previous steps failed"
      ls -la
      cat logs/*.log || true
```

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
