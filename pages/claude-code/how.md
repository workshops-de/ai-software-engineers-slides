---
layout: section
---

# How to use Claude Code?

Practical application and best practices

---
layout: default
---

# Claude Code Access Options 🔑

<div class="grid grid-cols-3 gap-6">
<div class="p-4 bg-blue-50 rounded-lg">

### 🌐 Claude.ai
- **Web Interface**
- Basic Code Capabilities
- File Upload possible
- No direct IDE access

**Best for:** Code Reviews, Explanations

</div>
<div class="p-4 bg-green-50 rounded-lg">

### 💻 Cursor Editor  
- **Native Claude Integration**
- Full Codebase Access
- AI-powered Features
- MCP Support

**Best for:** Active Development

</div>
<div class="p-4 bg-purple-50 rounded-lg">

### 🔌 API Integration
- **Custom Implementations**
- Workflow Automation  
- CI/CD Integration
- Enterprise Solutions

**Best for:** Automation & Scaling

</div>
</div>

---
layout: two-cols
layoutClass: gap-16
---

# Getting Started: Cursor Setup 🚀

## Installation & Config

```bash
# Download Cursor from cursor.sh
# Import existing VS Code settings
cursor --import-settings

# Configure Claude API
# Settings → Extensions → Claude
{
  "claude.apiKey": "your-api-key",
  "claude.model": "claude-3-5-sonnet-20241022",
  "claude.contextWindow": 200000
}
```

## First Interaction
```typescript
// Cmd+K in Cursor activates Claude
// Example conversation:
// User: "Add error handling to this function"
// Claude analyzes context and suggests improvements
```

::right::

## Key Shortcuts in Cursor

| Shortcut | Action |
|----------|--------|
| `Cmd+K` | Start Claude chat |
| `Cmd+Shift+K` | Claude with file context |
| `Cmd+I` | Inline Claude suggestions |
| `Cmd+L` | Add to Claude context |

<div class="mt-4 p-4 bg-green-100 rounded-lg text-sm">
<strong>Tip:</strong> Use `@filename` to include specific files in context
</div>

---
layout: default
---

# Effective Prompting Strategies 🎯

## 1. Context-First Prompting
```typescript
// ❌ Vague
"Fix this code"

// ✅ Context-rich
"This React component handles user authentication. 
The login function should validate email format before API call.
Current issue: Users can submit invalid emails.
Please add proper validation while maintaining existing UX."
```

## 2. Specific Output Requests
```typescript
// ❌ General
"Make this better"

// ✅ Specific  
"Refactor this function to:
1. Use async/await instead of promises
2. Add TypeScript types
3. Include error handling with try/catch
4. Maintain backward compatibility"
```

---
layout: default
---

# Claude Workflows for Typical Tasks 📋

## Code Review Workflow
```typescript
// 1. Load file context
"@src/components/UserProfile.tsx analyze this component for:"

// 2. Specific review points
"- Performance issues
- Security vulnerabilities  
- Code quality improvements
- TypeScript type safety"

// 3. Action items
"Provide specific refactoring suggestions with code examples"
```

## Debugging Workflow  
```typescript
// 1. Describe problem
"This async function fails intermittently with Promise rejection"

// 2. Include relevant files
"@src/services/api.ts @src/utils/retry.ts"

// 3. Error context
"Error occurs when network is slow. Stack trace: [paste here]"

// 4. Solution request
"Suggest robust error handling and retry logic"
```

---
layout: two-cols
layoutClass: gap-16
---

# Advanced Claude Patterns 🔥

## Multi-file Refactoring
```typescript
// Claude can orchestrate large refactorings
"I want to migrate from Redux to Zustand:

Files to update:
@src/store/redux-store.ts
@src/hooks/useAppState.ts  
@src/components/UserDashboard.tsx

Provide step-by-step migration plan with:
1. New Zustand store structure
2. Hook replacements
3. Component updates
4. Testing strategy"
```

::right::

## Architecture Review
```typescript  
// Claude analyzes entire architecture
"Review the architecture of this Express API:

@src/routes/
@src/controllers/
@src/services/  
@src/models/

Evaluate:
- Separation of concerns
- Error handling consistency
- Database query efficiency
- Security best practices

Suggest improvements with rationale"
```

---
layout: default
---

# MCP Integration in Practice 🔌

## GitHub MCP Setup
```typescript
// Install GitHub MCP Server
npm install @anthropic/github-mcp-server

// Configure in Cursor settings
{
  "mcp": {
    "servers": {
      "github": {
        "command": "npx",
        "args": ["@anthropic/github-mcp-server"],
        "env": {
          "GITHUB_TOKEN": "your-token"
        }
      }
    }
  }
}
```

## Using GitHub MCP
```typescript
// Claude can now execute GitHub operations directly
"Create a new issue for the bug we just discussed:
Title: 'User profile validation missing'
Body: Include the fix we implemented
Labels: bug, high-priority
Assign to: @me"
```

---
layout: default
---

# Testing with Claude 🧪

