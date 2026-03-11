---
name: clr-skill-improver
description: Use when the user asks to "improve a skill", "fix a skill", "update a skill", "optimize a skill", "review skill performance", "skill didn't work well", "that skill could be better", or wants to refine a skill or command based on how it performed in the current session. Also use when the user describes problems with a skill they just used, says "the skill missed X", or wants to make a skill trigger more accurately.
argument-hint: "<skill-or-command-name> [feedback]"
allowed-tools: Read, Grep, Glob, Edit, Write, Bash, Agent
---

# Skill Improver

Analyze how a skill or command performed in the current session and apply targeted improvements. Handle skills and commands across all locations — project-level, user-level, local plugin, and remote plugin — with location-appropriate update strategies.

## Workflow Overview

1. Identify the target skill or command
2. Locate the target files and classify the location type
3. Analyze the current session for how the target was used
4. Synthesize improvements from session evidence and user feedback
5. Present proposed changes for confirmation
6. Apply changes (direct edit or GitHub issue, depending on location)

## Step 1: Identify the Target

Parse the invocation arguments:
- **`$0`** — Name of the skill or command to improve (required)
- **`$1`** — Optional user feedback on how it performed

If `$0` contains a colon (e.g., `plugin-name:skill-name`), split on `:` to extract the plugin name and skill name separately. This is the namespaced format for plugin skills.

If `$0` is not provided, ask the user which skill or command to improve. Check the session history for recently invoked skills as suggestions.

## Step 2: Locate and Classify

Find the target's files and determine the location type. Load **[references/location-detection.md](references/location-detection.md)** for the full detection algorithm, search order, and classification rules.

The location type determines what action to take after analysis:

| Location Type | Action |
|---|---|
| Project skill or command | Direct edit, no versioning |
| User skill or command | Direct edit, no versioning |
| Plugin skill (current repo) | Direct edit + bump version in marketplace.json |
| Plugin skill (remote, owned) | Create GitHub issue with Claude-executable instructions |
| Plugin skill (remote, unowned) | Inform user — cannot auto-improve; suggest manual changes |

After locating the target, read its full content:
- For skills: read `SKILL.md` and list any files in `references/`, `scripts/`, `assets/`
- For commands: read the command `.md` file

Read any referenced supporting files that are relevant to understanding the skill's behavior.

## Step 3: Analyze the Session

Review the current conversation to understand how the target skill or command was used. Load **[references/session-analysis.md](references/session-analysis.md)** for the detailed analysis checklist.

The analysis covers five dimensions:

1. **Trigger accuracy** — Did the skill activate when it should have? Were there false triggers or missed triggers? Is the description effective at matching user intent?

2. **Instruction quality** — Did Claude follow the skill's instructions faithfully? Were instructions ambiguous, incomplete, or leading to incorrect output? Were steps missing or in the wrong order?

3. **Efficiency and permissions** — Were there unnecessary tool calls, redundant file reads, or wasted context? Could `allowed-tools` be tightened or expanded to reduce permission friction? Were there excessive approval prompts?

4. **Output quality** — Did the skill produce the desired result? What was the gap between expected and actual output? What specific changes would close that gap?

5. **Supporting files** — Are `references/` files used effectively? Is content in the right place (SKILL.md vs references/)? Should any content be moved, added, or removed?

Combine session evidence with user feedback (`$1`) if provided. The user's feedback takes priority when it conflicts with automated analysis — the user knows what they wanted.

## Step 4: Synthesize Improvements

Produce a concrete list of changes. Each improvement must:
- Reference specific evidence from the session (what happened vs. what should have happened)
- Identify the exact file and content to change
- Explain why the change addresses the issue

Common improvement categories:
- **Description rewrites** — Add missing trigger phrases, improve intent matching, remove false-positive triggers
- **Instruction edits** — Clarify ambiguous steps, add missing steps, reorder for better flow, fix incorrect guidance
- **Frontmatter changes** — Adjust `allowed-tools`, add `disable-model-invocation`, change `context` settings
- **Reference file updates** — Move verbose content from SKILL.md to references/, add missing reference docs, update stale references
- **Command-to-skill migration** — If improving a command, suggest converting it to a skill for better features (supporting files, frontmatter options)

Keep improvements focused and minimal. Change only what session evidence supports — do not refactor beyond what is needed. Follow the same writing conventions as the original: imperative form, no second person, `clr-` prefix on names.

## Step 5: Present Proposed Changes

Before making any edits, present the proposed changes to the user:

1. Show a summary of findings from the session analysis
2. For each proposed change:
   - The file being modified
   - What is changing (old content → new content, shown as a diff-style preview)
   - Why this change improves the skill
3. For plugin skills in the current repo, note the version bump (e.g., `1.1.0 → 1.2.0`)
4. For remote plugin skills, preview the GitHub issue that will be created

Wait for user confirmation before proceeding. If the user wants to modify or skip certain changes, adjust accordingly.

## Step 6: Apply Changes

Execute the approved changes based on location type:

### Direct Edit (project, user, or local plugin skills/commands)

Use the Edit tool to apply changes to each file. For each edit:
- Read the current file content first
- Apply the specific change using Edit with exact `old_string` → `new_string`
- Verify the edit was applied correctly

For **local plugin skills**, also bump the version in `.claude-plugin/marketplace.json`:
- Find the plugin's entry in the `plugins` array
- Increment the patch version (e.g., `1.1.0` → `1.1.1`) for small improvements
- Increment the minor version (e.g., `1.1.0` → `1.2.0`) for significant changes (new functionality, restructuring)

### GitHub Issue (remote plugin from collinrichman/claude-plugin-marketplace)

Load **[references/github-issue-template.md](references/github-issue-template.md)** for the issue format.

Create a GitHub issue in `collinrichman/claude-plugin-marketplace` using `gh issue create`. The issue body must be structured so another Claude Code session can parse and implement the changes directly:
- Specific file paths relative to the repo root
- Exact content changes with old → new blocks
- marketplace.json version bump instruction
- Rationale for each change

After creating the issue, share the issue URL with the user and explain the next step: go to the marketplace repo and assign the issue to Claude Code for implementation.

### Blocked (remote plugin from another owner)

Inform the user that automatic improvement is not supported for plugins from other authors. Provide the analysis findings and suggest they:
- Fork the plugin repo and make changes manually
- Open an issue in the plugin's repo with the improvement suggestions
- Create a local override skill that supplements the plugin's behavior

## Handling Commands

Commands (`.claude/commands/*.md`) follow the same analysis and improvement workflow as skills, with these differences:

- Commands are single `.md` files (no directory structure, no references/)
- Commands support the same YAML frontmatter as skills
- If a command would benefit from supporting files (references/, scripts/), suggest migrating it to a skill as part of the improvement
- When improving a command, preserve its existing behavior — do not change it to a skill unless the user agrees

## Quality Guardrails

- Never edit a file without reading it first
- Present all changes before applying them — no silent modifications
- Preserve the original writing style and conventions of the target
- Follow the `clr-` prefix convention for any new names introduced
- For version bumps, use semantic versioning (patch for fixes, minor for features)
- If session evidence is insufficient to support a change, say so rather than guessing
- If the skill has no obvious issues, say so — do not manufacture improvements

## Additional Resources

- **[references/location-detection.md](references/location-detection.md)** — Full search algorithm, priority order, classification rules, and plugin origin detection
- **[references/session-analysis.md](references/session-analysis.md)** — Detailed analysis checklist for trigger accuracy, instruction quality, efficiency, output quality, and supporting files
- **[references/github-issue-template.md](references/github-issue-template.md)** — Claude-executable issue format for remote plugin improvements
