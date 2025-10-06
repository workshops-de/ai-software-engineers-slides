---
layout: cover
---

# 🎯 Practical Exercise
## Understanding LLM Behavior

---
layout: default
---

# 📝 Exercise Overview

<div class="bg-gradient-to-r from-blue-50 to-purple-50 dark:from-blue-900/20 dark:to-purple-900/20 p-6 rounded-xl mb-6">
<h3 class="text-xl font-bold mb-3">Goal: Experience LLM phenomena firsthand</h3>
<p>Work through practical scenarios to understand how LLMs behave and why</p>
</div>

<div class="grid grid-cols-2 gap-6">

<div>
<h3 class="font-bold text-lg mb-3">What You'll Do:</h3>
<ul class="space-y-2">
<li>✓ Test tokenization effects</li>
<li>✓ Observe attention patterns</li>
<li>✓ Trigger hallucinations</li>
<li>✓ Fix context problems</li>
<li>✓ Optimize prompts</li>
</ul>
</div>

<div>
<h3 class="font-bold text-lg mb-3">What You'll Learn:</h3>
<ul class="space-y-2">
<li>→ How input affects output</li>
<li>→ Why models fail</li>
<li>→ How to work around limits</li>
<li>→ Best practices for prompting</li>
<li>→ Verification techniques</li>
</ul>
</div>

</div>

---
layout: default
---

# 🔬 Task 1: Tokenization Impact

<div class="bg-yellow-50 dark:bg-yellow-900/20 p-4 rounded-lg mb-4">
<strong>Objective:</strong> See how formatting affects token count and model understanding
</div>

<div class="grid grid-cols-2 gap-4">

<div>
<h4 class="font-bold mb-2">Step 1: Compare these prompts</h4>

```javascript
// Prompt A (minified)
"const sum=(a,b)=>a+b;const multiply=(a,b)=>a*b;const divide=(a,b)=>b!==0?a/b:null;"

// Prompt B (formatted)
"const sum = (a, b) => a + b;
const multiply = (a, b) => a * b;
const divide = (a, b) =>
  b !== 0 ? a / b : null;"
```
</div>

<div>
<h4 class="font-bold mb-2">Step 2: Ask AI to:</h4>
<ul class="text-sm space-y-2">
<li>1. Explain each function</li>
<li>2. Add error handling</li>
<li>3. Convert to TypeScript</li>
</ul>

<div class="bg-blue-50 dark:bg-blue-900/20 p-3 rounded mt-4">
<strong>Observe:</strong><br/>
Which format gets better results? Why?
</div>
</div>

</div>

---
layout: default
---

# 🎭 Task 2: Trigger a Hallucination

<div class="bg-yellow-50 dark:bg-yellow-900/20 p-4 rounded-lg mb-4">
<strong>Objective:</strong> Make the model confidently invent something that doesn't exist
</div>

<div class="space-y-4">

<div>
<h4 class="font-bold mb-2">Try these prompts:</h4>

```typescript
// Prompt 1: Ask about a fake Vue feature
"How do I use Vue.createQuantumState()
 in Vue 3.5 for parallel rendering?"

// Prompt 2: Request non-existent API
"Show me how to use the @vue/telepathy
 package for mind-reading components"

// Prompt 3: Mix real and fake
"Combine Vue's Composition API with the
 new useTimeTravel() hook from Vue 3.6"
```
</div>

<div class="bg-red-50 dark:bg-red-900/20 p-4 rounded-lg">
<strong>🔍 What happens?</strong> The model will likely create plausible-sounding but completely fictional implementations
</div>

</div>

---
layout: two-cols
---

# 🌀 Task 3: Lost in the Middle

<div class="text-sm">

<h4 class="font-bold mb-2">Create a long context:</h4>

```typescript
// Start with lots of setup...
import { ref, computed } from 'vue'
// ... 50+ lines of code ...

// IMPORTANT: Use 'isActive'
const isActive = ref(false)

// ... 50+ more lines ...

// Ask: "What's the variable
// for active state?"
```

<strong>Test:</strong> Does AI remember the middle instruction?

</div>

::right::

<div class="text-sm">

<h4 class="font-bold mb-2">Fix with anchoring:</h4>

```typescript
// === CRITICAL VARIABLES ===
// isActive: controls active state
const isActive = ref(false)
// === END CRITICAL ===

// ... rest of code ...

// REMINDER: isActive is the
// active state variable
```

<strong>Result:</strong> Better recall with clear markers

</div>

---
layout: default
---

# 🔧 Task 4: Prompt Engineering Challenge

<div class="bg-gradient-to-r from-blue-50 to-purple-50 dark:from-blue-900/20 dark:to-purple-900/20 p-4 rounded-lg mb-4">
<strong>Objective:</strong> Transform a vague request into a precise, effective prompt
</div>

<div class="grid grid-cols-2 gap-6">

<div>
<h4 class="font-bold mb-3">❌ Vague Request:</h4>

```text
"Make a form"
```

