---
name: clr-unsubscribe-triage
description: >-
  Use when the user asks to "run the unsubscribe triage", "clean up my inbox",
  "unsubscribe from marketing emails", "scan my Gmail for promos to unsubscribe",
  "next batch of unsubscribes", or any variation of bulk-unsubscribing from
  promotional senders in Gmail. Drives Gmail in Chrome to surface 10 candidate
  senders, renders a live review widget, and on approval executes Gmail's native
  one-click unsubscribe plus a bulk archive of every past email from each
  approved sender. Maintains a persistent ledger so processed senders never
  resurface as candidates.
---

## What this does

Bulk-unsubscribes the user from marketing email by driving Gmail's web UI in Chrome — never the Gmail MCP connector. Each run surfaces 10 candidate senders, lets the user approve/skip each in a live widget, and on confirmation runs Gmail's native one-click unsubscribe plus archives every past email from that sender.

Maintains a persistent ledger at `<outputs>/unsubscribe-triage/processed_senders.json` so already-handled senders never reappear as candidates.

## Prerequisites

- Claude in Chrome must be active.
- The user's Gmail must be open and authenticated in Chrome — confirm before starting. If `mail.google.com` redirects to a login page, stop and tell the user.
- DO NOT use the Gmail MCP connector for any step. Everything goes through Chrome navigation, finds, and clicks against `mail.google.com`.

## Workflow

### Phase 1 — Scan for candidates

1. Read the ledger at `<outputs>/unsubscribe-triage/processed_senders.json`. If missing, create it with `{"processed": [], "lastUpdated": "<today>"}`. The "processed" array lists sender addresses to skip.

2. Navigate Chrome to Gmail's promotions inbox view:
   ```
   https://mail.google.com/mail/u/0/#search/category%3Apromotions+in%3Ainbox
   ```
   Take a screenshot to identify recurring high-volume senders. Scroll once or twice with the `scroll` action to surface more.

3. Identify ~10 candidate senders by display name, prioritizing high-volume marketing/political/notification mail. Skip transactional senders: receipts, order confirmations, shipping updates, security alerts, bills, vet/medical reminders, account statements (especially employer benefits portals like Alight, Fidelity, Schwab — these are NOT marketing).

4. For each candidate, navigate to `#search/from%3A%22<Display+Name>%22+in%3Ainbox` (URL-encoded) and open the most recent thread with a click at approximately `(700, 197)` or `(700, 289)`. Capture the **real email address** from the message header — the display name is not a reliable address. Note the total count from the "X of Y" header.

5. Write `<outputs>/unsubscribe-triage/candidates.json` with the 10 verified candidates, each with `sender`, `displayName`, `count30d`, `sampleSubject`, and `verified: true`.

### Phase 2 — Render the review widget

