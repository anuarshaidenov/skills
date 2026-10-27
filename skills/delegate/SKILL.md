---
name: delegate
description: Split a task into sub-tasks and delegate them to subagents, each at an effort level sized to its complexity. Use when the user asks to delegate, split up or fan out work, or when a task spans several independent pieces.
disable-model-invocation: true
---

# Delegate

1. **Split.** Break the task into sub-tasks and mark the dependencies between them. Done when every part of the request belongs to exactly one sub-task.

2. **Size.** Give each sub-task an owner and an effort:

   | Sub-task | Owner | `effort` |
   |---|---|---|
   | A handful of tool calls, or tightly coupled to what you already hold in context | you, inline | — |
   | Mechanical and self-checking: find-and-report, grep across named files, an edit pattern spelled out | subagent (`Explore` when read-only) | `"lower"` |
   | Ordinary implementation against a clear spec | subagent | omit |
   | Tricky logic, debugging, or a design call inside a well-specified piece | subagent | `"higher"` |

   Show the user the split as a short list (sub-task → owner, effort), then carry on without waiting.

3. **Launch.** Brief each subagent cold: goal, files to read, scope, what to report back. Writing agents get disjoint sets of files; sub-tasks that touch the same file go to one agent or run in sequence. Launch dependent sub-tasks as their inputs arrive.

4. **Integrate.** Read each agent's actual changes, not only its summary, and fix or re-brief what falls short. Then run the project's checks. Done when every sub-task is verified against its goal and the checks pass, or their failures are reported to the user verbatim.
