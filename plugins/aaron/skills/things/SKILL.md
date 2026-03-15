---
name: things
description: "Manage Aaron's Things 3 task system via the Things MCP connector. Use when triaging inbox, categorizing tasks, cleaning up stale tasks, fleshing out projects, identifying priorities, reviewing areas, or any mention of Things 3, todos, task management, inbox processing, or task cleanup. Trigger phrases include 'triage', 'process inbox', 'sort my tasks', 'clean up', 'what should I focus on', 'prioritize', 'review my tasks', 'flesh out', or any reference to Things 3."
---
 
# Things 3 Task Management
 
Rules and context for managing Aaron's Things 3 system. All operations use the **Things MCP** tools.
 
## MCP Tools
 
### Key operations
 
- **Read inbox**: `Things MCP:get_inbox` — returns UUID, title, notes, creation date per task.
- **Read tasks in a project**: `Things MCP:get_todos` with project UUID — includes headings.
- **Move/update a task**: `Things MCP:update_todo` with `id`, `list_id` (UUID of destination), optionally `title`, `notes`, `heading`.
- **Create a task**: `Things MCP:add_todo` with `list_id` or `list_title`.
- **Discover structure**: `Things MCP:get_areas`, `Things MCP:get_projects`, `Things MCP:get_tags`.
- **Search**: `Things MCP:search_todos` for finding tasks by keyword.
- **Today / Upcoming**: `Things MCP:get_today`, `Things MCP:get_upcoming`.
- **Completed work**: `Things MCP:get_logbook`.
 
Always prefer `list_id` (UUID) over `list` (title) when moving tasks — avoids emoji encoding issues.
 
## Areas
 
Aaron organizes by **domain × urgency**, plus three special-purpose areas.
 
### Domain areas (Hot = active now, Slow burn = matters but not urgent)
 
| Area                         | UUID                           | Purpose                              |
| ---------------------------- | ------------------------------ | ------------------------------------ |
| 💼 Firsthand - Hot           | `TX6grmZShXTfpSHGfV4mKU`      | Active work tasks                    |
| 💼 Firsthand - Slow burn     | `CuwsGyepYR2mK19zwLvGbA`      | Work tasks that aren't urgent        |
| 💼 Galileo - Hot             | `UcLT9XbdVgrGu4LCyRpMrd`      | Former employer — legacy/transition  |
| 🏠️ Personal - Hot            | `EVXsJuhXYxDp97HVK8DsA4`      | Active personal tasks                |
| 🏘️ Personal - Slow burn      | `9vESbkT2d8ugFTHBg2Sm8e`      | Personal tasks, not urgent           |
 
### Special-purpose areas
 
| Area                         | UUID                           | Purpose                              |
| ---------------------------- | ------------------------------ | ------------------------------------ |
| 🤖 Things AI can do          | `HSy98Ub9B8EVXZ7AAF5mVT`      | Tasks Claude can research/execute autonomously. NOT for bookmarks or simple purchases. For real work Claude does. |
| 👁️ Things to go look at      | `XWpp87pVmhfjhmvnf5q7nd`      | Bookmark/read-later items. Has sub-projects by category. |
| 🕰️ Waiting on                | `TqrFex3LR9b7poFgPPziz4`      | Blocked tasks — ball is in someone else's court. Tasks move in and out. |
 
## Projects
 
### Discovering projects
 
Projects change over time. At the start of each session, call `Things MCP:get_projects` to discover current projects and their UUIDs. Match tasks to existing projects by name/purpose. If no project fits, route to the appropriate icebox.
 
### Bookmark sub-projects (under 👁️ Things to go look at)
 
These are permanent categorical buckets for read-later/bookmark items.
 
| Project                        | UUID                           | What goes here                            |
| ------------------------------ | ------------------------------ | ----------------------------------------- |
| 🔗 Misc links                  | `Ry7wGGF9bADYBnz6oWVEx1`      | General links, articles, misc bookmarks   |
| 📕 Books                       | `qoenfsfDPDfEG5hVW78zV`       | Books to read or look into                |
| 👾 Video games                  | `FzBHbgY3dqW8ow3MU17jdr`      | Games to check out                        |
| 🧑‍💻 Github things               | `2EUnWnWeDFC6uU1uRGsg7r`      | GitHub repos to explore                   |
| 🧑‍⚕️ Healthcare & healthtech     | `BdbqCNJFavAqBHoK6Tm6A8`      | Healthtech links, podcasts, newsletters   |
| 🤖 AI Tools                    | `XkBZDaGJY426Gw34ScSCTb`      | AI tools, MCP servers, dev tools to try   |
 
### Icebox Projects
 
The two iceboxes are loosely-organized backlogs — one for work, one for personal. They avoid the overhead of creating projects or defining scope too early. Tasks are grouped under **headings** by domain.
 
| Icebox                         | UUID                           | Use for                                   |
| ------------------------------ | ------------------------------ | ----------------------------------------- |
| 🧊 Firsthand - Icebox          | `Vmtvg42tRE3daidSZggvvS`      | Work tasks not ready for a project yet    |
| 🧊 Personal - Icebox           | `D1a8FYiowDHv6DvHCN1aVH`      | Personal tasks not ready for a project yet|
 
When routing to an icebox, always specify a `heading` to keep things organized. Pick the best-fit heading from the task's domain. If no existing heading fits, propose a new one to the user. Discover live headings via `Things MCP:get_todos` with the icebox project UUID.
 
## Universal Rules
 
