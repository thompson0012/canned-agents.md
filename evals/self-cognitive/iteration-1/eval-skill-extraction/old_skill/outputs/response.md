# Skill Proposal: Team Member Onboarding

## Proposed Skill Name

`team-onboarding`

## Description

A repeatable, structured onboarding workflow for new team members that ensures consistency across leads. This skill codifies the 5-step process you've been running manually for the past 6 months so that any team lead can execute it with the same quality and completeness.

## Workflow Steps

### Step 1: Create Dev Environment Access

**Goal**: New team member has full access to all required development tools and environments.

**Actions**:
- [ ] Provision accounts for source control (e.g., GitHub/GitLab org membership)
- [ ] Grant access to CI/CD pipelines
- [ ] Set up cloud/infrastructure credentials (staging, dev environments)
- [ ] Add to relevant communication channels (Slack, Teams, etc.)
- [ ] Provide access to project management tools (Jira, Linear, etc.)
- [ ] Share password manager vault or secrets access as needed
- [ ] Verify the new member can clone repos, run the project locally, and access all dashboards

**Verification**: New member confirms they can clone, build, and run the project end-to-end on their local machine.

### Step 2: Walk Through Codebase Structure

**Goal**: New team member understands the architecture, key modules, and conventions.

**Actions**:
- [ ] Schedule a 60–90 minute walkthrough session
- [ ] Cover high-level architecture (monorepo vs. multi-repo, service boundaries)
- [ ] Walk through directory structure and module responsibilities
- [ ] Explain naming conventions, coding standards, and linting rules
- [ ] Highlight key entry points (API routes, main components, config files)
- [ ] Point out documentation resources (README, CODEMAP, ADRs, wikis)
- [ ] Share any architecture diagrams or system maps
- [ ] Answer initial questions; note any documentation gaps to fix later

**Verification**: New member can verbally describe the high-level architecture and locate key modules without assistance.

### Step 3: Assign First Ticket

**Goal**: New team member has a well-scoped, low-risk first task to build confidence and familiarity.

**Actions**:
- [ ] Select a ticket that is low complexity, well-defined, and low-risk (e.g., a small bug fix, documentation improvement, or minor feature)
- [ ] Ensure the ticket has clear acceptance criteria
- [ ] Provide context on why this ticket matters and where it fits in the product
- [ ] Point to relevant code files and tests as starting points
- [ ] Set a realistic timeline expectation (bias toward generous)
- [ ] Let the new member know it's okay to ask questions — this is a learning task

**Verification**: New member has the ticket assigned, understands the scope, and knows where to start.

### Step 4: Pair on First PR

**Goal**: New team member submits their first pull request with guidance, learning team PR conventions in the process.

**Actions**:
- [ ] Schedule a pairing session (60–90 min) when the new member is ready to work on or submit their PR
- [ ] Walk through the team's PR process (branch naming, commit conventions, PR template)
- [ ] Pair on the implementation — guide, don't take over
- [ ] Review together: tests, linting, CI checks
- [ ] Demonstrate the code review process (how to request reviews, respond to feedback)
- [ ] Help them push and open the PR
- [ ] Ensure at least one other team member also reviews (exposes them to async review culture)

**Verification**: First PR is opened, reviewed, and merged. New member understands the full PR lifecycle.

### Step 5: Schedule 1-on-1 Feedback

**Goal**: Reflect on the onboarding experience, gather feedback, and set expectations for the first 30/60/90 days.

**Actions**:
- [ ] Schedule a 30-minute 1-on-1 within 3–5 days after the first PR merges
- [ ] Ask: What went well? What was confusing? What's still unclear?
- [ ] Share feedback on their first ticket and PR (be specific and encouraging)
- [ ] Discuss team norms, meeting cadence, and communication expectations
- [ ] Set goals or focus areas for the next 2–4 weeks
- [ ] Identify if any additional training or pairing sessions are needed
- [ ] Document any onboarding process improvements suggested by the new member

**Verification**: Both lead and new member leave with clear expectations and documented action items.

---

## Required Context / Inputs

Before executing this skill, the lead needs the following:

| Input | Description | Example |
|-------|-------------|---------|
| **New member name & role** | Who is being onboarded and their position | "Jane Doe, Backend Engineer" |
| **Start date** | When the new member begins | "2026-03-10" |
| **Tool/account list** | Specific tools and platforms requiring access | GitHub, AWS, Linear, Slack |
| **Repo list** | Repositories the member needs access to | `api-service`, `web-app`, `infra` |
| **First ticket candidates** | 2–3 pre-selected low-complexity tickets | "Fix typo in API docs", "Add unit test for X" |
| **Team conventions doc** | Link to coding standards, PR template, branch strategy | `CONTRIBUTING.md`, wiki link |
| **Lead's calendar availability** | Blocked time for walkthrough, pairing, and 1-on-1 | 3 sessions across first week |

## Success Criteria

The onboarding is considered **complete and successful** when:

1. **Access verified** — New member can independently clone, build, run, and deploy (to staging) without assistance
2. **Architecture understood** — New member can describe the system's high-level architecture and navigate the codebase to find relevant code for a given feature area
3. **First contribution merged** — At least one PR has been opened, reviewed, and merged following team conventions
4. **Feedback loop closed** — A 1-on-1 has occurred, mutual feedback has been exchanged, and 30-day goals are documented
5. **Process improved** — Any onboarding friction points raised by the new member are captured as action items for process improvement

## Suggested Timeline

| Day | Step | Duration |
|-----|------|----------|
| Day 1 | Step 1: Dev Environment Access | 2–4 hours |
| Day 1–2 | Step 2: Codebase Walkthrough | 60–90 min |
| Day 2–3 | Step 3: Assign First Ticket | 15–30 min |
| Day 3–5 | Step 4: Pair on First PR | 60–90 min |
| Day 5–7 | Step 5: 1-on-1 Feedback | 30 min |

**Total lead time investment**: ~4–6 hours spread across the first week.

## Notes

- This skill is designed to be **lead-agnostic** — any lead should be able to pick it up and execute it consistently.
- Consider creating a shared checklist (e.g., in your project management tool) from these steps for each new hire so progress is visible to the team.
- After running this 2–3 more times, revisit and refine based on collected feedback from new members.
