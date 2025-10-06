---
layout: cover
---

# How to Create Rule & Instruction Files
## Practical implementation for coding agents

---
layout: center
---

# Implementation Approach

<div class="text-xl">

All AI coding tools support Markdown-based rule files:

- **Simple Markdown format** - consistent across tools
- **Strategic file placement** - visible to AI assistants
- **Hierarchical structure** - using standard Markdown headers
- **Scope considerations** - from project-wide to file-specific

</div>

---
layout: two-cols
layoutClass: gap-6
---

# Markdown-Based Rule Implementation

```md {maxHeight:'500px'}
# Project Rules

## Coding Standards
- Use TypeScript for all new files
- Follow camelCase for variables and methods
- Use PascalCase for classes and interfaces
- Maximum line length: 100 characters

## Architecture
- Implement feature-based directory structure
- Follow SOLID principles
- Use dependency injection

## Component Pattern
interface ComponentProps {
  title: string;
  onAction: () => void;
}

export function ExampleComponent({ title, onAction }: ComponentProps) {
  // Implementation
}
```

::right::

# Common File Locations

- `/.github/workspace-instructions.md`
- `/.vscode/ai-rules.md`
- `/.cursorrules.md`
- `/docs/ai-instructions.md`

# Key Features

- Simple Markdown format
- Optional frontmatter for metadata
- Headers for logical sections
- Lists for rules and constraints
- Fenced code blocks for examples

---
layout: default
---

# 📁 File Placement Strategies

<div class="space-y-6">

<div class="flex items-start gap-4">
<div class="text-3xl">📌</div>
<div>
<h3 class="font-bold text-lg">Root Directory</h3>
<p class="text-gray-600 dark:text-gray-400">General project-wide rules that apply to all code</p>
<div class="mt-2 text-sm bg-gray-100 dark:bg-gray-800 p-2 rounded">
<code>/.cursorrules.md</code>, <code>/.github/copilot/workspace.md</code>
</div>
</div>
</div>

<div class="flex items-start gap-4">
<div class="text-3xl">🗂️</div>
<div>
<h3 class="font-bold text-lg">Feature Directories</h3>
<p class="text-gray-600 dark:text-gray-400">Feature-specific rules that override or extend general rules</p>
<div class="mt-2 text-sm bg-gray-100 dark:bg-gray-800 p-2 rounded">
<code>/src/components/.rules.md</code>, <code>/src/api/.instructions</code>
</div>
</div>
</div>

<div class="flex items-start gap-4">
<div class="text-3xl">📄</div>
<div>
<h3 class="font-bold text-lg">File-specific Comments</h3>
<p class="text-gray-600 dark:text-gray-400">Inline instructions for specific files or components</p>
<div class="mt-2 text-sm bg-gray-100 dark:bg-gray-800 p-2 rounded">
<code>/* @ai-rules: Follow Redux pattern */</code>
</div>
</div>
</div>

</div>

---
layout: image-right
image: https://images.unsplash.com/photo-1555066931-4365d14bab8c?w=500
---

# Best Practices

<div class="space-y-4 text-base">

- **Be specific and concrete** rather than general
- **Include examples** of desired patterns
- **Explain the why** behind each rule
- **Use hierarchical structure** for clarity
- **Prioritize rules** with importance indicators
- **Keep rules updated** as project evolves
- **Version control** your rule files
- **Test rule effectiveness** with real tasks

</div>

---
layout: default
---

# 🛠️ Simple Rule File Example

<div class="bg-gray-100 dark:bg-gray-800 p-6 rounded-lg overflow-auto" style="max-height: 450px;">

# Project Rules

## Purpose
These rules guide AI coding assistants to maintain consistency
and follow our team's best practices across the codebase.

## Code Style
- Use TypeScript for all new files
- Follow camelCase for variables and methods
- Use PascalCase for classes and interfaces
- Maximum line length: 100 characters
- Use 2-space indentation

## Architecture
- Organize code by feature, not by type
- Follow SOLID principles
- Use functional components with hooks for React
- Keep files under 200 lines

</div>

---
layout: two-cols
---

# Common Sections to Include

**Project Context:**
- Purpose and goals
- Tech stack overview
- Architectural patterns
- Domain terminology

**Code Standards:**
- Style conventions
- Documentation requirements
- Testing expectations
- Performance considerations

::right::

**Workflow Guidelines:**
- Git commit standards
- PR process instructions
- Review criteria
- Release procedures

**AI-Specific Instructions:**
- Response format preferences
- Error handling expectations
- Level of explanation needed
- Code generation constraints
