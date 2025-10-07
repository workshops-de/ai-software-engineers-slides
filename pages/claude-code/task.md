---
layout: cover
class: text-center
---

# 🎯 Workshop: Claude Code Mastery

Hands-on with Claude for real development tasks

---
layout: default
---

# Workshop Overview 🎯

## Goal
Use Claude Code productively in real development scenarios and create a **Claude-powered GitHub Action**.

<div class="mt-8 grid grid-cols-4 gap-4">
<div class="p-4 bg-blue-100 rounded-lg text-center">
<strong>⏱️ Time</strong><br>60 minutes
</div>
<div class="p-4 bg-green-100 rounded-lg text-center">
<strong>🎯 Level</strong><br>Intermediate
</div>
<div class="p-4 bg-purple-100 rounded-lg text-center">
<strong>🛠️ Tools</strong><br>Claude, Cursor, GitHub
</div>
<div class="p-4 bg-orange-100 rounded-lg text-center">
<strong>🏆 Outcome</strong><br>Productive Agent
</div>
</div>

---
layout: default
---

# Phase 1: Claude Setup & First Interaction (15 min)

## Setup Checklist

<div class="grid grid-cols-2 gap-8">
<div>

### 1. Claude.ai Account
- [ ] Account created/verified
- [ ] Claude 3.5 Sonnet access
- [ ] API Key generated (if API access)

### 2. Cursor Installation  
- [ ] Cursor downloaded from cursor.sh
- [ ] VS Code settings imported (optional)
- [ ] Claude integration activated

</div>
<div>

### 3. Test Repository
```bash
# Clone workshop repo
git clone https://github.com/your-org/claude-workshop
cd claude-workshop

# Open in Cursor
cursor .
```

### 4. First Claude Interaction
- Press `Cmd+K` in Cursor
- Type "Analyze this codebase structure"
- Evaluate Claude's response

</div>
</div>

---
layout: default
---

# Phase 2: Code Analysis & Review (20 min) 

## Challenge 1: Legacy Code Analysis

```typescript
// Given: Problematic Legacy Function
function processUserData(users, options) {
  var result = [];
  for (var i = 0; i < users.length; i++) {
    if (users[i].active == true) {
      var userData = users[i];
      userData.processedAt = new Date();
      if (options && options.includeMetrics) {
        userData.metrics = calculateMetrics(userData);
      }
      result.push(userData);
    }
  }
  return result;
}
```

### Claude Prompt
```text
@legacy/userProcessor.js

Analyze this function and suggest improvements for:
1. Modern JavaScript/TypeScript patterns
2. Performance optimization  
3. Type safety
4. Error handling
5. Testability

Provide refactored code with explanations.
```

---
layout: default
---

# Challenge 1: Expected Claude Output 

```typescript
// Claude's Refactored Version
interface User {
  id: string;
  active: boolean;
  name: string;
  email: string;
  // ... other properties
}

interface ProcessingOptions {
  includeMetrics?: boolean;
  // ... other options
}

interface ProcessedUser extends User {
  processedAt: Date;
  metrics?: UserMetrics;
}

async function processUserData(
  users: User[], 
  options: ProcessingOptions = {}
): Promise<ProcessedUser[]> {
  if (!Array.isArray(users)) {
    throw new Error('Users must be an array');
  }

  const activeUsers = users.filter(user => user.active === true);
  
  return Promise.all(
    activeUsers.map(async (user) => {
      const processedUser: ProcessedUser = {
        ...user,
        processedAt: new Date()
      };

      if (options.includeMetrics) {
        try {
          processedUser.metrics = await calculateMetrics(user);
        } catch (error) {
          console.warn(`Failed to calculate metrics for user ${user.id}:`, error);
        }
      }

      return processedUser;
    })
  );
}
```

---
layout: default
---

# Challenge 2: Bug Detection & Fix (10 min)

## Buggy React Component

```typescript
import { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    setLoading(true);
    fetch(`/api/users/${userId}`)
      .then(response => response.json())
      .then(userData => {
        setUser(userData);
        setLoading(false);
      });
  }, []);

  if (loading) return <div>Loading...</div>;
  
  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

### Claude Prompt
```text
Find and fix all bugs in this React component. 
Explain each issue and provide the corrected version.
```

---
layout: default
---

# Challenge 2: Claude's Bug Analysis

## Identified Issues:

1. **Missing dependency**: `userId` not in useEffect deps
2. **No error handling**: fetch can fail
3. **Potential null access**: `user.name` when user is null  
4. **Memory leak**: No cleanup for cancelled requests
5. **Loading state**: Not reset on error

```typescript
// Claude's Fixed Version
import { useState, useEffect, useRef } from 'react';

