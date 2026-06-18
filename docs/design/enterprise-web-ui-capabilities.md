# Enterprise Web UI Capabilities Spec

> Status: MVP design draft for issue #1595.

## 1. Background

Enterprise users need direct visibility and control over autonomous-agent capabilities. The Web UI should expose scheduled tasks, skill list, and memory management as first-class features instead of hiding them behind configuration files or backend-only operations.

## 2. Goals

- Let users create, view, pause, resume, and delete scheduled tasks.
- Let users view installed skills and understand what each skill can do.
- Let users add, edit, disable, and delete memory entries.
- Make policy scope and risk level visible before a capability runs.
- Provide enough state and logs for troubleshooting.

## 3. Non-goals

- This spec does not define the final backend schema.
- This spec does not implement HA scheduling.
- This spec does not replace the enterprise administrator policy model.

## 4. Navigation

Recommended enterprise navigation:

```text
Enterprise Console
├── Conversations
├── Scheduled Tasks
├── Skills
├── Memory
├── Policy
└── System Status
```

## 5. Scheduled Tasks Page

### List View

Columns:

| Field | Description |
|---|---|
| Name | Task title |
| Status | Enabled, paused, failed, completed |
| Schedule | Trigger expression or readable schedule |
| Owner | Creator or workspace |
| Last run | Last execution time |
| Next run | Next planned execution time |
| Actions | View, edit, pause or resume, run once, delete |

### Create and Edit Form

Fields:

- Task name.
- Prompt.
- Schedule type: one-time, daily, weekly, cron-like advanced.
- Timezone.
- Execution mode.
- Required skill or agent, optional.
- Runtime policy.
- Failure retry policy.
- Notification target.

### Run Detail

Show:

- Trigger time.
- Input prompt.
- Execution trace.
- Capability calls.
- Result summary.
- Error logs.

## 6. Skills Page

### List View

Columns:

| Field | Description |
|---|---|
| Skill name | Display name |
| Version | Installed version |
| Source | Built-in, local, remote, marketplace |
| Status | Enabled or disabled |
| Scope | Required capability scope |
| Last updated | Update time |

### Detail View

Show:

- Description.
- Usage examples.
- Required capabilities.
- Risk level.
- Configuration fields.
- Run test entry.

## 7. Memory Page

### List View

Columns:

| Field | Description |
|---|---|
| Content | Memory summary |
| Scope | Personal, team, workspace |
| Status | Enabled or disabled |
| Source | User-created, system-created, imported |
| Updated at | Last update time |

### Create and Edit Form

Fields:

- Memory content.
- Scope.
- Tags.
- Expiration or review date.
- Enable or disable switch.

## 8. Policy Display

Before a task, skill, or memory operation executes, the UI should show:

- What capability will run.
- Which capability scope may be used.
- Which file or resource boundary applies.
- Whether confirmation is required.
- Whether a backend policy can block the operation.

Important rule: UI confirmation must not bypass backend policy. The backend remains the final enforcement point.

## 9. Mock API Contract

```ts
type ScheduledTask = {
  id: string
  name: string
  prompt: string
  schedule: string
  timezone: string
  status: 'enabled' | 'paused' | 'failed' | 'completed'
  owner: string
  lastRunAt?: string
  nextRunAt?: string
}

type Skill = {
  id: string
  name: string
  version: string
  source: 'builtin' | 'local' | 'remote' | 'marketplace'
  status: 'enabled' | 'disabled'
  scope: string[]
  updatedAt: string
}

type MemoryEntry = {
  id: string
  content: string
  scope: 'personal' | 'team' | 'workspace'
  status: 'enabled' | 'disabled'
  tags: string[]
  updatedAt: string
}
```

## 10. Acceptance Criteria

- Users can see scheduled tasks, skills, and memory from the main navigation.
- Users can create and edit scheduled tasks from the UI.
- Users can view skill capability scope before running a skill.
- Users can add and disable memory entries.
- UI confirmation cannot bypass backend policy.
- Empty, loading, failure, and policy-denied states are handled.
