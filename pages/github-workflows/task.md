---
layout: cover
class: text-center
---

# 🎯 Workshop: Prepare GitHub Workflows

Your first agentic workflow for the workshop tasks

---
layout: default
---

# Workshop Goal: Prepare GitHub Workflows 🚀

## Objective
Build the foundation for all 5 workshop tasks by creating a smart GitHub Actions workflow that can:

1. **Analyze GitHub Issues** (AI-powered classification)
2. **Auto-implement solutions** (Code generation pipeline)  
3. **Review Pull Requests** (Intelligent code review)
4. **Self-heal CI pipeline** (Auto-recovery mechanisms)

<div class="mt-8 grid grid-cols-4 gap-4">
<div class="p-4 bg-blue-100 rounded-lg text-center">
<strong>⏱️ Time</strong><br>90 minutes
</div>
<div class="p-4 bg-green-100 rounded-lg text-center">
<strong>🎯 Level</strong><br>Intermediate
</div>
<div class="p-4 bg-purple-100 rounded-lg text-center">
<strong>🛠️ Tools</strong><br>GitHub, Claude/OpenAI
</div>
<div class="p-4 bg-orange-100 rounded-lg text-center">
<strong>🏆 Output</strong><br>Complete Agent System
</div>
</div>

---
layout: default
---

# Phase 1: Repository Setup (15 min)

## 1. Create Workshop Repository
```bash
# Create new repository
gh repo create agentic-workflows-workshop --public --clone

cd agentic-workflows-workshop

# Initialize with basic structure
mkdir -p .github/workflows .github/scripts src tests docs
echo "# Agentic Workflows Workshop" > README.md

# Create sample code for agents to work with
cat > src/calculator.js << 'EOF'
function add(a, b) {
    return a + b;
}

function multiply(a, b) {
    return a * b;
}

module.exports = { add, multiply };
EOF
```

## 2. Configure Repository Settings
- Enable Issues, Pull Requests, Actions
- Add branch protection rules for `main`
- Set up required status checks
- Configure merge options (squash merge)

---
layout: default
---

# Phase 2: Core Agent Workflow (30 min)

## Master Workflow: Agentic Orchestrator

```yaml
# .github/workflows/agentic-orchestrator.yml
name: Agentic Development Orchestrator

on:
  issues:
    types: [opened, labeled, assigned]
  pull_request:
    types: [opened, synchronize, reopened, closed]
  push:
    branches: [main]
  workflow_dispatch:
    inputs:
      operation:
        type: choice
        options: [analyze-issues, auto-implement, review-pr, heal-pipeline]
        description: 'Agent operation to perform'

env:
  OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
  ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}

jobs:
  orchestrator:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      issues: write
      pull-requests: write
      actions: write
    
    outputs:
      operation-type: ${{ steps.determine-operation.outputs.type }}
      confidence: ${{ steps.determine-operation.outputs.confidence }}
      
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4
        with:
          fetch-depth: 0
          token: ${{ secrets.GITHUB_TOKEN }}
          
      - name: Setup Node.js Environment
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
          
      - name: Install Agent Dependencies
        run: |
          npm init -y
          npm install @anthropic-ai/sdk openai @octokit/rest
          
      - name: Determine Operation Type
        id: determine-operation
        run: |
          cat > determine-operation.js << 'EOF'
          const eventName = '${{ github.event_name }}';
          const action = '${{ github.event.action }}' || '';
          const manualOperation = '${{ github.event.inputs.operation }}' || '';
          
          let operation = 'none';
          let confidence = 1.0;
          
          if (manualOperation) {
            operation = manualOperation;
          } else if (eventName === 'issues' && action === 'opened') {
            operation = 'analyze-issues';
          } else if (eventName === 'pull_request' && action === 'opened') {
            operation = 'review-pr';
          } else if (eventName === 'push' && '${{ github.ref }}' === 'refs/heads/main') {
            operation = 'heal-pipeline';
            confidence = 0.7;
          }
          
          console.log(`type=${operation}`);
          console.log(`confidence=${confidence}`);
          EOF
          
          node determine-operation.js >> $GITHUB_OUTPUT
          
  # Task 1: Analyze Issues
  analyze-issues:
    needs: orchestrator
    if: ${{ needs.orchestrator.outputs.operation-type == 'analyze-issues' }}
    runs-on: ubuntu-latest
    permissions:
      issues: write
    steps:
      - uses: actions/checkout@v4
      - name: AI Issue Analysis
        run: |
          echo "🔍 Analyzing issue: ${{ github.event.issue.title }}"
          # Implementation comes in Task 1
          
  # Task 2: Auto-implement Solutions  
  auto-implement:
    needs: orchestrator
    if: ${{ needs.orchestrator.outputs.operation-type == 'auto-implement' }}
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
    steps:
      - uses: actions/checkout@v4
      - name: AI Code Generation
        run: |
          echo "⚡ Auto-implementing solution..."
          # Implementation comes in Task 2
          
  # Task 3: Review Pull Requests
  review-pr:
    needs: orchestrator  
    if: ${{ needs.orchestrator.outputs.operation-type == 'review-pr' }}
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
    steps:
      - uses: actions/checkout@v4
      - name: AI Code Review
        run: |
          echo "👀 Reviewing PR: ${{ github.event.pull_request.title }}"
          # Implementation comes in Task 3
          
  # Task 4: Self-healing Pipeline
  heal-pipeline:
    needs: orchestrator
    if: ${{ needs.orchestrator.outputs.operation-type == 'heal-pipeline' }}
    runs-on: ubuntu-latest
    permissions:
      contents: write
      actions: write
    steps:
      - uses: actions/checkout@v4
      - name: Pipeline Health Check
        run: |
          echo "🏥 Checking pipeline health..."
          # Implementation comes in Task 4
```

