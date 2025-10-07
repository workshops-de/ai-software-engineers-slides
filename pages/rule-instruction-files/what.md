---
layout: cover
---

# What Are Rule & Instruction Files?

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: center
---

# Core Concepts

<div class="text-xl space-y-4 mt-8">

Rule & instruction files are:

- **Structured documents** that guide AI coding assistants
- **Markdown-based** configuration for AI behavior
- **Project-specific guidance** encoded in plain text
- **Version-controlled** standards for AI interaction

</div>

---
layout: default
---

# 📄 Anatomy of Rule Files

<div class="space-y-6">

<div class="flex items-start gap-4">
<div class="text-3xl">📚</div>
<div>
<h3 class="font-bold text-lg">Sections & Structure</h3>
<p class="text-gray-600 dark:text-gray-400">Organized into logical blocks with clear hierarchy</p>
</div>
</div>

<div class="flex items-start gap-4">
<div class="text-3xl">🔖</div>
<div>
<h3 class="font-bold text-lg">Tags & Directives</h3>
<p class="text-gray-600 dark:text-gray-400">Special markers that control AI behavior and focus</p>
</div>
</div>

<div class="flex items-start gap-4">
<div class="text-3xl">🧩</div>
<div>
<h3 class="font-bold text-lg">Rules & Constraints</h3>
<p class="text-gray-600 dark:text-gray-400">Explicit guidelines that shape AI output</p>
</div>
</div>

<div class="flex items-start gap-4">
<div class="text-3xl">📏</div>
<div>
<h3 class="font-bold text-lg">Examples & Templates</h3>
<p class="text-gray-600 dark:text-gray-400">Reference implementations for AI to follow</p>
</div>
</div>

</div>

---
layout: two-cols
---

# Common File Types

**Project Rules:**
- Overall architecture guidance
- Technology stack constraints
- Naming conventions
- Project structure rules
- Security requirements

**Feature Rules:**
- Component standards
- Module-specific patterns
- Feature implementation guides
- Testing requirements

::right::

**Workflow Rules:**
- PR creation standards
- Code review templates
- Documentation requirements
- Release procedures

**AI Behavior Rules:**
- Response format preferences
- Verbosity settings
- Code generation constraints
- Error handling procedures

---
layout: center
---

# The Markdown Standard

<div class="text-xl mb-6">
Rule files follow a specialized markdown syntax that AI tools can interpret
</div>

<div class="grid grid-cols-2 gap-8 mt-8">
  <div class="p-6 bg-blue-50 dark:bg-blue-900/20 rounded-lg">
    <h3 class="text-xl font-bold mb-4 text-blue-800">Standard Elements</h3>
    <ul class="text-left space-y-2 text-blue-700">
      <li>Headers for sections</li>
      <li>Lists for enumeration</li>
      <li>Code blocks with language tags</li>
      <li>Tables for structured data</li>
    </ul>
  </div>
  <div class="p-6 bg-purple-50 dark:bg-purple-900/20 rounded-lg">
    <h3 class="text-xl font-bold mb-4 text-purple-800">AI-Specific Extensions</h3>
    <ul class="text-left space-y-2 text-purple-700">
      <li>Tool directive tags</li>
      <li>Context blocks</li>
      <li>Priority indicators</li>
      <li>Memory references</li>
    </ul>
  </div>
</div>

---
layout: default
---

# 📊 Rule File Ecosystem

<div class="text-lg space-y-6">

All modern AI coding tools use Markdown for instructions:

- **Cursor, GitHub Copilot, Junie, Claude**: Use Markdown-based files
- Some tools add optional YAML frontmatter for metadata
- All support standard Markdown elements (headers, lists, code blocks)
- All recognize specialized sections for different rule types

Common patterns across all platforms:

- Clear section hierarchies using headers
- Code examples as fenced code blocks
- Project-specific terminology in structured lists
- Simple, readable plain text format

</div>

---
layout: default
---

# Best practices
## Good rules are focused, actionable, and scoped.

- Keep rules under **500** lines
- Split large rules into multiple, composable rules
- Provide concrete examples or referenced files
- Avoid vague guidance. Write rules like clear internal docs
- Reuse rules when repeating prompts in chat
