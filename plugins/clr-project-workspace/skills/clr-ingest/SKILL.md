---
name: clr-ingest
description: >-
  Process and route incoming content to the right places in your workspace.
  Use when the user pastes, shares, or references any content with actionable
  information — emails, meeting notes, contracts, invoices, travel confirmations,
  HOA notices, vendor quotes, receipts, agendas, minutes, forwarded messages,
  policy documents, letters, or any text containing names, dates, decisions,
  deadlines, action items, or reference details. Also triggers when the user says
  "ingest this", "process this", "file this", "add this to the project",
  "here's an update", "I got this email", or "log this".
  Companion to clr-setup-project-workspace — acts as the workspace inbox.
---

## What This Does

You are the workspace inbox. When content arrives — pasted text, forwarded emails, meeting notes, scanned documents, invoices, contracts, travel confirmations — your job is to extract what matters and route it to the right places in the workspace.

Every piece of incoming content can produce up to five outputs:

1. **CLAUDE.md updates** — new people, terms, preferences, or context that should persist across sessions
2. **Reference documents in notes/** — meeting notes, decision logs, research, correspondence worth keeping
3. **TASKS.md updates** — new tasks, deadline changes, status updates to existing tasks
4. **Saved originals** — the raw content preserved for future reference when it has lasting value
5. **Summary for the user** — what you did, what changed, what needs their attention

Not every piece of content produces all five. An invoice might create a task and a reference doc. A casual status update might just update an existing task. Use judgment.

## Step 1: Understand the Content

Before doing anything, read and classify the content:

- **Type**: Email, meeting notes, invoice, contract, travel confirmation, HOA notice, letter, receipt, policy document, agenda, minutes, forwarded message, or other
- **Source**: Who sent it, what organization, what context
- **Key information**: Names, dates, amounts, deadlines, decisions, action items, reference numbers
- **Urgency**: Is there a deadline? Does something need a response? Is this time-sensitive?
- **Relevance**: What parts matter for this project? What's filler?

If the content is a PDF, see `references/pdf-handling.md` for extraction guidance.

## Step 2: Ask Clarifying Questions

Only ask questions when you genuinely cannot proceed without an answer. Most content is self-explanatory.

Valid reasons to ask:
- The content references a person or project you have no context for and it matters for routing
- There are multiple plausible interpretations that lead to different actions
- The content seems to belong to a different project entirely

Do NOT ask:
- "Should I add this to TASKS.md?" — just do it if there are actionable items
- "Where should I save this?" — follow the routing rules below
- "Is this important?" — if they sent it to you, treat it as important

Limit yourself to one focused question when you must ask. Batch sub-questions into a single message.

## Step 3: Explore the Workspace

Before making changes, understand the current state. Read these files:

1. **CLAUDE.md** — understand the project context, known people, terms, and preferences
2. **TASKS.md** — see existing tasks to avoid duplicates and understand current priorities
3. **Glob for notes/** — scan existing reference documents to avoid duplicating content and to find related files

This step is critical. Without it, you'll create duplicate tasks, miss context, and put things in the wrong place.

## Step 4: Make Updates

Work through each destination in order. Only touch what needs updating.

### CLAUDE.md Updates

Add new entries when the content introduces:
- **People**: Names, roles, contact info, relationships (e.g., "contractor", "HOA board member")
- **Terms/acronyms**: Project-specific language the user uses casually
- **Preferences**: Stated preferences about how things should be done
- **Context**: Background information that will matter in future sessions

Use the existing format and sections in CLAUDE.md. Don't restructure it. Append to the right section. If no section fits, add to the most logical place.

### Reference Documents in notes/

Create a new file in notes/ when the content is:
- Meeting notes or minutes worth keeping
- A decision log or policy document
- Correspondence that may be referenced later
- Research, quotes, or proposals

Naming convention: `notes/YYYY-MM-DD-descriptive-name.md` (e.g., `notes/2026-03-15-hoa-board-meeting.md`)

Format reference docs as clean markdown:
- Clear title and date at the top
- Source attribution (who sent it, when, what context)
- Key points extracted and organized
- Action items called out explicitly if present

### TASKS.md Updates

For new tasks discovered in the content:
- Add them to the appropriate section in TASKS.md
- Include deadlines if mentioned
- Include context about who assigned them or where they came from
- Check for duplicates first — update existing tasks rather than creating new ones

For updates to existing tasks:
- Update status, deadlines, or details in place
- Add notes about what changed and why

Use the productivity:task-management skill conventions for formatting.

### Save Originals

Save the raw content when it has lasting reference value — contracts, formal correspondence, official notices, detailed quotes. Save to `notes/` with a clear filename indicating it's the original:
- `notes/2026-03-15-original-contractor-bid.md`
- `notes/2026-03-15-original-hoa-notice.md`

Don't save originals of casual messages, brief status updates, or content where the extracted information captures everything useful.

### Create New Files

Sometimes content warrants a new standalone file beyond notes/ — a budget spreadsheet structure, a timeline, a contact list. Create these at the workspace root or in a logical subdirectory. Use judgment about when this is warranted versus when notes/ is sufficient.

## Step 5: Report Back

After processing, give the user a concise summary:

1. **What you processed** — one line describing the content type and source
2. **What changed** — bullet list of files created or modified
3. **Task updates** — any new tasks added or existing tasks updated
4. **Flagged items** — anything that needs the user's attention or decision
5. **Suggested next steps** — optional, only if there are obvious follow-ups

Keep it tight. The user can see the diffs and read the files. Don't regurgitate the content back at them.

## Workspace Bootstrap

If the workspace doesn't have CLAUDE.md and TASKS.md, the productivity system isn't set up yet. Tell the user:

> This workspace hasn't been initialized yet. Run `clr-setup-project-workspace` first to set up the project structure, then come back with your content.

Don't attempt to create workspace files manually — let the proper initialization skill handle it.

## Content Examples

These illustrate routing decisions, not exact templates:

**Contractor email about kitchen renovation**
- CLAUDE.md: Add contractor name, company, contact info to people section
- notes/: Save as reference doc with key details (scope, timeline, price)
- TASKS.md: "Review contractor bid" with deadline if one was mentioned
- Original: Save if it's a formal bid/proposal

**HOA board meeting minutes**
- CLAUDE.md: Add any new board members or terms mentioned
- notes/: Save full minutes as reference doc
- TASKS.md: Extract action items assigned to the user
- Original: Save — official record

**Travel confirmation email**
- CLAUDE.md: Add trip details to context if it's a project-related trip
- notes/: Save itinerary as reference doc with dates, confirmation numbers, addresses
- TASKS.md: Add pre-trip tasks if relevant (pack, arrange pet care, etc.)

**Vendor invoice**
- notes/: Save with amount, due date, vendor details
- TASKS.md: "Pay [vendor] invoice — $X due by [date]"
- Original: Save — financial record

## References

For edge cases (mixed-relevance content, conflicting information, sensitive data, duplicates), see `references/edge-cases.md`.

For PDF-specific handling (text extraction, OCR fallback, compression), see `references/pdf-handling.md`.