---
layout: default
---

# Phase 3: Agent Foundation Scripts (25 min)

## Base Agent Class

```javascript
// .github/scripts/base-agent.js
const { Anthropic } = require('@anthropic-ai/sdk');
const { OpenAI } = require('openai');
const { Octokit } = require('@octokit/rest');

class BaseAgent {
  constructor() {
    this.anthropic = new Anthropic({ apiKey: process.env.ANTHROPIC_API_KEY });
    this.openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });
    this.octokit = new Octokit({ auth: process.env.GITHUB_TOKEN });
    
    this.context = {
      repo: process.env.GITHUB_REPOSITORY.split('/'),
      event: process.env.GITHUB_EVENT_NAME,
      sha: process.env.GITHUB_SHA
    };
  }
  
  async analyzeWithClaude(prompt, maxTokens = 1000) {
    try {
      const response = await this.anthropic.messages.create({
        model: 'claude-3-sonnet-20240229',
        max_tokens: maxTokens,
        messages: [{ role: 'user', content: prompt }]
      });
      return response.content[0].text;
    } catch (error) {
      console.error('Claude API error:', error);
      throw error;
    }
  }
  
  async analyzeWithGPT(prompt, model = 'gpt-4') {
    try {
      const response = await this.openai.chat.completions.create({
        model,
        messages: [{ role: 'user', content: prompt }],
        max_tokens: 1000
      });
      return response.choices[0].message.content;
    } catch (error) {
      console.error('OpenAI API error:', error);
      throw error;
    }
  }
  
  async getFileContent(path) {
    try {
      const { data } = await this.octokit.rest.repos.getContent({
        owner: this.context.repo[0],
        repo: this.context.repo[1],
        path,
        ref: this.context.sha
      });
      return Buffer.from(data.content, 'base64').toString('utf8');
    } catch (error) {
      return null;
    }
  }
  
  async createIssueComment(issueNumber, body) {
    return await this.octokit.rest.issues.createComment({
      owner: this.context.repo[0],
      repo: this.context.repo[1],
      issue_number: issueNumber,
      body
    });
  }
  
  async createPR(title, body, head, base = 'main') {
    return await this.octokit.rest.pulls.create({
      owner: this.context.repo[0],
      repo: this.context.repo[1],
      title,
      body,
      head,
      base
    });
  }
  
  log(message, data = null) {
    console.log(`[${new Date().toISOString()}] ${message}`);
    if (data) console.log(JSON.stringify(data, null, 2));
  }
}

module.exports = BaseAgent;
```

---
layout: default
---

# Phase 4: Issue Analysis Agent (Task 1 Foundation)

```javascript
// .github/scripts/issue-analyzer-agent.js
const BaseAgent = require('./base-agent');

class IssueAnalyzerAgent extends BaseAgent {
  async analyze(issue) {
    this.log(`Analyzing issue: ${issue.title}`);
    
    const prompt = `You are an expert software engineer analyzing a GitHub issue.

Issue Details:
Title: ${issue.title}
Body: ${issue.body || 'No description provided'}
Labels: ${issue.labels?.map(l => l.name).join(', ') || 'None'}
Author: ${issue.user.login}

Repository Context:
${await this.getRepositoryContext()}

Tasks:
1. Classify the issue type (bug, feature, question, documentation, etc.)
2. Assess complexity (1-10 scale)
3. Estimate effort (hours)
4. Identify affected components
5. Suggest appropriate labels
6. Recommend assignment strategy
7. Determine if auto-implementation is feasible

