# Edge Cases

## Mixed-Relevance Content

Some content is partly useful and partly filler — a long email thread where only the latest reply matters, meeting minutes where three of twelve agenda items are relevant.

- Extract the signal, skip the filler
- When saving an original, save the whole thing but make the extracted reference doc focused on what matters
- If less than 20% of the content is relevant, don't save the original — just extract what you need

## Conflicting Information

The new content contradicts something already in CLAUDE.md or TASKS.md — a deadline changed, a person's role is different, a decision was reversed.

- Never silently overwrite existing information
- Flag the conflict to the user explicitly in your summary
- Present both versions: "CLAUDE.md says X, but this email says Y"
- Let the user decide which to keep, or update if the new information is clearly more recent and authoritative
- If updating, add a note about what changed and when (e.g., "Updated 2026-03-15 per contractor email — original deadline was April 1")

## Sensitive Content

Content may contain account numbers, passwords, SSNs, credit card numbers, medical information, or other sensitive data.

- Never put passwords, SSNs, or credit card numbers in markdown files
- Redact or omit sensitive fields: "SSN: on file", "CC: on file"
- Account numbers are fine to include — they're useful reference info for the project
- If the entire document is sensitive (medical records, legal filings), ask the user how they want it handled before processing
- When in doubt, err on the side of omitting sensitive details — the user can always add them manually

## Multiple Pieces of Content at Once

The user pastes several emails, documents, or updates in a single message.

- Process each piece sequentially in a logical order
- Deduplicate across pieces — if two emails mention the same task, create one task entry with both sources noted
- Give a combined summary at the end, organized by what changed rather than by source document
- If the batch is large (5+ items), consider grouping your summary by category (tasks, people, reference docs)

## Content in Images or Attachments

The user provides a screenshot, photo of a document, or image attachment.

- Use the Read tool to view images directly — it supports PNG, JPG, and other image formats
- Extract text content from the image and process it normally
- For photos of physical documents (mail, printed letters, handwritten notes), acknowledge that OCR-style extraction may miss details and flag anything unclear
- For PDFs, see references/pdf-handling.md

## Near-Empty Workspace

The workspace has CLAUDE.md and TASKS.md but they're mostly empty — the project was just initialized.

- This is fine. Process the content normally.
- The first few ingestions will do more CLAUDE.md writing than usual as you build up the project context
- Don't hold back on adding people, terms, and context just because the file is sparse — that's how it grows

## Duplicate Detection

Before creating any new file or task, check what already exists.

- Glob for files in notes/ with similar dates or topics
- Search TASKS.md for existing tasks that match the new content
- If a duplicate exists, update it rather than creating a new entry
- If similar but not identical content exists (e.g., an earlier version of the same document), note the relationship: "Updated version of notes/2026-03-01-contractor-bid.md"

## Ambiguous Source

The content doesn't clearly indicate who sent it, when, or in what context.

- Infer from formatting: email headers, letterheads, signature blocks, timestamps
- Infer from content: project-specific terms, people mentioned, subject matter
- If you can make a reasonable inference, state it: "This appears to be an email from [name] based on the signature"
- If you genuinely can't determine the source and it matters for routing, ask one question: "Who sent this / what's the context?"
- Don't ask if the source doesn't affect how you'd process it
