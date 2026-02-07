
# AGENTS.md — Entrypoint Constitution

This is a canned documentation kit for AI agents. The canonical detailed constitution is at `/docs/AGENTS.md`.

**Full constitution**: See [/docs/AGENTS.md](/docs/AGENTS.md) for the complete detailed rules.
**Doc Standards**: See [/docs/GUIDELINES.md](/docs/GUIDELINES.md) for documentation standards.

---

## 1. Security Non-Negotiables
- **No Secrets**: Never output, log, or commit API keys, passwords, or PII.
- **No Unsafe Patterns**: Avoid `eval()`, hardcoded credentials, or unparameterized queries.
- **Input Validation**: Treat all external data as untrusted.

## 2. Execution Discipline
- **No Hallucinations**: Stick strictly to documented reality. If a document is missing or ambiguous, do not assume—ask.
- **Approval Gate**: Do not implement any code change without an explicit, approved plan from the user.
- **Small Steps**: Execute in small, reversible increments. Verify after each step.

## 3. Missing Documentation Protocol
- If a referenced document (e.g., `TECH_STACK.md`, `PRD.md`) is missing:
  1. Stop and notify the user.
  2. Ask to scaffold the missing document or proceed with minimal, safe defaults.
  3. Record the gap in your task list.

## 4. Documentation Conventions
- All canonical documentation lives under the `/docs` directory.
- Use absolute repository paths when linking to files (e.g., `[/docs/FILE.md](/docs/FILE.md)`).
- Maintain documentation as you work (e.g., `progress.txt`, `LESSONS.md`).

## 5. Coding Rituals
- **Match Style**: Always mirror existing naming, structure, and import patterns.
- **Test-First**: Write reproduction tests for bugs before fixing them.
- **LSP Check**: Ensure `lsp_diagnostics` are clean before completion.

---

_This entrypoint provides the non-negotiable safety guardrails. Refer to the full constitution for comprehensive workflow and engineering standards._


