# SunStays Admin Panel

## Test Command
```
npm test -- --ci --passWithNoTests 2>&1
```

## Multi-Agent Loop Rules

These rules apply to every agent in this project and are non-negotiable:

- **Builder agents** must never touch files matching `**/*.test.*`, `**/*.spec.*`, or anything inside `__tests__/` directories.
- **Checker agents** have read-only access to the whole repo. They run the test suite and report output — nothing else.
- A **"stuck" condition** is when the exact same set of failing test identifiers appears in two consecutive checker cycles without change.
- The **loop cap** is 5 builder-checker cycles. If the suite is not green by then, stop and escalate.
- Never delete, skip, or weaken a test assertion to make a test pass. If a test is wrong, surface it as a blocker — don't silently remove it.
