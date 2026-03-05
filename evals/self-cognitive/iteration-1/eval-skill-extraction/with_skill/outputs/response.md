## Goal
Extract the user's 5-step team onboarding process into a persistent, repeatable skill that any team lead can execute consistently.

## Current context
- User has been running a manual 5-step onboarding process for 6 months.
- The process is currently tribal knowledge held by one person.
- Steps are: (1) Create dev environment access, (2) Walk through codebase structure, (3) Assign first ticket, (4) Pair on first PR, (5) Schedule 1-on-1 feedback.
- Goal is consistency across any lead who runs it, not just the original author.
- No existing skill file covers this workflow.

## Verification
### Assumptions
- The 5 steps are sequential and must be executed in order (e.g., environment access before codebase walkthrough).
- "Dev environment access" includes tooling, repo permissions, and CI/CD — specifics are UNKNOWN and need the user to fill in.
- "First ticket" implies a curated, scoped-down starter task — selection criteria are UNKNOWN.
- The process applies to all new engineers regardless of seniority level.
- No automation tooling (e.g., scripts, Terraform, IDP) currently exists for step 1.

### Risks and failure modes
- **Incomplete step definitions**: Steps are high-level summaries; a different lead may interpret "walk through codebase structure" differently without a checklist.
- **Environment access drift**: Tooling and permissions change over time; the skill will go stale if not maintained.
- **Missing exit criteria**: Without clear "done" signals per step, leads may skip or shortcut steps.
- **Seniority mismatch**: A senior hire may not need the same depth of pairing; the skill should note when to adapt.
- **Sensitive data**: Step 1 likely involves credentials/secrets — the skill must not embed actual secrets or access tokens.

### Checks
- Confirm with user that the 5 steps are strictly sequential.
- Confirm whether the skill should include seniority-based variations.
- Validate that each step has a clear exit criterion before finalizing.
- Verify no secrets or credentials are embedded in the skill file.
- Test the draft skill by having a second lead run it on the next hire and collecting feedback.

### Confidence
- Level: medium
- Why: The 5 steps are clearly stated from 6 months of practice, giving strong signal on the workflow shape. However, each step lacks the sub-tasks, checklists, and exit criteria needed for someone unfamiliar to execute independently.
- To raise confidence:
  - User provides sub-steps and exit criteria for each of the 5 steps.
  - A second lead dry-runs the skill draft and reports gaps.
  - User confirms whether seniority-based branching is needed.

## Lessons learned
### Technical
- Onboarding workflows touch security-sensitive resources (repo access, CI/CD tokens); any skill covering these must explicitly prohibit embedding secrets.

### Process
- A 5-step verbal process is a strong starting point, but repeatability requires explicit exit criteria per step — otherwise different leads will define "done" differently.
- Six months of manual repetition is a reliable signal that a process is stable enough to codify.

### Prompting and coordination
- When a user says "make this repeatable," default to Skill Extraction mode and propose a new skill outline rather than just documenting the steps in prose.
- Ask for sub-steps and exit criteria upfront to avoid a second round-trip.

## Persistable updates
### Memory summary
- User preferences:
  - Wants repeatable, lead-agnostic processes.
  - Values consistency across team members executing the same workflow.
- Project facts:
  - Team has a 5-step onboarding process used for 6 months.
  - No existing automation or skill file for onboarding.
- Open questions:
  - What specific tools/permissions are included in "dev environment access"?
  - Are there seniority-based variations to any step?
  - What does a good "first ticket" look like (size, label, complexity)?
  - What is the expected timeline from step 1 to step 5 (days/weeks)?
  - Who owns maintaining this skill when tooling changes?

### Skill update proposal
No target skill exists. Proposing a new skill:

**Proposed skill file: `team-onboarding.md`**