<div class="bg-red-50 dark:bg-red-900/20 p-3 rounded mt-3">
Results: Generic, possibly outdated, no validation, unclear purpose
</div>
</div>

<div>
<h4 class="font-bold mb-3">✅ Engineered Prompt:</h4>

```text
"Create a Vue 3 contact form using:
- Composition API with <script setup>
- TypeScript interfaces
- Fields: name, email, message
- Validation: required, email format
- Accessibility: ARIA labels
- Error handling with user feedback
- Submit to /api/contact endpoint"
```

<div class="bg-green-50 dark:bg-green-900/20 p-3 rounded mt-3">
Results: Specific, modern, complete
</div>
</div>

</div>

---
layout: default
---

# 🎯 Task 5: Multi-Step Reasoning

<div class="bg-yellow-50 dark:bg-yellow-900/20 p-4 rounded-lg mb-4">
<strong>Objective:</strong> Use Chain of Thought to solve a complex problem
</div>

<div class="space-y-4">

<h4 class="font-bold">Scenario: Optimize a slow Vue component</h4>

<div class="grid grid-cols-2 gap-4">

<div>
<h5 class="font-semibold mb-2">Without CoT:</h5>

```text
"This component is slow, fix it"
```

Result: Random optimizations
</div>

<div>
<h5 class="font-semibold mb-2">With CoT:</h5>

```text
"Analyze step by step:
1. Identify performance bottlenecks
2. Explain why each is slow
3. Propose specific solutions
4. Show implementation
5. Explain the improvements"
```

Result: Systematic optimization
</div>

</div>

</div>

---
layout: two-cols
---

# 🔍 Task 6: Verification Practice

<div class="text-sm space-y-3">

<h4 class="font-bold">AI generates this code:</h4>

```typescript
import { useAsyncState } from '@vueuse/core'
import { performanceMonitor } from 'vue'

const { state, isLoading } =
  useAsyncState(fetchData, null)

performanceMonitor.track(component)
```

<strong>Your task:</strong> Verify each import and API

</div>

::right::

<div class="text-sm space-y-3">

<h4 class="font-bold">Verification Steps:</h4>

1. ✅ Check `@vueuse/core` docs
2. ❌ `performanceMonitor` doesn't exist
3. ✅ `useAsyncState` is real
4. ❌ `.track()` is invented

<div class="bg-yellow-50 dark:bg-yellow-900/20 p-3 rounded mt-3">
<strong>Lesson:</strong> Always verify imports and APIs, even if they look plausible!
</div>

</div>

---
layout: default
---

# 🏁 Exercise Summary

<div class="grid grid-cols-2 gap-8">

<div>
<h3 class="font-bold text-lg mb-4">✅ What You Practiced:</h3>
<ul class="space-y-3">
<li class="flex items-start gap-2">
<span class="text-green-500">✓</span>
<span>Tokenization effects on output</span>
</li>
<li class="flex items-start gap-2">
<span class="text-green-500">✓</span>
<span>Triggering and identifying hallucinations</span>
</li>
<li class="flex items-start gap-2">
<span class="text-green-500">✓</span>
<span>Working around context limitations</span>
</li>
<li class="flex items-start gap-2">
<span class="text-green-500">✓</span>
<span>Engineering effective prompts</span>
</li>
<li class="flex items-start gap-2">
<span class="text-green-500">✓</span>
<span>Using Chain of Thought</span>
</li>
<li class="flex items-start gap-2">
<span class="text-green-500">✓</span>
<span>Verifying AI output</span>
</li>
</ul>
</div>

<div>
<h3 class="font-bold text-lg mb-4">🎯 Key Takeaways:</h3>
<div class="space-y-4">

<div class="bg-blue-50 dark:bg-blue-900/20 p-4 rounded-lg">
<strong>Format matters</strong><br/>
<span class="text-sm">Clean code → better AI understanding</span>
</div>

<div class="bg-green-50 dark:bg-green-900/20 p-4 rounded-lg">
<strong>Trust but verify</strong><br/>
<span class="text-sm">Always check generated APIs</span>
</div>

<div class="bg-purple-50 dark:bg-purple-900/20 p-4 rounded-lg">
<strong>Structure helps</strong><br/>
<span class="text-sm">Clear prompts → clear results</span>
</div>

<div class="bg-yellow-50 dark:bg-yellow-900/20 p-4 rounded-lg">
<strong>Iterate to improve</strong><br/>
<span class="text-sm">Refine prompts for better output</span>
</div>

</div>
</div>

</div>

---
layout: center
---

# 🚀 Next Steps

<div class="space-y-6 text-lg">

<div class="bg-gradient-to-r from-blue-50 to-purple-50 dark:from-blue-900/20 dark:to-purple-900/20 p-6 rounded-xl">
<strong>Continue Learning:</strong>
<ul class="mt-3 space-y-2">
<li>Experiment with different AI tools</li>
<li>Build a personal prompt library</li>
<li>Share findings with your team</li>
<li>Stay updated on AI developments</li>
</ul>
</div>

<div class="text-center text-2xl font-bold mt-8">
Understanding AI makes you a better developer
</div>

</div>
