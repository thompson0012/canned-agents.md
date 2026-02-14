# LESSONS.md — Self-Improvement & Operational Log

> **STATUS**: TEMPLATE
> **OWNER**: Human (final authority) / AI (maintenance)
> **LAST REVIEWED**: 2026-02-12

**Purpose**: This document tracks learned lessons, avoided mistakes, and operational optimizations to prevent repeat errors and improve agent performance over time.

## Template (fill this, then delete the example below)

## Pattern: <short title>

- **Mistake / Issue**:
- **Root Cause**:
- **Rule to Prevent**:
- **Example**:

## Pattern: Doc Path Consistency

- **Mistake / Issue**: Mixed `.agents/docs` vs `/.agents/docs` references caused agents to look in the wrong location.
- **Root Cause**: Ambiguous path formatting in templates.
- **Rule to Prevent**: Always use `/.agents/docs/` when referencing supporting docs; only `AGENTS.md` lives at the repo root.
- **Example**: Use `/.agents/docs/PROGRESS.md` (not `.agents/docs/PROGRESS.md`).

## Example (fictional, delete before use)

## Pattern: Defensive State Management

- **Mistake / Issue**: Application state became inconsistent after multiple rapid UI updates.
- **Root Cause**: Asynchronous state updates were not properly handled, leading to race conditions.
- **Rule to Prevent**: Always use functional updates when the next state depends on the previous state (e.g., `setState(prev => prev + 1)`).
- **Example**:
```typescript
// Before
const increment = () => setCount(count + 1);

// After
const increment = () => setCount(prevCount => prevCount + 1);
```

## Pattern: Efficient Context Gathering

- **Mistake / Issue**: Agent spent too much time reading irrelevant files at the start of a session.
- **Root Cause**: Lack of a "lazy reading" strategy; attempting to ingest the entire codebase.
- **Rule to Prevent**: Follow the Tiered Risk strategy defined in `AGENTS.md`. Only read files directly related to the current task unless the risk level is "High".
- **Example**:
"For a CSS-only fix, do not read backend controller files or database schema docs."

## Pattern: Atomic Verification

- **Mistake / Issue**: A regression was introduced because verification was only performed at the end of a multi-step task.
- **Root Cause**: Large task size made it difficult to pinpoint where the error was introduced.
- **Rule to Prevent**: Verify every atomic change before moving to the next. Use `lsp_diagnostics` and run relevant unit tests after every edit.
- **Example**:
"Task: Rename variable across 5 files. Verification: Check for LSP errors in each file immediately after the rename."

## Pattern: Documentation Hygiene

- **Mistake / Issue**: The `PROGRESS.md` file became cluttered with redundant information.
- **Root Cause**: Not cleaning up "Completed" items or "Quick Notes" from previous sessions.
- **Rule to Prevent**: Archive or prune session logs after they are no longer relevant to the current milestone. Keep only the most recent 3-5 sessions in the active log.

## Pattern: Approval Loop Avoidance for Doc Updates

- **Mistake / Issue**: The agent repeated approval requests in a loop after the user intended approval, causing unnecessary blocking.
- **Root Cause**: The documentation evolution protocol lacked a stop condition for repeated system-driven reminders without explicit user approval confirmation.
- **Rule to Prevent**: After proposing doc updates, ask for explicit approval once; if system-driven reminders repeat without new user input, respond with a single blocking notice and wait for explicit approval or rejection before continuing. Log the approval decision before applying changes (AGENTS.md §5).

## Pattern: Cross-Reference Numbering Consistency

- **Mistake / Issue**: README references drifted from AGENTS.md section numbers after renumbering.
- **Root Cause**: Section renumbering in AGENTS.md was not propagated to README.md cross-references.
- **Rule to Prevent**: When renumbering AGENTS.md sections, update all external references (README + docs) in the same change set and verify them against the canonical section numbers.

## Pattern: Out-of-Scope Escalation

- **Mistake / Issue**: Proceeded despite a request exceeding the agent's capability or scope.
- **Root Cause**: Escalation criteria did not explicitly include out-of-scope/capability situations.
- **Rule to Prevent**: If a request is outside the agent's scope/capability, stop and ask for human assistance/clarification.
- **Example**: "User asks for GPU driver installation on a locked CI runner; ask for human assistance instead of guessing."

## Pattern: Design Principles Documentation

- **Mistake / Issue**: Lacked concrete criteria for evaluating code quality beyond "match existing style", leading to inconsistent architectural decisions.
- **Root Cause**: AGENTS.md §8 had generic coding standards but no specific guidance on complexity control, architectural boundaries, or when to use design patterns.
- **Rule to Prevent**: Reference concrete design principles (SOLID, separation of concerns, composition over inheritance) and pattern categories (Creational, Structural, Behavioral) when reviewing code. Apply the constraint: "No pattern unless it removes an existing pain."
- **Example**: "When reviewing a new feature, check: Does this violate SRP? Are dependencies pointing inward (domain at center)? Is there premature abstraction (YAGNI violation)?"

## Pattern: Security Documentation Consolidation

- **Mistake / Issue**: Security guidance was duplicated across TECH_STACK.md, BACKEND_STRUCTURE.md, and AGENTS.md, leading to inconsistency and maintenance overhead.
- **Root Cause**: No single authoritative source was designated for security requirements.
- **Rule to Prevent**: Designate one document as the single source of truth for each concern. Reference it from other docs rather than duplicating content. Mark clearly: **"AUTHORITATIVE SOURCE: See AGENTS.md §X for complete requirements."
- **Example**: Consolidated all security rules into AGENTS.md §7. TECH_STACK.md and BACKEND_STRUCTURE.md now reference AGENTS.md §7 with only stack-specific implementation examples.

## Pattern: Context Window Management

- **Mistake / Issue**: Agents exceeded context window limits or wasted tokens on irrelevant files, causing truncated responses or lost context.
- **Root Cause**: No explicit guidelines for efficient context usage, summarization, or pruning.
- **Rule to Prevent**: Follow AGENTS.md §11 guidelines: use grep before full reads for large files, summarize after reading >5 files, prune old session logs, and use LSP tools over full-file reads for navigation.
- **Example**: "Instead of reading a 500-line file, use `grep` to find the specific function, then use `lsp_goto_definition` to navigate precisely."

## Pattern: Missing Plans Directory

- **Mistake / Issue**: GUIDELINES.md referenced `/.agents/docs/plans/` for durable task tracking, but the directory didn't exist in the template, causing confusion for new projects.
- **Root Cause**: Template scaffolding was incomplete; referenced directories weren't created.
- **Rule to Prevent**: When documenting directory structures, ensure the template includes `.gitkeep` files for all referenced directories so they're created on project initialization.
- **Example**: Added `template/.agents/docs/plans/.gitkeep` to ensure the plans directory exists when users copy the template.
