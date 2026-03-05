# Skill Optimization Design

**Date**: 2026-03-05
**Status**: Draft (user-approved)
**Scope**: Optimize five skills with separate eval suites: seedance2.0-prompt-skill, self-cognitive, meta-prompting, domain-expert-consultation, context-compaction.

## Goal
Improve the quality and triggering reliability of each listed skill using the full skill-creator evaluation loop with separate eval suites per skill.

## Non-Goals
- Do not modify project PRD/TECH_STACK (templates remain unchanged).
- Do not batch multiple skills into a single eval suite.

## Approach
For each skill, sequentially:
1. Create 2–3 realistic eval prompts that exercise edge cases and trigger conditions.
2. Snapshot the current skill as baseline.
3. Run baseline and with-skill subagents in parallel for each prompt.
4. Draft assertions while runs execute; grade and aggregate benchmark results.
5. Generate the eval viewer for human review.
6. Apply revisions and repeat until feedback is satisfied.

## Success Criteria
- Each skill has at least one eval iteration with documented baseline vs. with-skill outputs.
- Human review completed via eval viewer; feedback integrated.
- Final skill revisions reflect user-approved improvements.

## Risks
- Overfitting to limited prompts. Mitigation: keep prompts realistic and minimal, expand only if needed.
- Cross-skill confusion. Mitigation: separate eval suites and sequential execution.

## Verification
- Benchmark artifacts produced for each iteration.
- Viewer generated for each skill iteration prior to revisions.
