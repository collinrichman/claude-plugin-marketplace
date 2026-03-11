# GitHub Issue Template for Remote Plugin Improvements

Use this template when the target skill belongs to a plugin from the `collinrichman/claude-plugin-marketplace` repository that is not the current working directory.

## Issue Creation Command

Create the issue using `gh issue create`:

```bash
gh issue create \
  --repo collinrichman/claude-plugin-marketplace \
  --title "[clr-skill-improver] Improve <skill-name> in <plugin-name>" \
  --label "skill-improvement" \
  --body "$(cat <<'ISSUE_EOF'
<issue body here>
ISSUE_EOF
)"
```

## Issue Body Structure

The issue body must be structured so another Claude Code session can parse and implement the changes directly. Use the following format:

```markdown
## Summary

Brief description of the improvement needed and what prompted it.

- **Skill:** `<skill-name>` in plugin `<plugin-name>`
- **Source:** Session analysis via `clr-skill-improver`
- **User feedback:** <user feedback if provided, or "None — improvements identified from session analysis">

## Session Analysis Findings

### Trigger Accuracy
<findings or "No issues identified">

### Instruction Quality
<findings or "No issues identified">

### Efficiency & Permissions
<findings or "No issues identified">

### Output Quality
<findings or "No issues identified">

### Supporting Files
<findings or "No issues identified">

## Changes Required

### File 1: `plugins/<plugin-name>/skills/<skill-name>/SKILL.md`

**Change:** <brief description of what is changing>
**Reason:** <why this change improves the skill>

Replace this content:
```
<exact old content to find>
```

With this content:
```
<exact new content to replace with>
```

### File 2: `plugins/<plugin-name>/skills/<skill-name>/references/<file>.md`
(repeat the same format for each additional file)

**Change:** <brief description>
**Reason:** <why>

Replace this content:
```
<old content>
```

With this content:
```
<new content>
```

## Version Bump

Update `.claude-plugin/marketplace.json`:

Find the entry for `<plugin-name>` in the `plugins` array and change:
```json
"version": "<current-version>"
```
to:
```json
"version": "<new-version>"
```

**Versioning rationale:** <patch for minor fixes, minor for new functionality or restructuring>

## Implementation Notes

- All edits use exact string matching — the "old content" blocks must match the current file content exactly
- Read each file before editing to verify the old content still matches
- After applying all changes, run `claude plugin validate ./plugins/<plugin-name>` to verify structure
- Test the improved skill locally with `claude --plugin-dir ./plugins/<plugin-name>`
```

## Formatting Rules

- Use fenced code blocks with language hints for all content snippets
- Keep the old/new content blocks as small as possible — show only the changed section with enough surrounding context for unique matching
- If a change adds a new file (e.g., a new reference), use "Create new file" instead of the replace format
- If a change deletes content, use "Delete this content" with the exact content to remove
- Include line context (a few surrounding lines) in old content blocks to ensure unique matching

## After Creating the Issue

1. Capture the issue URL from the `gh issue create` output
2. Share the URL with the user
3. Explain the next step: navigate to the `claude-plugin-marketplace` repo and assign the issue to Claude Code for implementation
4. The assignee can run `/fix-issue <number>` or similar to implement the changes described in the issue

## Label Convention

Always apply the `skill-improvement` label. If the label does not exist yet, create it:

```bash
gh label create "skill-improvement" \
  --repo collinrichman/claude-plugin-marketplace \
  --description "Automated skill improvement from clr-skill-improver" \
  --color "0E8A16"
```

Attempt to create the label before creating the issue. If it already exists, the command will fail harmlessly — proceed with issue creation regardless.
