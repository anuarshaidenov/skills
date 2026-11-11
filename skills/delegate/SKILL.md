---
name: delegate
description: Split a task into sub-tasks and delegate them to subagents, each on a model and effort level sized to its complexity. Use when the user asks to delegate, split up or fan out work, or when a task spans several independent pieces.
---

# Delegate

1. **Split.** Break the task into sub-tasks and mark the dependencies between them. Done when every part of the request belongs to exactly one sub-task.

2. **Size.** Give each sub-task an owner, and each subagent the cheapest `model` and `effort` that gets it right first time:

   | Sub-task                                                                                                                         | Owner                               | `model`    | `effort`   |
   | -------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------- | ---------- | ---------- |
   | A handful of tool calls, tightly coupled to what you already hold in context, or the judgment call the rest of the task hangs on | you, inline                         | —          | —          |
   | Lookup with a checkable answer: find-and-report, grep across named files, list the call sites                                    | `Explore` subagent                  | `"haiku"`  | `"medium"` |
   | Reading that takes judgment (trace a flow, compare options, research docs), or an edit pattern spelled out in full               | subagent (`Explore` when read-only) | `"sonnet"` | `"medium"` |
   | Ordinary implementation against a clear spec                                                                                     | subagent                            | `"opus"`   | `"medium"` |
   | Tricky logic, debugging, or a design call inside a well-specified piece                                                          | subagent                            | `"opus"`   | `"high"`   |

   Pass both parameters on every Agent call: an omitted `model` runs on yours. A sub-task between two rows takes the higher one, since a failed cheap run costs its tokens plus the redo. Cap each `model` at the one you are running on.

   Show the user the split as a short list (sub-task → owner, model, effort), then carry on without waiting.

3. **Launch.** Brief each subagent cold: goal, files to read, scope, what to report back. Lookup agents report paths and line numbers, so you can spot-check the answer. Writing agents get disjoint sets of files; sub-tasks that touch the same file go to one agent or run in sequence. Launch dependent sub-tasks as their inputs arrive.

4. **Integrate.** Read each agent's actual changes, not only its summary. Fix what falls short yourself, or re-brief it one row up. Then run the project's checks. Done when every sub-task is verified against its goal and the checks pass, or their failures are reported to the user verbatim.