```markdown
---
name: team-onboarding
description: Use when onboarding a new team member. Ensures consistent execution of the 5-step onboarding process by any team lead.
---

# Team Onboarding Skill

## Overview
A repeatable 5-step checklist for onboarding new engineers, designed so any lead can execute it consistently.

## When to Use
- A new team member is joining.
- Any lead (not just the original author) is running onboarding.

## Steps

### Step 1: Create Dev Environment Access
- **Actions**:
  - [ ] Grant repository access (GitHub/GitLab org + specific repos)
  - [ ] Provision CI/CD credentials (UNKNOWN — fill in specifics)
  - [ ] Set up local dev environment (UNKNOWN — link to setup guide)
  - [ ] Verify the new member can clone, build, and run tests locally
- **Exit criterion**: New member has pushed a trivial change (e.g., README typo) to a feature branch and CI passes.
- **Security note**: Never embed credentials in this file. Use your org's secrets manager.

### Step 2: Walk Through Codebase Structure
- **Actions**:
  - [ ] Review high-level architecture (CODEMAP.md or equivalent)
  - [ ] Walk through key directories and entry points
  - [ ] Explain conventions (naming, testing, PR standards)
  - [ ] Point to relevant docs (FRONTEND.md, BACKEND.md, etc.)
- **Exit criterion**: New member can describe the system's major modules and data flow in their own words.

### Step 3: Assign First Ticket
- **Actions**:
  - [ ] Select a scoped, well-defined starter ticket (UNKNOWN — define selection criteria)
  - [ ] Ensure the ticket has clear acceptance criteria
  - [ ] Walk through the ticket together before they start
- **Exit criterion**: New member has a ticket assigned and can articulate what "done" looks like.

### Step 4: Pair on First PR
- **Actions**:
  - [ ] Schedule a pairing session for the first pull request
  - [ ] Let the new member drive; lead observes and coaches
  - [ ] Review PR conventions (size, description, tests, review process)
  - [ ] Merge the PR together
- **Exit criterion**: First PR is merged with passing CI and at least one approval.

### Step 5: Schedule 1-on-1 Feedback
- **Actions**:
  - [ ] Schedule a 1-on-1 within the first week after PR merge
  - [ ] Discuss: What went well? What was confusing? What's missing?
  - [ ] Capture action items for improving the onboarding process
  - [ ] Update this skill file if gaps are identified
- **Exit criterion**: Feedback session completed and action items logged.

## Adaptation Notes
- For senior hires, steps 2-4 may be lighter; adjust depth based on experience.
- For interns or junior engineers, consider extending step 4 to multiple PRs.

## Maintenance
- Review this skill quarterly or after every 3rd onboarding.
- Owner: UNKNOWN — assign a team lead to keep this current.
```

**Frontmatter suggestions:**
- `name`: `team-onboarding`
- `description`: `Use when onboarding a new team member. Ensures consistent execution of the 5-step onboarding process by any team lead.`

### Artifacts JSON
```json
{
  "self_cognitive_meta": {
    "goal": "Extract the user's 5-step team onboarding process into a persistent, repeatable skill that any team lead can execute consistently.",
    "context_facts": [
      "User has been running a manual 5-step onboarding process for 6 months.",
      "The process is tribal knowledge held by one person.",
      "Steps: (1) Create dev environment access, (2) Walk through codebase structure, (3) Assign first ticket, (4) Pair on first PR, (5) Schedule 1-on-1 feedback.",
      "Goal is consistency across any lead who runs it.",
      "No existing skill file covers this workflow."
    ],
    "verification": {
      "assumptions": [
        "The 5 steps are sequential and must be executed in order.",
        "Dev environment access includes tooling, repo permissions, and CI/CD — specifics UNKNOWN.",
        "First ticket implies a curated, scoped-down starter task — selection criteria UNKNOWN.",
        "The process applies to all new engineers regardless of seniority.",
        "No automation tooling currently exists for step 1."
      ],
      "risks": [
        "Incomplete step definitions — different leads may interpret steps differently without checklists.",
        "Environment access drift — tooling changes will make the skill stale.",
        "Missing exit criteria — leads may skip or shortcut steps.",
        "Seniority mismatch — senior hires may not need the same depth.",
        "Security — step 1 involves credentials; skill must not embed secrets."
      ],
      "checks": [
        "Confirm the 5 steps are strictly sequential.",
        "Confirm whether seniority-based variations are needed.",
        "Validate each step has a clear exit criterion.",
        "Verify no secrets or credentials are embedded in the skill file.",
        "Have a second lead dry-run the skill on the next hire."
      ],
      "confidence": {
        "level": "medium",
        "why": "The 5 steps are clearly stated from 6 months of practice, but each step lacks sub-tasks, checklists, and exit criteria needed for independent execution by an unfamiliar lead.",
        "to_raise": [
          "User provides sub-steps and exit criteria for each of the 5 steps.",
          "A second lead dry-runs the skill draft and reports gaps.",
          "User confirms whether seniority-based branching is needed."
        ]
      }
    },
    "lessons_learned": {
      "technical": [
        "Onboarding workflows touch security-sensitive resources; skills covering these must prohibit embedding secrets."
      ],
      "process": [
        "Repeatability requires explicit exit criteria per step — otherwise different leads define done differently.",
        "Six months of manual repetition is a reliable signal that a process is stable enough to codify."
      ],
      "prompting_and_coordination": [
        "When a user says 'make this repeatable,' default to Skill Extraction mode and propose a new skill outline.",
        "Ask for sub-steps and exit criteria upfront to avoid a second round-trip."
      ]
    },
    "memory_summary": {
      "user_preferences": [
        "Wants repeatable, lead-agnostic processes.",
        "Values consistency across team members executing the same workflow."
      ],
      "project_facts": [
        "Team has a 5-step onboarding process used for 6 months.",
        "No existing automation or skill file for onboarding."
      ],
      "open_questions": [
        "What specific tools/permissions are included in 'dev environment access'?",
        "Are there seniority-based variations to any step?",
        "What does a good 'first ticket' look like (size, label, complexity)?",
        "What is the expected timeline from step 1 to step 5?",
        "Who owns maintaining this skill when tooling changes?"
      ]
    },
    "skill_updates": {
      "target_skill_name": null,
      "proposed_patch": null,
      "new_skill_drafts": [
        "team-onboarding"
      ]
    }
  }
}
```
