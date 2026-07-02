## **Introduction**

"I want to clean up my codebase and improve code quality through a careful, low risk cleanup pass.

Work in 7 focused tracks. For each track: inspect the code, write a critical assessment, rank changes by confidence, implement ONLY high confidence low risk fixes, then run all checks after."

---

## **Subagent 1: Deduplication**

"Scan the entire codebase for repeated logic, copy pasted functions, and redundant abstractions.

Consolidate where it reduces complexity without obscuring intent.

Implement DRY only where it genuinely simplifies. Do not merge code that merely LOOKS similar but serves different purposes."

---

## **Subagent 2: Type Consolidation**

"Find all type definitions scattered across files.

Consolidate any that should be shared where duplication causes drift or inconsistency.

Check for types defined in multiple places that have quietly gone out of sync.

Merge into a single source of truth."

---

## **Subagent 3: Dead Code Removal**

"Use tools like **knip** to find all unused exports, unreferenced functions, and orphaned files.

Verify MANUALLY before removing. Check for dynamic imports, config references, framework conventions, and code generation that static analysis misses.

Remove only what is CONFIRMED dead."

---

## **Subagent 4: Circular Dependencies**

"Use tools like **madge** to map the full dependency graph.

Identify every circular import and prioritize the ones that affect maintainability, testability, or correctness.

Untangle by extracting shared logic into neutral modules.

Do not introduce new abstractions just to break a cycle."

---

## **Subagent 5: Type Strengthening**

"Find every instance of 'any,' 'unknown,' and other weak types the AI left as placeholders.

Research what the real types should be by inspecting the codebase, related packages, and actual runtime usage.

Replace with STRONG types. Run type checks after every batch. Preserve legitimate boundary types where 'unknown' is correct."

---

## **Subagent 6: Error Handling Cleanup**

"Find all try/catch blocks and equivalent defensive patterns.

Remove any that are silently swallowing errors, hiding failures, or falling back to defaults that mask real problems.

Keep error handling that serves a REAL boundary: recovery, logging, cleanup, or user facing error reporting.

No error hiding. No silent fallbacks."

---

## **Subagent 7: Deprecated Code and AI Slop**

"Find legacy, deprecated, and fallback code paths. Remove only what is CLEARLY obsolete and not required for compatibility or active users.

Then find every AI artifact: stubs, placeholder logic, comments that narrate edit history instead of explaining intent.

Remove all of it. If a comment is worth keeping, rewrite it so a NEW engineer understands why the code exists."