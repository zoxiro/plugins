# Connectors

Zoxiro is tool-agnostic. Each user connects their own accounts during onboarding, and
the modules work with whatever the user connects in each category. Zoxiro never stores
or hosts user data — it operates on the user's connected accounts.

## Tier 1 — Required for the morning automations (connect these for everyone)

| Connector | Used for |
| --- | --- |
| Gmail (or the user's email) | Daily inbox review, deal-flow scan, reply drafts, reply-gap scan |
| Google Drive | The `Zoxiro/` working folder — the book of record every scheduled job reads and writes |
| Google Calendar | Daily digest's calendar preview; scheduling |

**Why Drive is required:** scheduled jobs run in the cloud and can NEVER reach local
folders or the device bridge — the Drive-synced Zoxiro folder is how outputs persist
and reach the user's computer (Drive for Desktop mirrors it down automatically).

**Authorization matters (learned in live beta):** the Google Drive connector must be
granted FULL Drive access — every box checked on Google's consent screen. A
limited-access grant makes real folders return "not found" and synced files invisible.
And in the connector's tool-permission settings, read AND write/create tools must be
set to "Allowed" — "Needs approval" means every unattended write is silently declined
at 7:00 AM when nobody is there to approve. If a user's scheduled jobs can't see or
save files, disconnect → reconnect Drive with all permissions is the fix.

## Tier 2 — Connect if the user has them

| Connector | Used for |
| --- | --- |
| CRM (HubSpot; others as supported) | Pipeline module reads/writes deals there |
| Booking link (Calendly or similar — stored as a link, not a connector) | Scheduling-request reply drafts |


## Things that do NOT connect — and what to say

- **MLS feeds** — there is no MLS connector. Listings enter by pasted link, exported
  file, or by the user emailing/forwarding listings to their own inbox, where the
  daily inbox review picks them up and queues them for underwriting.
- **PropStream / BatchLeads / DealMachine** — enter by exported list (CSV dropped in
  chat or the folder). Skip-traced data comes along in the export.
- **Other AI tools** — nothing to connect and nothing needed; Zoxiro runs on the
  user's actual business accounts, not on other assistants.

## Notes

- The **Pipeline/CRM** module is built on top of the user's existing CRM rather than a
  Zoxiro-hosted database. If no CRM is connected, that module prompts the user to
  connect one.
- Connectors are account-level: connect once at onboarding and they survive every
  plugin update. Re-connection is only ever needed if the user disconnects one,
  switches Google accounts, or needs to upgrade a limited grant to full access.
- The **Admin** module's daily inbox review and 24-hour reply-gap scan are most
  useful scheduled (see the orchestrator's `references/scheduled-job-templates.md`
  for the platform rules and prompt templates).