## Test Generation Workflow
```typescript
// 1. Provide context
"@src/utils/validation.ts 
Generate comprehensive unit tests for this validation utility"

// 2. Specify test requirements
"Include tests for:
- Valid inputs (happy path)
- Invalid inputs (error cases)  
- Edge cases (empty, null, undefined)
- Type safety validation
- Performance with large inputs"

// 3. Test framework preference
"Use Jest with TypeScript. Follow AAA pattern (Arrange, Act, Assert)"
```

## Claude's Test Output
```typescript
// Claude generates structured tests
describe('ValidationUtils', () => {
  describe('validateEmail', () => {
    it('should validate correct email formats', () => {
      // Test implementation
    });
    
    it('should reject invalid email formats', () => {
      // Test implementation  
    });
    
    it('should handle edge cases gracefully', () => {
      // Test implementation
    });
  });
});
```

---
layout: default
---

# Documentation with Claude 📚

## API Documentation
```typescript
// Prompt for API docs
"@src/api/routes/users.ts
Generate OpenAPI/Swagger documentation for all routes in this file.
Include:
- Request/Response schemas
- Error responses
- Authentication requirements
- Example requests"
```

## Code Comments
```typescript
// Before: Undocumented function
function processUserData(data, options) {
  // Complex logic without explanation
}

// Claude's enhanced version with JSDoc
/**
 * Processes raw user data according to specified options
 * @param {Object} data - Raw user data from external source
 * @param {ProcessingOptions} options - Configuration for processing
 * @param {boolean} options.sanitize - Whether to sanitize input
 * @param {string[]} options.allowedFields - Fields to include in output
 * @returns {Promise<ProcessedUser>} Processed and validated user object
 * @throws {ValidationError} When data format is invalid
 */
async function processUserData(data, options) {
  // Implementation with clear comments
}
```

---
layout: default
---

# Performance Optimization with Claude ⚡

## Analysis Prompt
```typescript
"@src/components/DataTable.tsx
This component renders slowly with large datasets (>1000 rows).
Analyze performance bottlenecks and suggest optimizations:

1. React rendering optimization
2. Memory usage reduction  
3. Scroll virtualization options
4. Caching strategies
5. Code splitting opportunities"
```

## Claude's Optimization Suggestions
```typescript
// Before: Naive implementation
function DataTable({ data }) {
  return (
    <table>
      {data.map(row => <TableRow key={row.id} data={row} />)}
    </table>
  );
}

// Claude's optimized version
import { FixedSizeList as List } from 'react-window';

const DataTable = ({ data }) => {
  const Row = useCallback(({ index, style }) => (
    <div style={style}>
      <TableRow data={data[index]} />
    </div>
  ), [data]);

  return (
    <List height={600} itemCount={data.length} itemSize={50}>
      {Row}
    </List>
  );
};
```

---
layout: two-cols
layoutClass: gap-16
---

# Error Handling Best Practices 🛡️

## Claude Prompt Strategy
```typescript
"Review error handling in this service:
@src/services/payment.ts

Requirements:
- Proper error types/hierarchy
- Logging with context
- User-friendly messages
- Retry logic for transient failures
- Circuit breaker pattern"
```

::right::

## Claude's Enhanced Error Handling
```typescript
class PaymentError extends Error {
  constructor(message, code, details) {
    super(message);
    this.code = code;
    this.details = details;
  }
}

class PaymentService {
  async processPayment(data) {
    try {
      return await this.apiCall(data);
    } catch (error) {
      this.logger.error('Payment failed', {
        userId: data.userId,
        amount: data.amount,
        error: error.message
      });
      
      if (this.isRetryable(error)) {
        return await this.retryWithBackoff(data);
      }
      
      throw new PaymentError(
        'Payment processing failed',
        error.code,
        error.details
      );
    }
  }
}
```

---
layout: default
---

# Claude Code Review Checklist ✅

## Automated Review Points
```typescript
"Review this PR with focus on:

✅ Code Quality
- Readability and maintainability
- DRY principle adherence
- Proper naming conventions

✅ Performance  
- Algorithmic efficiency
- Memory usage optimization
- Async/await best practices

✅ Security
- Input validation
- SQL injection prevention
- XSS vulnerability checks

✅ Testing
- Test coverage adequacy
- Edge case handling
- Mocking strategies

✅ Documentation
- Code comments clarity
- API documentation completeness
- README updates needed"
```

---
layout: fact
---

# Claude's Development Superpowers

<div class="text-xl">

🧠 **Context Awareness** - Understands entire codebase  
⚡ **Speed** - Seconds instead of hours for analysis  
🎯 **Precision** - Specific, actionable suggestions  
🔄 **Iteration** - Continuous improvement  
🛡️ **Reliability** - Consistent code quality  

</div>

<div class="text-center mt-8 text-lg">
Not a tool - a team member!
</div>

---
layout: center
class: text-center
---

# 🎯 Next: Advanced Scenarios

## What's possible with Claude Code?

<div class="text-sm mt-8 opacity-75">
From basics to advanced use cases
</div>