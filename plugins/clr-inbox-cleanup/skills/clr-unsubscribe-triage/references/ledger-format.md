# Ledger and run log formats

## processed_senders.json

Lives at `<outputs>/unsubscribe-triage/processed_senders.json`. The source of truth for what's already been handled. Re-read on every skill invocation.

```json
{
  "processed": [
    "teampappas@e.chrispappas.org",
    "noreply@shop.app",
    "..."
  ],
  "lastUpdated": "2026-04-23",
  "method": "chrome+mail.google.com",
  "notes": "Optional free-text notes about edge cases or skips."
}
```

Rules:
- `processed` is a flat list of sender email addresses (lowercase or original-case, both OK — Gmail search is case-insensitive).
- Append-only. Never remove entries unless the user explicitly asks.
- Add the verified address (the one in `<sender@domain>` of the actual email), not the guessed one from the candidate widget.
- One brand may have multiple addresses — add each separately as it gets processed.

## candidates.json

Written by the scan phase, read by the widget render phase, then overwritten on the next run.

```json
{
  "generatedAt": "2026-04-23T18:00:00-05:00",
  "method": "chrome+mail.google.com",
  "candidates": [
    {
      "rank": 1,
      "sender": "info@e.vindmanforcongress.com",
      "displayName": "Eugene Vindman for Congress",
      "category": "political",
      "count30d": 18,
      "sampleSubject": "I need you with me to prove Trump wrong",
      "verified": true
    }
  ]
}
```

Field notes:
- `sender` should be the real address, captured by opening at least one email.
- `verified: true` means the address was confirmed by opening an email; `false` means it's a guess from display name.
- `category` is one of `political`, `social`, `marketing`. Used by the widget for the colored pill.
- `count30d` is approximate — pulled from the "X of Y" header in the search results.

## run_log_<date>_run<N>.json

One per execution batch. Captures what actually happened.

```json
{
  "runDate": "2026-04-23",
  "runNumber": 5,
  "method": "chrome+mail.google.com",
  "results": [
    {
      "sender": "info@e.vindmanforcongress.com",
      "displayName": "Eugene Vindman for Congress",
      "unsubscribe": "success",
      "archived": 28
    },
    {
      "sender": "Abt@mail.abt.com",
      "displayName": "Abt",
      "unsubscribe": "failed (unsubscribe page expired)",
      "archived": 69,
      "note": "Unsub URL returned 'page no longer exists'. Added to ledger so it won't resurface."
    }
  ],
  "summary": {
    "sendersProcessed": 9,
    "unsubscribedSuccessfully": 5,
    "archiveOnly": 2,
    "skipped": 1,
    "totalArchivedApprox": 218
  }
}
```

Always record the actual archive count from the Gmail toast — never assume "1" because a sender had one visible email. The bulk archive often catches dozens of historical messages from older threads.
