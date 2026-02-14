# TECH_STACK.md — Approved Technology Stack

> **STATUS**: TEMPLATE
> **OWNER**: Human (final authority) / AI (maintenance)
> **LAST REVIEWED**: Unknown

**Purpose**: This document tracks the versions and tools approved for use in the project. 

**Discipline**: Adhere to an "unknown until confirmed" policy. Do not assume the existence of tools or libraries unless they are explicitly listed here.

## Frontend

**Guidance**: Focus on accessibility, performance (TTI, bundle size), and clean code patterns.

- Framework: (e.g., React, Vue, Svelte) — *Unknown until confirmed*
- Styling: (e.g., Tailwind CSS, CSS Modules, Styled Components)
- State Management: (e.g., Redux, Zustand, Context API)

## Backend

**Guidance**: Prioritize security, scalability, and maintainable API patterns.

- Runtime: (e.g., Node.js, Python, Go) — *Unknown until confirmed*
- Framework: (e.g., Express, FastAPI, NestJS)
- Authentication: (e.g., JWT, OAuth2, Session-based)

## Database / Infrastructure

- Database: (e.g., PostgreSQL, MongoDB, Redis)
- Hosting: (e.g., AWS, Vercel, Railway)
- ORM/Client: (e.g., Prisma, Mongoose, Kysely)

## Dev Tools

- Linter: (e.g., ESLint, Biome)
- Formatter: (e.g., Prettier)
- Test Runner: (e.g., Vitest, Jest, Pytest)
- Documentation: (e.g., TSDoc, JSDoc)

## Prohibited / Deprecated

**Guidance**: Avoid these tools to prevent technical debt or security vulnerabilities.

- (Example): Libraries with known critical vulnerabilities or those that have been officially deprecated.
- (Example): Tools that do not align with the project's performance or accessibility goals.

## Security Guidance

**Purpose**: Defense-in-depth security requirements.

> **AUTHORITATIVE SOURCE**: All security rules are defined in `AGENTS.md` §7 (Defense-in-Depth Security). This section provides stack-specific implementation examples only.

### Implementation Examples (by Stack)

#### Secrets Management
- ✅ Use environment variables: `process.env.API_KEY`
- ❌ Hardcoded secrets: `const apiKey = "sk_live_12345"`

#### Input Validation
- ✅ Use schema validation libraries (e.g., Zod, Joi, Marshmallow)
- ❌ Trusting client-side validation only

See `AGENTS.md` §7 for complete security requirements.

## Test Strategy (Consolidated)

**Note**: Core testing rules reside in `AGENTS.md` §4 (Error Protocol) and §7 (Engineering & Coding Standards — Test Default).

### Test-First for Bugs (Per AGENTS.md §4)

- **Every bug must have a failing test** before the fix
- **Minimal Reproduction**: Simplest test that proves the bug exists
- **No Paper-Overs**: Tests must verify actual behavior, not mask failures

### Test Pyramid (Target Distribution)

| Type | Coverage Target | Purpose |
|------|-----------------|---------|
| **Unit** | 70% | Logic, utilities, pure functions |
| **Integration** | 20% | API endpoints, database interactions |
| **E2E** | 10% | Critical user flows (auth, checkout, etc.) |

### Coverage Minimums

- **New Code**: 80% minimum (exceptions require explicit approval)
- **Bug Fixes**: 100% (reproduction test is mandatory)
- **Refactors**: Maintain or improve existing coverage

### Test Organization

#### Frontend (from FRONTEND_GUIDELINES.md)
- Component tests: Co-located with components (`Button.test.tsx`)
- Integration tests: `/tests/integration/`
- E2E tests: `/tests/e2e/`
- Use the test runner defined in TECH_STACK.md (example: Vitest)

#### Backend (from BACKEND_STRUCTURE.md)
- Unit tests: Co-located with modules (`auth.test.ts`)
- API tests: `/tests/api/`
- Database tests: Use test DB with migrations

### When to Skip Tests

Per AGENTS.md §7 "Test Default":
- ✅ User explicitly opts out
- ✅ Prototype/throwaway code (mark with `@temporary`)
- ❌ "I'll add tests later" (not acceptable)
- ❌ "It's too simple to test" (write the test anyway)

### CI/CD Integration

- All tests must pass before merge (per `AGENTS.md` §9 Completion Checklist)
- Coverage reports generated on every PR
- Flaky tests are treated as failures: fix the root cause, or quarantine with explicit approval (and a tracked follow-up). Do not delete tests just to make CI pass.

### Examples

#### Good Test (Minimal Reproduction)
```typescript
test('bug: user profile API returns 500 on empty bio', async () => {
  const user = await createUser({ bio: '' });
  const response = await api.getProfile(user.id);
  expect(response.status).toBe(200); // Should be 200, not 500
});
```

#### Bad Test (Not Actually Testing Anything)
```typescript
test('profile works', async () => {
  const result = await getProfile();
  expect(result).toBeTruthy(); // ❌ What does this prove?
});
```
