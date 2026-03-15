---
name: clr-setup-project-workspace
description: >-
  Initialize a structured workspace for non-code administrative projects.
  Use when the user says "set up a new project", "initialize a workspace",
  "start a new project folder", "set up tracking for X", "organize my project",
  "start fresh", "get this folder ready", or wants to create a workspace for
  trip planning, HOA management, event coordination, home renovation tracking,
  work process tracking, or similar organizational tasks.
  NOT for coding projects, software applications, repositories, or cloud-based Claude Projects.
  NOT for workspaces that already have CLAUDE.md and TASKS.md (offer to update preferences instead).
---

## What This Does

This skill sets up a complete workspace for administrative and organizational projects by:

1. Running `productivity:start` to create the core productivity system (TASKS.md, CLAUDE.md, memory/, dashboard.html) and conducting the memory bootstrap interview.
2. Creating a `notes/` directory for project documents and reference materials.
3. Appending a `## Workspace Preferences` section to CLAUDE.md with standard workspace preferences tailored to admin/documentation projects.
4. Giving the user a brief orientation on what was created.

## Scope Boundary

This skill is for administrative, planning, and organizational projects only — not for code projects, software applications, or repositories. If the user appears to want a coding project, decline and suggest a standard project scaffold instead.

## Dependencies

This skill orchestrates other skills. It requires:
- `productivity:start` — creates core workspace files and runs the bootstrap interview
- `productivity:memory-management` — referenced in preferences for ongoing use
- `productivity:task-management` — referenced in preferences for ongoing use
- `productivity:update` — referenced in preferences for syncing tasks and refreshing memory when returning to the project
- `clr-ingest` — referenced in preferences for routing incoming content to the right place

If `productivity:start` is not available, inform the user they need to install the productivity plugin and stop.

## Step 1: Check If Already Initialized

Check if CLAUDE.md and TASKS.md both exist in the current working directory.

**If both exist:** Tell the user the workspace is already set up. Offer to:
- Update workspace preferences (edit the `## Workspace Preferences` section in CLAUDE.md)
- Re-run the bootstrap interview

Do not overwrite or blow away existing work.

**If neither or only one exists:** Proceed to Step 2.

## Step 2: Run productivity:start

Invoke `productivity:start` using the Skill tool and follow its complete flow:
- Creates TASKS.md, CLAUDE.md, memory/, dashboard.html
- Runs the memory bootstrap interview (asks about the project, people, terms, shorthand)

Do NOT skip or abbreviate the interview. Let `productivity:start` do its full thing.

## Step 3: Create Project Structure & Append Preferences

After `productivity:start` finishes and CLAUDE.md exists:

1. Create a `notes/` directory with a README.md explaining it holds project documents, reference materials, research, drafts, and working notes. Include example filenames like `notes/2026-03-15-vendor-call.md`.

2. **Append** the following exact markdown to the end of CLAUDE.md. Do not overwrite anything `productivity:start` already wrote:

```markdown
## Workspace Preferences

### Skills Usage
Always use these skills proactively whenever they're relevant. Don't wait for explicit requests.
- **productivity:start**, **productivity:memory-management**, **productivity:task-management**: Use for all task tracking, memory updates, and workspace maintenance.
- **productivity:update**: Use when returning to the project after time away, when tasks may have gone stale, or when pulling in tasks from external sources. Suggest running this at the start of a session if the last update was more than a few days ago.
- **clr-ingest**: Use whenever new content arrives (emails, notes, updates, pasted text) that contains actionable information like names, dates, decisions, or tasks. Route it to the right place automatically.

### Task Completion
- When completing a task in TASKS.md, move it to the ## Done section (don't leave checked-off items in Active/Waiting On). Keep the task's completion date and a brief summary of the outcome.

### Output Preferences
- Prefer Markdown files over Word docs (no Word on Mac).
- Keep things practical, not formal. Skip unnecessary ceremony.
- Avoid em dashes in drafted communications. Use periods, commas, or apostrophes instead.

### Notes Organization
- Store meeting notes, decision logs, research, and reference materials in notes/ with descriptive date-prefixed filenames.
- Use memory/ for cross-session context summaries per the productivity system.

### Project Scope
- This workspace is for administrative and organizational tasks. Do not treat files as source code or attempt to run them.
```

## Step 4: Brief Orientation

Give the user a short summary. Keep it tight — don't over-explain.

Cover:
- **What was created** — list the files and where they are (CLAUDE.md, TASKS.md, memory/, dashboard.html, notes/)
- **Preferences persist** — workspace preferences in CLAUDE.md carry forward to future sessions automatically
- **Updating preferences** — they can say "update my workspace preferences" anytime
- **Key skills** — quick reminder of task management and memory skills
- **Catching up** — mention that when they return after time away, they can say "catch me up" or "sync my tasks" to run `productivity:update`, which triages stale items and fills memory gaps

That's it. The user is ready to work.

## Updating Preferences Later

If the user asks to "add a preference", "update workspace settings", "change my output preferences", or similar, edit the `## Workspace Preferences` section in CLAUDE.md directly. This section is the canonical location for cross-session preferences.

## Preferences Reference

Default preferences for quick reference when maintaining this skill. Changes to the defaults should be edited in the Step 3 code block above; this copy is just for reference.

**Skills Usage:**
- productivity:start, productivity:memory-management, productivity:task-management — all task tracking, memory updates, workspace maintenance
- productivity:update — sync tasks and refresh memory when returning after time away
- clr-ingest — route incoming content (emails, notes, updates) to the right places

**Task Completion:**
- Move completed tasks to Done section. Keep completion date and brief outcome summary.

**Output Preferences:**
- Markdown over Word docs
- Practical, not formal
- No em dashes in drafted communications

**Notes Organization:**
- Date-prefixed filenames in notes/
- Cross-session context in memory/

**Project Scope:**
- Admin/organizational tasks only