- **NEVER take action without explicit confirmation.** Always present recommendations first. Wait for Aaron to approve, adjust, or reject before executing any `update_todo`, `add_todo`, or other write operations. This applies to all workflows — triage, cleanup, planning, everything.
- **NEVER trash or delete anything.** NEVER set `canceled: true`. Everything was written for a reason.
- Always rewrite voice-dictated tasks into clean, actionable language.
- A task can only belong to ONE project. Pick the best fit.
- When moving to Things AI can do, add thorough notes describing the full scope of work.
- When moving to an icebox, always specify a `heading` to categorize by domain.
- Use `list_id` (UUID) over `list` (title) when moving tasks — avoids emoji encoding issues.
- n8n automation tasks: file with the actual subject/area/project, not a separate n8n category.
- Healthtech links go to "🧑‍⚕️ Healthcare & healthtech". Ambiguous healthtech text — clarify with user.
- Forward emails from newsletters usually mean "save the useful link and unsubscribe."
 
## Voice Dictation Errors
 
Many tasks are voice-dictated. Watch for these common misrecognitions:
 
| Dictated as                    | Actual meaning                            |
| ------------------------------ | ----------------------------------------- |
| "NHN", "NHI", "and ate in"    | **n8n** (automation platform)             |
| "Rick ass"                     | **Raycast** (Mac launcher)                |
| "fire", "FIRE"                 | **FHIR** (healthcare interop standard) — **only** in medical/healthtech context. If the task is about actual fire (home, safety, outdoors), leave it as-is. |
| "first hand"                   | likely **Firsthand** (employer)            |
 
## Patterns
 
These recurring patterns help with routing and context:
 
- Many tasks are voice-dictated thoughts (rambling, no punctuation).
- "Look up X" tasks are research Claude can do → Things AI can do.
- Work tasks typically route to Firsthand area/projects.
- Health-related personal tasks recur frequently.
- Collectible/hobby tasks (board games, puzzles, LJN, Four Souls) appear regularly.
- Home automation / Home Assistant tasks are common.
- Tools and dev environment tasks cluster in 🔨️ Tools or AI Tools.
 
---
 
## Inbox Triage
 
Use this section when processing inbox tasks into projects and areas.
 
### Setup
 
1. Call `Things MCP:get_inbox` to fetch all inbox tasks.
2. Call `Things MCP:get_projects` to discover current projects and UUIDs.
3. Optionally call `Things MCP:get_todos` on the iceboxes to see existing headings.
 
### Triage Questions
 
Ask these in order for each task:
 
1. **Actionable?** — Is there a clear next step, or is this just a thought/note?
2. **Detailed enough?** — Would someone know what to do with this?
3. **Claude-doable?** — Can Claude execute this (research, scripts, file ops)?
4. **Project?** — Does it fit an existing project? (check discovered projects)
5. **Area?** — Which area does it belong to?
6. **Icebox?** — If no specific project fits and it's not urgent, route to the appropriate icebox with a heading.
 
### Routing Rules
 
**AI-doable tasks**
- **Research task ("look up X")**: Rewrite clearly, move to **🤖 Things AI can do** (`HSy98Ub9B8EVXZ7AAF5mVT`). Add detailed notes describing what to research.
- **Task Claude can do (scripts, automation, comparison)**: Move to **🤖 Things AI can do** with comprehensive body notes.
 
**Human-action tasks**
- **Requires human action (conversations, logins, purchases)**: Rewrite clearly, file to correct project/area.
- **Simple purchase ("order X")**: File to appropriate area (usually Personal - Hot). Do NOT put in Things AI can do unless substantial product research is genuinely needed.
 
**Icebox routing**
- **Work task, no specific project, not urgent**: → **🧊 Firsthand - Icebox** (`Vmtvg42tRE3daidSZggvvS`) with an appropriate `heading`.
- **Personal task, no specific project, not urgent**: → **🧊 Personal - Icebox** (`D1a8FYiowDHv6DvHCN1aVH`) with an appropriate `heading`.
 
**Bookmark routing**
- **Just a link (healthtech, podcasts, newsletters)**: → 🧑‍⚕️ Healthcare & healthtech (`BdbqCNJFavAqBHoK6Tm6A8`).
- **Just a link (AI tool, MCP server, dev tool)**: → 🤖 AI Tools (`XkBZDaGJY426Gw34ScSCTb`).
- **Just a link (GitHub repo)**: → 🧑‍💻 Github things (`2EUnWnWeDFC6uU1uRGsg7r`).
- **Just a link (book)**: → 📕 Books (`qoenfsfDPDfEG5hVW78zV`).
- **Just a link (video game)**: → 👾 Video games (`FzBHbgY3dqW8ow3MU17jdr`).
- **Just a link (other)**: → 🔗 Misc links (`Ry7wGGF9bADYBnz6oWVEx1`).
 
**Special routing**
- **Four Souls / Binding of Isaac content**: → Find the BOI/Four Souls project via `get_projects`.
- **Leadership principle or career philosophy note**: → Find the leadership style project via `get_projects`.
- **Blocked / waiting on someone else**: → 🕰️ Waiting on (`TqrFex3LR9b7poFgPPziz4`).
 
**Fallback**
- **Not a task / just a note**: NEVER trash. Rewrite as an action or file it somewhere appropriate. Aaron wrote it for a reason.
- **Vague voice-dictated thought**: Rewrite clearly before filing. Ask user for clarification if goal is unclear.
 
### Triage Presentation Format
 
Present all recommendations as a batch:
 
```
**Task**: [original title]
**Rewrite**: [cleaned up title, if needed]
**Destination**: [Project name] (in [Area])
**Heading**: [heading name, if routing to an icebox]
**Notes to add**: [any context/scope to include in notes field]
**Rationale**: [one line on why this routing]
```
 
Then ask: **"Want me to execute all of these, or adjust any?"**
 
After confirmation, execute all moves using `Things MCP:update_todo` calls.