Render the inline triage widget using `mcp__visualize__show_widget`. The widget template lives at `${CLAUDE_PLUGIN_ROOT}/assets/triage-widget.html` (this skill's own assets directory) — load it, substitute `__CANDIDATES_JSON__` with the candidate array, and pass the result as `widget_code`.

The widget renders cards for each sender with approve/skip toggles. The Confirm button calls `sendPrompt()` with a structured execution prompt that triggers Phase 3 in the next turn.

### Phase 3 — Execute on confirmation

When the user confirms in the widget, the `sendPrompt()` callback delivers a list of approved senders. For each one, run this loop in Chrome:

1. Navigate to `#search/from%3A<sender>` (URL-encoded). If the search returns "No messages matched your search", fall back to `#search/from%3A%22<displayName>%22+in%3Ainbox` and grab the real address from the first opened email.

2. Open the most recent thread (click coordinate near the top of the list, e.g. `(700, 197)` or `(700, 289)` depending on banner state).

3. Find Gmail's native Unsubscribe link in the message header (`find` query: "Unsubscribe link next to sender address in header"). Click it.

4. Handle the unsubscribe dialog with judgment — the flow varies by sender:
   - **One-click**: Dialog has Cancel / Unsubscribe → click Unsubscribe. Done.
   - **Go to website**: Dialog has Cancel / Go to website → click through and navigate the third-party preference page on the fly (usually an obvious Unsubscribe button or a form).
   - **No native chip**: Search for an in-body Unsubscribe link with `find` and follow that flow.
   - **Site-login required** (e.g., Nextdoor, Pinterest beyond per-campaign): Skip the unsubscribe step. Tell the user the sender requires manual action on their site.

5. Return to Gmail search for the sender. Click the select-all checkbox at coordinate `(307, 156)`.

6. If a "Select all conversations that match this search" banner appears (only when results exceed 100), click that link. This bumps the selection from the visible page to all matching threads.

7. Click the Archive icon at coordinate `(373, 156)`.

8. If a "Confirm bulk action" dialog appears (only on >100 matches), find and click its OK button.

9. **Verify the archive worked.** Take a screenshot or read the toast. If the toast says "X conversations archived", record X. If the search still shows results in the inbox, archive failed — diagnose and retry. Never assume a count.

10. Append the verified sender address to `processed_senders.json`.

### Phase 4 — Report

After all senders are processed, write `<outputs>/unsubscribe-triage/run_log_<date>_run<N>.json` with per-sender results. Reply with a markdown table summarizing:
- ✅ unsubscribed + archived (count)
- ⚠️ archive only (unsubscribe needed website login or page expired)
- 🚫 skipped (no match found, or transactional)

## Critical pitfalls

These come from real failures — do not repeat them:

**Never assume archive counts.** The exact pattern that broke trust: "ChiTown Baggers" got searched as `from:info@chitownbaggers.com`, returned zero results, and a select-all + archive on an empty page silently did nothing — but the sender was actually `leagues@chitownbaggers.com`. Always read the toast or screenshot after archiving to confirm the count. If the search returns 0 results, that's a signal to fall back to a display-name search, not to move on.

**Display-name searches are essential.** Many senders use non-obvious addresses (e.g., Target surveys come from `Target@express.medallia.com`, Toast-hosted restaurants from `no-reply+<hash>@toast-restaurants.com`). Never trust an address you guessed without opening an email to verify.

**Some transactional addresses look promotional.** Nordstrom Benefits Portal (`NordstromBenefitsPortal@alight.com`) sends recurring messages but they are 401k retirement statements — not marketing. Always read at least one email before adding a high-volume "survey" or "benefits" sender to the candidate list. When in doubt, skip.

**One sender, multiple addresses.** A brand often sends from several addresses (Goodreads uses both `no-reply@mail.goodreads.com` and `noreply@goodreads.com`; Paperless Post uses `help@...` for T&Cs and `paperless@...` for marketing). Each needs its own unsubscribe + archive pass. Adding the first to the ledger does not catch the second.

**Political campaigns use many display names from one address.** A single campaign address often signs emails as surrogates ("Sherrod Brown", "Ken Burns", etc.) to get opens. One unsubscribe handles them all.

**Some addresses bundle marketing AND receipts.** Unsubscribing from a sender like `no-reply@doordash.com` and archiving everything from that address sweeps up historical order confirmations along with marketing. Not destructive (still searchable in All Mail), but worth flagging in the report so the user isn't surprised.

**Chrome occasionally hangs.** If `screenshot` or `find` time out 45 seconds in a row, navigate to `https://www.google.com` to reset, wait, then resume. Don't keep retrying the hung action.

## Resources

- `references/ledger-format.md` — schema for `processed_senders.json`, `candidates.json`, and the run log
- `assets/triage-widget.html` — the live review widget template; substitute `__CANDIDATES_JSON__` with the candidate array

## Output paths

All persistent state lives in the user's outputs directory (Cowork: `/sessions/<id>/mnt/outputs`; Claude Code: project root or wherever the user designates):

- `unsubscribe-triage/processed_senders.json` — ledger
- `unsubscribe-triage/candidates.json` — current run's candidates
- `unsubscribe-triage/run_log_<date>_run<N>.json` — per-run results

Re-read the ledger every time the skill triggers. Treat it as the source of truth.
