# Unsubscribe flow patterns

Catalog of third-party unsubscribe pages encountered in real runs and how to handle each.

## Gmail one-click (List-Unsubscribe-Post header)

The cleanest case. Click the Unsubscribe link in the message header → Gmail dialog appears with `Cancel` and `Unsubscribe` → click Unsubscribe → done. No new tab opens.

Handles: most modern marketing senders (Replit, Shop, MGM Rewards, Mary Peltola, Caamp, Angel's Envy, ParkMobile, Saint John's Resort, Article, Chipotle, Goodreads, Ticketmaster, StubHub, BISSELL, The Second City, Holbox, Intelligentsia, Fireplace & Chimney, Sportsmans Outdoor, Mr Duct dialog only, Target Medallia, Tanuki/Toast, DoorDash, Wispr Flow, Brewsology, Eugene Vindman, Luther Giving Day, MileagePlus, Paperless Post, Vindman, Chicago Beer Fest, ChiTown Baggers).

## Klaviyo email-form (manage.kmail-lists.com)

Pattern: Click in-body Unsubscribe → new tab opens to `manage.kmail-lists.com/subscriptions/unsubscribe?...` → page asks user to type their email and click Unsubscribe.

**Gotcha**: the email field is autofilled but the form ignores the autofill — typing into the field appends to the autofilled value, producing a duplicate. Triple-click the field, press Backspace, then type the email fresh.

Handles: Fireplace & Chimney Authority, Intelligentsia (when reached via in-body link).

## SevenRooms toggle UI

Pattern: Gmail dialog has `Cancel` / `Go to website` → clicking Go to website opens `sevenrooms.com/direct/email-campaign-subscriptions/...` → page shows Manage Subscriptions with toggles per location, plus an "Unsubscribe from All" button.

Just click "Unsubscribe from All" — confirmation page says "Email Preferences Updated".

Handles: Dēliz Italian Steakhouse, PLANTA, any restaurant on SevenRooms.

## Nutshell preferences (depotstreetmail.com)

Pattern: In-body Unsubscribe → new tab to `depotstreetmail.com/preferences/...` → page lists "Unsubscribe me from messages like this" (pre-selected) and "Unsubscribe me from all emails" with an Unsubscribe button.

Default selection unsubs from one campaign. To kill all mail from this sender, click "Unsubscribe me from all emails" first.

Handles: Chicago HVAC Repair Doctor (and other small businesses on Nutshell).

## Pinterest per-campaign

Pattern: In-body "Unsubscribe from this email" → new tab to `pinterest.com/email/subscription/...` → confirms unsub from one specific email type only (e.g., "Pins picked for you").

Pinterest has many email categories. Each has its own per-campaign unsubscribe link. Site-wide unsubscribe requires Pinterest login → settings → notifications. Skip for full unsubscribe; otherwise click Confirm to handle the one campaign.

## Nextdoor — site login required

Pattern: Gmail dialog says "go to their website to unsubscribe" with `Cancel` / `Go to website` → clicking Go to website lands on a Nextdoor page that requires login.

Cannot complete via Chrome automation. Skip the unsubscribe step, archive past mail, and tell the user they need to manage Nextdoor email preferences via their account settings (or deactivate the account, which we've seen them do).

## Apple — managed via Apple ID

Pattern: No Gmail Unsubscribe chip, no in-body unsubscribe link. Apple manages all marketing via Apple ID privacy settings.

Skip the unsubscribe step, archive only.

## Substack

Standard Gmail one-click works.

## Eventbrite (campaign emails)

Standard Gmail one-click works (e.g., Brewsology emails sent through `noreply@campaign.eventbrite.com`).

## Constant Contact (ccsend.com)

Standard Gmail one-click works (e.g., Chicago Beer Fest from `contact-drinkeatplay.com@shared1.ccsend.com`).

## Toast (restaurants)

Standard Gmail one-click works (e.g., Tanuki Sushi from `no-reply+f006105c@toast-restaurants.com`).

## Form-based confirmation (political campaigns on ActionKit)

Pattern: Gmail one-click opens new tab to a campaign's confirmation form with an "UNSUBSCRIBE" button, optional "why are you leaving" textarea. Click UNSUBSCRIBE — confirmation says "Thanks! We've removed you from our mailing list."

Handles: Chris Pappas, similar campaigns.

## Expired unsubscribe page

Some senders' unsubscribe URLs go stale. Page shows "This page no longer exists" or 404.

Action: Skip the unsubscribe step, archive past mail, add to ledger anyway (so it doesn't reappear). Note in run log.

Handles: Abt (`Abt@mail.abt.com` — the abt.com unsubscribe link returned page-no-longer-exists).
