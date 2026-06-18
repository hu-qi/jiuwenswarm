# Enterprise User Guide

> Status: MVP draft for issue #1594. This guide focuses on user-facing enterprise features and should be updated as the enterprise Web UI stabilizes.

## 1. Overview

JiuwenSwarm helps users complete complex tasks through multi-agent collaboration, skill reuse, tool invocation, and natural-language workflows. In enterprise scenarios, users also need visible controls for model configuration, scheduled tasks, skills, memory, and access policies.

## 2. First-time Setup

1. Open the JiuwenSwarm frontend.
2. Configure a model provider.
3. Verify connection.
4. Select an execution mode.
5. Start a simple conversation.
6. Run a Swarm task to verify multi-agent collaboration.

## 3. Execution Modes

| Mode | Use Case |
|---|---|
| Plan Mode | Complex tasks that need step-by-step planning and confirmation |
| Performance Mode | Simple tasks that need faster execution |
| Swarm Mode | Complex tasks requiring multiple specialized agents |

## 4. Skills

Users can use skills to reuse task capabilities. Enterprise UI should make the following visible:

- Installed skills.
- Skill description and version.
- Skill source.
- Enable or disable state.
- Required capability scope.
- Last update time.

## 5. Memory

Memory should help JiuwenSwarm remember stable user or organization preferences. Enterprise UI should support:

- Adding memory.
- Viewing memory entries.
- Editing or disabling memory.
- Deleting obsolete memory.
- Showing memory scope, such as personal, team, or workspace.

## 6. Scheduled Tasks

Scheduled tasks should be visible to users and administrators.

Recommended fields:

| Field | Description |
|---|---|
| Task name | Human-readable title |
| Prompt | Instruction to execute |
| Schedule | One-time or recurring trigger |
| Owner | User or workspace owner |
| Status | Enabled, paused, failed, completed |
| Last run | Last execution time |
| Next run | Next planned execution time |
| Run history | Success, failure, and logs |

## 7. Policy Visibility

Enterprise users should understand that some operations may require confirmation or may be limited by workspace policy. Examples:

- File read and write boundaries.
- Command execution restrictions.
- Integration scope.
- Data modification actions.
- Operations requiring user confirmation.

## 8. Troubleshooting

| Problem | Suggested Action |
|---|---|
| Model connection fails | Check provider and base URL |
| Skill cannot run | Check whether required capabilities are enabled |
| Scheduled task does not trigger | Check task status, timezone, and scheduler service |
| Memory not used | Check memory scope and whether the entry is enabled |
| File access is unexpectedly allowed | Contact administrator and verify policy enforcement |

## 9. TODO

- Add screenshots after enterprise UI is stable.
- Add administrator guide.
- Add role and policy matrix.
- Add examples for common enterprise workflows.
