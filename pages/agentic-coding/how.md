---
layout: section
---

# How to implement Agentic Coding?

Practical patterns and first steps

---
layout: default
---

# The Agentic Development Stack 📚

<div class="grid grid-cols-3 gap-6">
<div>

## 🤖 Agent Layer
- **Claude/GPT-4**: Reasoning Engine
- **Cursor**: Code Intelligence
- **GitHub Copilot**: Code Generation
- **Custom Agents**: Specialized Tasks

</div>
<div>

## 🔧 Tool Layer  
- **APIs**: GitHub, Linear, Slack
- **Git**: Version Control
- **CI/CD**: Actions, Workflows
- **Monitoring**: Logs, Metrics

</div>
<div>

## 🏗️ Platform Layer
- **GitHub**: Repository & Actions
- **Docker**: Containerization
- **Cloud**: AWS, Azure, GCP
- **Webhooks**: Event Integration

</div>
</div>

---
layout: two-cols
layoutClass: gap-16
---

# Pattern 1: Event-Driven Agent

## Webhook → Agent → Action

```typescript
// GitHub Webhook Handler
app.post('/webhook/issues', async (req, res) => {
  const issue = req.body.issue;
  
  // Agent analyzes the issue
  const analysis = await issueAgent.analyze({
    title: issue.title,
    body: issue.body,
    labels: issue.labels
  });
  
  // Agent decides on action
  if (analysis.confidence > 0.8) {
    await codeAgent.implementFix(analysis);
  }
});
```

::right::

## Agent Implementation
```typescript
class IssueAgent {
  async analyze(issue) {
    const prompt = `
      Analyze this issue:
      Title: ${issue.title}
      Description: ${issue.body}
      
      Return: type, priority, files_affected
    `;
    
    return await this.llm.complete(prompt);
  }
}
```

---
layout: default
---

# Pattern 2: Pipeline-based Agents 🔄

```typescript
class AgentPipeline {
  constructor(agents) {
    this.agents = agents;
  }
  
  async execute(input) {
    let result = input;
    
    for (const agent of this.agents) {
      result = await agent.process(result);
      
      // Validation after each step
      if (!this.validate(result)) {
        throw new Error(`Agent ${agent.name} failed validation`);
      }
    }
    
    return result;
  }
}

// Usage
const pipeline = new AgentPipeline([
  new AnalysisAgent(),
  new CodeGenAgent(), 
  new TestAgent(),
  new ReviewAgent()
]);

await pipeline.execute(pullRequest);
```

---
layout: default
---

# Pattern 3: Multi-Agent Collaboration 🤝

```typescript
class AgentOrchestrator {
  async handleComplexTask(task) {
    // Parallel agent execution
    const [codeAnalysis, testAnalysis, secAnalysis] = await Promise.all([
      this.codeAgent.analyze(task),
      this.testAgent.analyze(task), 
      this.securityAgent.analyze(task)
    ]);
    
    // Conflict resolution
    const consensus = await this.resolveConflicts([
      codeAnalysis, testAnalysis, secAnalysis
    ]);
    
    // Final implementation
    return await this.implementationAgent.execute(consensus);
  }
  
  async resolveConflicts(analyses) {
    // LLM-based consensus finding
    const prompt = `Given these different analyses: ${JSON.stringify(analyses)}
                   Find the best approach considering all perspectives.`;
    return await this.consensusLLM.complete(prompt);
  }
}
```

---
layout: two-cols
layoutClass: gap-16
---

# Simple Start: GitHub Action Agent 

```yaml
# .github/workflows/agent-review.yml
name: Agent Code Review
on: 
  pull_request:
    types: [opened, synchronize]

jobs:
  agent-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Agent Analysis
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
        run: |
          node scripts/agent-review.js
```

::right::

```javascript
// scripts/agent-review.js
const { OpenAI } = require('openai');
const { Octokit } = require('@octokit/rest');

const openai = new OpenAI();
const octokit = new Octokit({
  auth: process.env.GITHUB_TOKEN
});

async function reviewPR() {
  const diff = await getDiff();
  
  const analysis = await openai.chat.completions.create({
    model: "gpt-4",
    messages: [{
      role: "user", 
      content: `Review this code diff and suggest improvements: ${diff}`
    }]
  });
  
  await postComment(analysis.choices[0].message.content);
}
```

---
layout: default
---

# Agent Prompt Engineering 🎯

## Effective Agent Prompts

```typescript
const AGENT_PROMPT = `
You are a Senior Software Engineer Agent specializing in TypeScript/React.

CONTEXT:
- Repository: ${repo.name}
- Issue: ${issue.title}
- Files changed: ${files.join(', ')}

TASK:
1. Analyze the issue thoroughly
2. Identify root cause
3. Generate minimal fix
4. Ensure no regressions

CONSTRAINTS:
- Follow existing code style
- Maintain backwards compatibility  
- Add appropriate tests
- Update documentation if needed

OUTPUT FORMAT:
{
  "analysis": "Root cause analysis",
  "solution": "Step by step solution",
  "files": [{
    "path": "file path", 
    "content": "new content"
  }],
  "tests": "Test cases to add"
}
`;
```

---
layout: default
---

# Agent State Management 💾

```typescript
class AgentStateManager {
  constructor() {
    this.state = {
      currentTasks: new Map(),
      completedTasks: [],
      knowledgeBase: {},
      learnings: []
    };
  }
  
  async saveContext(agentId, context) {
    this.state.currentTasks.set(agentId, {
      ...context,
      timestamp: Date.now(),
      status: 'in-progress'
    });
    
    // Persist to database/file
    await this.persist();
  }
  
  async learnFromOutcome(task, outcome) {
    const learning = {
      pattern: this.extractPattern(task),
      success: outcome.success,
      feedback: outcome.feedback,
      improvements: outcome.suggestions
    };
    
    this.state.learnings.push(learning);
  }
}
```

---
layout: default
---

# Error Handling & Fallbacks 🛡️

```typescript
class RobustAgent {
  async executeWithFallbacks(task) {
    const strategies = [
      () => this.primaryStrategy(task),
      () => this.fallbackStrategy(task),
      () => this.humanEscalation(task)
    ];
    
    for (const strategy of strategies) {
      try {
        const result = await this.retry(strategy, 3);
        return result;
      } catch (error) {
        console.warn(`Strategy failed: ${error.message}`);
        continue;
      }
    }
    
    throw new Error('All strategies failed');
  }
  
  async retry(fn, attempts) {
    for (let i = 0; i < attempts; i++) {
      try {
        return await fn();
      } catch (error) {
        if (i === attempts - 1) throw error;
        await this.delay(Math.pow(2, i) * 1000); // Exponential backoff
      }
    }
  }
}
```

---
layout: fact
---

# 🎯 Quick Win: First Agent in 30 minutes

<div class="text-xl">

1. **GitHub Action** with OpenAI API setup (10 min)
2. **Simple Prompt** for code review (10 min)  
3. **Webhook** on Pull Request events (5 min)
4. **Test** with real PR (5 min)

</div>

<div class="mt-8 p-4 bg-blue-100 rounded-lg text-center">
<strong>Result:</strong> Automatic code review comments on every PR! 
</div>

---
layout: center 
class: text-center
---

# 🚀 Ready for Claude Code?

## Now let's look at the concrete tool

<div class="text-sm mt-8 opacity-75">
From patterns to practical implementations
</div>