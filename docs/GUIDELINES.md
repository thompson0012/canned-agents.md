<!-- TEMPLATE: This is a canned template for AI agent documentation kits. Replace placeholders with project-specific content. -->

# GUIDELINES.md — How to Write & Maintain Project Documentation

_Purpose: Teach the coding agent exactly how to create, update, and structure every key document referenced in `AGENTS.md`._

## General principles

- Keep documents concise yet complete — aim for clarity over length.
- Use Markdown with semantic headings (`##`, `###`), bullet lists, code blocks, tables, and XML-like tags only when they improve readability.
- Version control everything — commit changes with descriptive messages.
- Traceability — every statement should be grounded in reality (code, user requirements, decisions). No hallucinations.
- Human review gate — after proposing major updates to any doc, always wait for user approval before considering it final.
- Iterate ruthlessly — after every correction or new insight, propose precise updates to the relevant file.

## Document-specific guidelines

### PROGRESS.md (Session / Project State Log)
- Purpose — Carry-over lightweight state between sessions.
- Structure:
  - Current date / session summary at top
  - `## Completed This Session`
  - `## Still In Progress / Next Up`
  - `## Blockers / Open Questions`
  - `## Quick Notes / Metrics`
- Tone — Telegraphic, bullet-heavy, developer shorthand.

### PRD.md (Product Requirements Document)
- Purpose — Single source of truth for what the product should do, why, and for whom.
- Structure:
  1. `## Overview`
  2. `## Goals & Success Metrics`
  3. `## User Personas`
  4. `## Features & Requirements` (MoSCoW)
  5. `## Non-Functional Requirements`
  6. `## Assumptions & Risks`
  7. `## Out of Scope`

### IMPLEMENTATION_PLAN.md
- Purpose — Locked roadmap of phases, tasks, and decisions.
- Structure:
  1. `## Current Milestone`
  2. `## Phases`
  3. `## Open Decisions`
  4. `## Task Breakdown`
  5. `## Dependencies & Blockers`
  6. `## Risks & Mitigations`

### LESSONS.md
- Purpose — Prevent repeat mistakes; self-improvement log.
- Structure:
  - Date / Session
  - Mistake / Issue
  - Root Cause
  - Rule to Prevent
  - Example

### TECH_STACK.md
- Purpose — Exact versions and allowed tools/libraries.
- Structure:
  - `## Frontend`
  - `## Backend`
  - `## Database / Infra`
  - `## Dev Tools`
  - `## Prohibited / Deprecated`

### DESIGN_SYSTEM.md
- Purpose — Visual & interaction standards.
- Structure:
  - `## Tokens`
  - `## Components`
  - `## Motion & Accessibility`
  - `## Icons & Assets`

### APP_FLOW.md
- Purpose — High-level user journeys & screen flows.
- Structure:
  - `## Main Flows`
  - `## Edge Cases & Errors`
  - Use simple text-based diagrams (ASCII or Mermaid if supported).

### FRONTEND_GUIDELINES.md & BACKEND_STRUCTURE.md
- Purpose — Deeper, example-heavy rules.
- Structure:
  1. `## Preferred Patterns`
  2. `## Do / Don’t Table`
  3. `## Folder & Naming Conventions`
  4. `## Common Gotchas & Anti-patterns`
  5. `## Example Files`

### TASKS.md (Session & Medium-term Task List)
- Purpose — Concrete, actionable checklist.
- Structure:
  - Use Markdown task lists: `- [ ]` / `- [x]`
  - Group by phase or priority.

### MEMORY.md (Long-term Architectural / Decision Log)
- Purpose — Stable, rarely changing knowledge.
- Structure:
  1. `## Architectural Decisions` (ADR-style)
  2. `## Adopted Patterns`
  3. `## Domain Glossary`
  4. `## Historical Context`

## Update protocol
1. Propose full updated file with changes highlighted.
2. Ask for approval.
3. Confirm commitment.

## Core rules
- Never invent content.
- Consistent language & formatting.
- Prefer tables and code blocks.
- End update with: “This document is now locked until next approved change.”
