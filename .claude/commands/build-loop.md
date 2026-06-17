Run a builder-checker loop to reach a green test suite.

## Inputs

$ARGUMENTS contains the task description (e.g. "implement login page" or "fix all failing tests").
If empty, default to "fix all failing tests".

## Protocol

```
cycle = 0
previous_failing = null

WHILE cycle < 5:
  cycle += 1

  [BUILDER STEP]
  Spawn the builder subagent (@builder) with:
    - The original task: $ARGUMENTS
    - current_failing (null on cycle 1, otherwise the list from the last checker run)
    - The last checker's FULL_OUTPUT (null on cycle 1)
    - "This is cycle N of 5."

  [CHECKER STEP]
  Spawn the checker subagent (@checker) with no arguments.
  Parse its structured report.

  [STOP RULES — check in this order]
  1. BROKEN_RUNNER → stop immediately, report the runner error.
  2. Tampering check → if checker reports test files were modified, stop and report tampering.
  3. PASS → report success and stop.
  4. Stuck check → if current FAILING_TESTS == previous_failing (same identifiers,
     same files), stop and surface the blocker.
  5. Cycle cap → if cycle == 5 and still FAIL, stop and report what's still failing.

  previous_failing = current FAILING_TESTS
  CONTINUE to next cycle
```

## Final Report

Always end with:

```
=== LOOP RESULT ===
STATUS: PASSED | FAILED | STOPPED_EARLY
CYCLES USED: N / 5
STOP REASON: (green | cycle_cap | stuck | broken_runner | tampering)

REMAINING FAILURES:
- (list failing tests if not green, else "none")

BLOCKER DETAIL:
- (explanation of why the loop stopped early, if applicable)
```
