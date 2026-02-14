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

## Pattern: Input Validation Bypass

- **Mistake / Issue**: Malformed user input caused unexpected application crashes.
- **Root Cause**: Client-side validation was trusted without server-side verification.
- **Rule to Prevent**: Always validate and sanitize all inputs on the server, regardless of client-side validation.
- **Example**:
```typescript
// Before
app.post('/api/users', (req, res) => {
  const user = await db.createUser(req.body); // No validation!
});

// After
const userSchema = z.object({
  email: z.string().email(),
  age: z.number().min(0).max(150)
});

app.post('/api/users', (req, res) => {
  const validData = userSchema.parse(req.body);
  const user = await db.createUser(validData);
});
```

## Pattern: Missing Error Boundaries

- **Mistake / Issue**: A single component error crashed the entire application.
- **Root Cause**: No error boundaries were implemented to isolate component failures.
- **Rule to Prevent**: Wrap critical component trees with error boundaries to prevent cascading failures.
- **Example**:
```tsx
// Before
<App>
  <Header />
  <MainContent /> {/* Crashes here = whole app crashes */}
  <Footer />
</App>

// After
<App>
  <Header />
  <ErrorBoundary fallback={<ErrorPage />}>
    <MainContent />
  </ErrorBoundary>
  <Footer />
</App>
```

## Pattern: Inefficient Database Queries

- **Mistake / Issue**: API endpoints became slow under load due to N+1 query problems.
- **Root Cause**: Loading related data in a loop instead of using eager loading or joins.
- **Rule to Prevent**: Use eager loading or join queries when fetching related data.
- **Example**:
```typescript
// Before - N+1 problem
const users = await db.users.findAll();
for (const user of users) {
  user.orders = await db.orders.find({ userId: user.id }); // N queries!
}

// After - Single query with join
const users = await db.users.findAll({
  include: [{ model: db.orders }]
});
```