Return response as JSON with these fields:
{
  "classification": "bug|feature|question|documentation|enhancement",
  "complexity": 1-10,
  "effort_hours": number,
  "affected_components": ["component1", "component2"],
  "recommended_labels": ["label1", "label2"],
  "auto_implementable": boolean,
  "confidence": 0.0-1.0,
  "reasoning": "explanation of analysis"
}`;

    const analysis = await this.analyzeWithClaude(prompt, 1500);
    
    try {
      const parsedAnalysis = JSON.parse(analysis);
      await this.applyAnalysis(issue, parsedAnalysis);
      return parsedAnalysis;
    } catch (error) {
      this.log('Failed to parse analysis JSON', error);
      throw error;
    }
  }
  
  async getRepositoryContext() {
    const packageJson = await this.getFileContent('package.json');
    const readme = await this.getFileContent('README.md');
    
    let context = 'Repository: JavaScript/Node.js project\n';
    
    if (packageJson) {
      const pkg = JSON.parse(packageJson);
      context += `Dependencies: ${Object.keys(pkg.dependencies || {}).slice(0, 10).join(', ')}\n`;
    }
    
    if (readme) {
      context += `README excerpt: ${readme.slice(0, 500)}...\n`;
    }
    
    return context;
  }
  
  async applyAnalysis(issue, analysis) {
    // Add labels
    if (analysis.recommended_labels?.length > 0) {
      await this.octokit.rest.issues.addLabels({
        owner: this.context.repo[0],
        repo: this.context.repo[1],
        issue_number: issue.number,
        labels: analysis.recommended_labels
      });
    }
    
    // Add analysis comment
    const comment = `## 🤖 AI Issue Analysis

**Classification:** ${analysis.classification}
**Complexity:** ${analysis.complexity}/10  
**Estimated Effort:** ${analysis.effort_hours} hours
**Affected Components:** ${analysis.affected_components?.join(', ') || 'Unknown'}

**Analysis:** ${analysis.reasoning}

**Auto-implementation feasible:** ${analysis.auto_implementable ? '✅ Yes' : '❌ No'}
**Confidence:** ${Math.round(analysis.confidence * 100)}%

---
*Automated analysis by Agentic Workflow System*`;

    await this.createIssueComment(issue.number, comment);
  }
}

module.exports = IssueAnalyzerAgent;
```

---
layout: default
---

# Phase 5: Integration Script (15 min)

```javascript
// .github/scripts/run-agent.js
const IssueAnalyzerAgent = require('./issue-analyzer-agent');

async function main() {
  const eventName = process.env.GITHUB_EVENT_NAME;
  const eventPath = process.env.GITHUB_EVENT_PATH;
  
  if (!eventPath) {
    console.error('No event data available');
    process.exit(1);
  }
  
  const eventData = JSON.parse(require('fs').readFileSync(eventPath, 'utf8'));
  
  switch(eventName) {
    case 'issues':
      if (eventData.action === 'opened') {
        const agent = new IssueAnalyzerAgent();
        await agent.analyze(eventData.issue);
      }
      break;
      
    case 'pull_request':
      if (eventData.action === 'opened') {
        console.log('PR review agent coming in Task 3...');
      }
      break;
      
    default:
      console.log(`Event ${eventName} not handled yet`);
  }
}

main().catch(error => {
  console.error('Agent execution failed:', error);
  process.exit(1);
});
```

---
layout: default
---

# Phase 6: Testing & Validation (5 min)

## Test Your Workflow

1. **Create Test Issue**
```bash
gh issue create --title "Bug: Calculator division by zero" \
  --body "The calculator crashes when dividing by zero. We need to handle this edge case properly."
```

2. **Check Workflow Execution**
```bash
# Monitor workflow runs
gh run list --workflow="agentic-orchestrator.yml"

# View logs
gh run view --log
```

3. **Validate Agent Response**
- Check if issue got labeled automatically
- Verify AI analysis comment was posted
- Confirm classification accuracy

## Success Criteria ✅
- [ ] Workflow triggers on issue creation
- [ ] Agent analyzes issue content
- [ ] Appropriate labels are applied
- [ ] Analysis comment is posted
- [ ] Classification seems reasonable
- [ ] No workflow errors in logs

---
layout: fact
---

# 🎉 Foundation Complete!

<div class="text-xl">

✅ **Orchestrator Workflow** - Central command system  
✅ **Base Agent Class** - Reusable AI foundation  
✅ **Issue Analyzer** - Task 1 preparation complete  
✅ **Integration Scripts** - Event handling ready  
✅ **Testing Framework** - Validation process working  

</div>

<div class="text-center mt-8 p-4 bg-green-100 rounded-lg">
<strong>Ready for Tasks 2-5:</strong> Auto-implement, Review PRs, Self-heal Pipeline!
</div>

---
layout: default
---

# Next Steps: Workshop Tasks Roadmap 🗺️

## Task 2: Auto-implement GitHub Issues
- Extend base agent with code generation
- Create branch, implement solution, submit PR
- Handle different issue types (bug fix, feature, docs)

## Task 3: Review GitHub Pull Requests  
- Analyze code changes with AI
- Provide constructive feedback
- Auto-approve low-risk changes

## Task 4: Self-healing CI Pipeline
- Monitor workflow failures
- Analyze failure patterns  
- Implement automatic fixes

## Task 5: Advanced Orchestration
- Multi-agent coordination
- Learning from feedback
- Performance optimization

---
layout: center
class: text-center
---

# 🚀 Workshop Foundation Complete!

## Your agentic development platform is ready

### Time to build the 5 workshop tasks on this foundation

<div class="text-sm mt-8 opacity-75">
The future of autonomous development starts here
</div>