function UserProfile({ userId }: { userId: string }) {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);
  const abortControllerRef = useRef<AbortController | null>(null);

  useEffect(() => {
    if (!userId) return;

    // Cancel previous request
    if (abortControllerRef.current) {
      abortControllerRef.current.abort();
    }

    const abortController = new AbortController();
    abortControllerRef.current = abortController;

    const fetchUser = async () => {
      setLoading(true);
      setError(null);
      
      try {
        const response = await fetch(`/api/users/${userId}`, {
          signal: abortController.signal
        });
        
        if (!response.ok) {
          throw new Error(`Failed to fetch user: ${response.statusText}`);
        }
        
        const userData = await response.json();
        setUser(userData);
      } catch (err) {
        if (err.name !== 'AbortError') {
          setError(err.message);
        }
      } finally {
        setLoading(false);
      }
    };

    fetchUser();

    // Cleanup
    return () => {
      abortController.abort();
    };
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>No user found</div>;
  
  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

---
layout: default
---

# Phase 3: Claude-powered GitHub Action (25 min)

## Goal: Intelligent PR Analyzer

### Step 1: Action Setup

```yaml
# .github/workflows/claude-pr-analyzer.yml
name: Claude PR Analyzer
on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  claude-analysis:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
          
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          
      - name: Install Claude SDK
        run: npm install @anthropic-ai/sdk @octokit/rest
        
      - name: Run Claude Analysis
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: node .github/scripts/claude-analyzer.js
```

---
layout: default
---

# Step 2: Claude Analyzer Script

```javascript
// .github/scripts/claude-analyzer.js
const Anthropic = require('@anthropic-ai/sdk');
const { Octokit } = require('@octokit/rest');
const { execSync } = require('child_process');

const anthropic = new Anthropic({
  apiKey: process.env.ANTHROPIC_API_KEY,
});

const octokit = new Octokit({
  auth: process.env.GITHUB_TOKEN,
});

async function analyzePR() {
  try {
    // Get PR details
    const prNumber = process.env.GITHUB_REF.split('/')[2];
    const [owner, repo] = process.env.GITHUB_REPOSITORY.split('/');
    
    const { data: pr } = await octokit.rest.pulls.get({
      owner,
      repo,
      pull_number: prNumber,
    });

    // Get diff
    const diff = execSync('git diff origin/main...HEAD', { encoding: 'utf8' });
    
    // Get changed files
    const { data: files } = await octokit.rest.pulls.listFiles({
      owner,
      repo,
      pull_number: prNumber,
    });

    await analyzeWithClaude(pr, diff, files, { owner, repo, prNumber });
    
  } catch (error) {
    console.error('Claude analysis failed:', error);
    process.exit(1);
  }
}
```

---
layout: default
---

# Step 3: Claude Intelligence Layer

```javascript
async function analyzeWithClaude(pr, diff, files, { owner, repo, prNumber }) {
  const prompt = `You are a senior software engineer reviewing a Pull Request.

PR DETAILS:
Title: ${pr.title}
Description: ${pr.body || 'No description provided'}
Author: ${pr.user.login}
Files changed: ${files.length}

FILES AFFECTED:
${files.map(f => `- ${f.filename} (+${f.additions}, -${f.deletions})`).join('\n')}

CODE CHANGES:
${diff}

ANALYSIS FRAMEWORK:
1. **Code Quality** (0-10): Readability, maintainability, best practices
2. **Security** (0-10): Vulnerabilities, input validation, sensitive data
3. **Performance** (0-10): Efficiency, memory usage, algorithmic complexity  
4. **Testing** (0-10): Test coverage, test quality, edge cases
5. **Documentation** (0-10): Comments, README updates, API docs

For each category:
- Provide score and rationale
- List specific issues found
- Suggest concrete improvements
- Highlight any blocking concerns

FORMAT:
## 🤖 Claude PR Analysis

### Summary
[Brief overall assessment]

### Scores
- Code Quality: X/10 - [rationale]
- Security: X/10 - [rationale]  
- Performance: X/10 - [rationale]
- Testing: X/10 - [rationale]
- Documentation: X/10 - [rationale]

### Detailed Findings
[Specific issues with line references]

### Recommendations
[Actionable improvement suggestions]

### Approval Status
❌ Needs Changes | ⚠️ Minor Issues | ✅ Approved`;

  const response = await anthropic.messages.create({
    model: 'claude-3-5-sonnet-20241022',
    max_tokens: 2000,
    messages: [
      { role: 'user', content: prompt }
    ],
  });

  const analysis = response.content[0].text;
  
  // Post analysis as PR comment
  await octokit.rest.issues.createComment({
    owner,
    repo,
    issue_number: prNumber,
    body: analysis,
  });

  console.log('✅ Claude analysis posted successfully!');
}

analyzePR();
```

---
layout: default
---

# Enhancement: Smart Labels & Actions

```javascript
// Add intelligent labeling based on Claude analysis
async function addSmartLabels(analysis, { owner, repo, prNumber }) {
  const labels = [];
  
  // Extract scores from analysis
  const scores = extractScores(analysis);
  
  // Add labels based on analysis
  if (scores.security < 7) labels.push('security-review-needed');
  if (scores.performance < 6) labels.push('performance-concern');  
  if (scores.testing < 5) labels.push('needs-tests');
  if (scores.documentation < 4) labels.push('needs-documentation');
  
  // Size labels
  const diff = execSync('git diff --stat origin/main...HEAD', { encoding: 'utf8' });
  const changedLines = parseInt(diff.match(/(\d+) insertions/)?.[1] || 0) + 
                      parseInt(diff.match(/(\d+) deletions/)?.[1] || 0);
  
  if (changedLines < 50) labels.push('size/small');
  else if (changedLines < 200) labels.push('size/medium');  
  else labels.push('size/large');

  // Apply labels
  if (labels.length > 0) {
    await octokit.rest.issues.addLabels({
      owner,
      repo,
      issue_number: prNumber,
      labels,
    });
  }
}

function extractScores(analysis) {
  const scores = {};
  const lines = analysis.split('\n');
  
  for (const line of lines) {
    const match = line.match(/- (\w+): (\d+)\/10/);
    if (match) {
      scores[match[1].toLowerCase()] = parseInt(match[2]);
    }
  }
  
  return scores;
}
```

---
layout: default
---

# Testing & Validation 🧪

## Create Test Cases

1. **Simple PR**: README update → Minimal Claude response
2. **Bug Fix**: Code change with tests → Positive review  
3. **Security Issue**: Hardcoded passwords → Security warning
4. **Performance Problem**: Inefficient algorithm → Performance alert
5. **Breaking Change**: API modification → Documentation request

```javascript
// Test different PR types
const testScenarios = [
  {
    name: 'Documentation Update',
    files: ['README.md'],
    expectedLabels: ['documentation', 'size/small']
  },
  {
    name: 'Security Fix', 
    files: ['auth/security.js'],
    content: 'remove hardcoded API key',
    expectedLabels: ['security-review-needed']
  },
  // ... more scenarios
];
```

---
layout: fact
---

# 🏆 Workshop Success Criteria

<div class="text-xl">

✅ **Claude Setup**: Productive in Cursor/Web interface  
✅ **Code Analysis**: Legacy code successfully refactored  
✅ **Bug Detection**: React bugs found & fixed  
✅ **GitHub Action**: Claude analyzer deployed & tested  
✅ **Intelligence**: Smart labels & actions working  

</div>

<div class="text-center mt-8 p-4 bg-green-100 rounded-lg">
<strong>Outcome:</strong> Claude is now part of your development workflow! 🚀
</div>

---
layout: default
---

# Next Steps & Extensions 📈

## Immediate Improvements
- **Custom Prompts** for team-specific reviews
- **Integration Testing** with Claude analysis
- **Slack Notifications** for critical issues  
- **Metrics Dashboard** for Claude performance

## Advanced Features
- **Multi-Agent Review**: Security + Performance + Quality agents
- **Learning Loop**: Claude learns from team feedback
- **Predictive Analysis**: Bug prediction based on patterns
- **Auto-Fix Generation**: Claude suggests code fixes

```typescript
// Future Vision: Auto-implementing fixes
class AutoFixAgent {
  async generateFix(issue) {
    const fix = await claude.generateCodeFix(issue);
    const tests = await claude.generateTests(fix);
    const pr = await github.createPR({ fix, tests });
    return pr;
  }
}
```

---
layout: center
class: text-center
---

# 🎉 Claude Code Mastery Achieved!

## You've created an intelligent development assistant

### Next Step: GitHub Workflows for automation

<div class="text-sm mt-8 opacity-75">
Ready for the final module?
</div>