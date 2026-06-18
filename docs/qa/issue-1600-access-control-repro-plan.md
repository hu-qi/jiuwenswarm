# Issue #1600 Access-control Reproduction Plan

> Status: MVP QA plan for issue #1600. This document does not change runtime behavior. It defines how to reproduce and verify access-control rule effectiveness.

## 1. Background

Issue #1600 reports that configured rules are not taking effect. Related issues also mention file protection rules and blocked command rules. Before changing core logic, the project needs a repeatable reproduction matrix.

## 2. Goal

- Confirm whether rules are loaded.
- Confirm whether rules are enforced in backend execution paths.
- Confirm whether UI confirmation can bypass backend policy.
- Confirm expected behavior for allowed, denied, and confirmation-required operations.

## 3. Test Matrix

| Case | Rule | Operation | Expected Result |
|---|---|---|---|
| A1 | allow read in workspace | Read allowed file | Success |
| A2 | deny read outside workspace | Read protected file | Blocked |
| A3 | deny write outside workspace | Write protected file | Blocked |
| A4 | deny command | Execute blocked command | Blocked |
| A5 | confirmation required | Execute sensitive operation | Requires confirmation |
| A6 | backend deny plus UI confirm | Confirm from UI | Still blocked by backend |
| A7 | missing policy file | Start service | Warning or safe default |
| A8 | invalid policy syntax | Start service | Clear error or safe fallback |

## 4. Reproduction Steps

1. Start JiuwenSwarm with a minimal model configuration.
2. Add an access-control rule that denies reading a selected path.
3. Ask the agent to read that path.
4. Record whether the operation is blocked before execution.
5. Repeat with write operation.
6. Add a blocked command rule.
7. Ask the agent to execute the command.
8. Try to confirm the operation from UI if confirmation is shown.
9. Confirm backend still rejects denied operations.
10. Save logs and screenshots for each case.

## 5. Evidence to Collect

- Runtime configuration file.
- Service startup logs.
- Request payload for the attempted capability call.
- Backend decision result.
- UI confirmation state, if applicable.
- Final user-facing response.
- Error message and stack trace if any.

## 6. Expected Enforcement Principle

Access control should be enforced in the backend execution path. UI confirmation may authorize operations that require confirmation, but it must not override explicit deny rules.

Recommended precedence:

```text
deny > require_confirmation > allow
```

## 7. Acceptance Criteria for Fix

- Denied file read or write operations are blocked.
- Denied commands are blocked.
- UI confirmation cannot bypass backend deny rules.
- Policy loading errors are visible.
- Regression cases are documented or automated.
