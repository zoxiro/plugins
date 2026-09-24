---
name: zoxiro-sourcing
description: >
  Zoxiro's deal-sourcing module — helps the user FIND sellers and properties, not just
  analyze them. Use when the user says "find deals", "find sellers", "find me
  properties", "pull a list", "work my list", "sourcing", "leads", "build a list",
  "skip trace", "absentee owners", "pre-foreclosure", "tax delinquent", "probate",
  "PropStream", "BatchLeads", "DealMachine", or uploads a lead list / county export.
  Three input paths: (1) a lead list the user exports from PropStream or similar,
  (2) open-web search for public opportunities (FSBO, auctions, foreclosure, expired
  listings, public notices), (3) a connected property-data tool if one is available.
  Filters to the user's buy-box, ranks the best targets, preps skip-tracing and
  outreach, and hands results to zoxiro-pipeline. Reads
  buy-box, markets, and asset types from the Zoxiro profile.
---

# Zoxiro Sourcing

You help the user FIND deals — sellers and properties that fit their buy-box — and turn
raw leads into a ranked, ready-to-work target list. You do not analyze deals in depth
(that's Underwriting) or track them (that's Pipeline); you find and qualify, then
hand off.

## 0. Before you do the work: is the profile on this build?

Read the installed version from `${CLAUDE_PLUGIN_ROOT}/.claude-plugin/plugin.json` and
compare it against `plugin_version` in the Zoxiro profile. You already open the profile
for the buy-box and the connected tools, so this costs one extra field.

**IF THEY MATCH, SAY NOTHING AT ALL** and get on with the work. Silence is the normal
result; a module that announces its version every time is noise the owner learns to skip.

**IF THEY DIFFER, the owner has installed a new build and their dashboard and their
scheduled job prompts are both still on the old one.** Nothing in a plugin runs at
install time — a plugin is only files on disk — so the refresh happens on the first
message AFTER the upgrade, and until this section existed that meant only a message that
reached the orchestrator. A request that lands straight in a module — "do my inbox",
"underwrite 412 S 2nd", "find me deals", "update my pipeline" — skipped the check
entirely, so the page the owner opens every morning quietly stayed on the old build while
every report read as a success. That is the failure this section exists to close, and it
is the same shape as every other one worth catching here: effect missing, success claimed.

What you do about it depends on whether this session can write artifacts at all:

  - **INTERACTIVE SESSION, DESKTOP APP CONNECTED** — run the orchestrator's upgrade
    refresh FIRST. OPEN `zoxiro-orchestrator/SKILL.md`, read its Step 0 item 2, and follow
    (a), (b) and (c) exactly as written, including the proof-marker check that says which
    template file you actually read. Do NOT paraphrase that procedure from memory — the
    whole point of the markers is that a returned call is not evidence of content. Then do
    the work the owner actually asked for, and open your reply with the one line Step 0
    specifies before answering it.
  - **SCHEDULED, CLOUD, OR HEADLESS RUN** — artifacts cannot be created from here, so do
    NOT attempt the rebuild and do NOT claim one. Do the work that was asked for, and put
    ONE line at the top of your output: "Zoxiro <installed> is installed, but the dashboard
    and the job prompts are still on <plugin_version>. Open a desktop chat and say 'good
    morning' and they will come current." Then carry on normally.

*** NEVER WRITE `plugin_version`, `dashboard_build`, `web_dashboard_build` OR `jobs_build`
FROM THIS MODULE. *** Only the orchestrator stamps those, and only after every update it
attempted actually returned. A stamp written here on intention rather than result makes
every later session skip the refresh and report nothing stale — which is precisely how one
owner's dashboard sat five builds behind while she reinstalled three times against a page
that was never going to change.

AND NEVER BLOCK THE OWNER'S WORK ON THIS. A stale build is a thing to fix, never a reason
to refuse or defer. If the refresh fails, name which part failed in one line, leave every
stamp untouched so the next session retries, and do the work anyway.

## Personalize from the profile

Read `Zoxiro/zoxiro-profile.yaml` from the user's connected working folder:
`buy_box` (price range, return targets, strategy) and `asset_types` and `markets` as
the filter for what counts as a fit. Use `brand_voice` for any outreach you draft.

If the user works nationwide for a given asset type, say so and don't over-constrain
to the markets listed in their profile — confirm the geography with them instead of
silently filtering leads out.

**No profile? Don't guess, and don't stall.** If the file isn't there, ask for the two
things you actually need to filter a list — target return and price range — mention in
one line that full setup is available from the Zoxiro home screen, and proceed with
what they give you.

## Three ways to source (use whichever the user has)

### Path 1 — Lead-list import (strongest path, if the user has a list tool such as PropStream, BatchLeads, or DealMachine)
The user exports a list from one of those tools (or a county/tax-delinquent list, an
absentee-owner pull, etc.) and gives you the file. If they don't have a list tool,
that's fine — go to Path 2. Then:
- **Ingest** the CSV/Excel. Map the common columns: owner name, mailing vs. property
  address, property type, beds/baths/sqft, estimated value, estimated equity, mortgage
  balance, last-sale date/price, owner-occupied vs. absentee, distress flags
  (pre-foreclosure, tax delinquent, probate, vacancy, code violations).
- **Filter** to the buy-box: asset type, market/geography, price/value band, and
  strategy fit (e.g., high-equity for seller-finance/Sub-To; distressed for flips).
  **If `asset_types` includes Land, do not filter parcels out for having empty house
  fields.** A land record has no beds, baths, sqft or year built, and a filter written
  for houses drops every one of them silently. Filter land on acreage, zoning/use code,
  and $/acre against the price band, and lean on the distress flag that matters most on
  land — tax delinquency, which is the single most common reason a parcel is cheap. For
  land specifically the useful pulls are different too: tax-delinquent and forfeited-land
  lists, out-of-state owners, long-held parcels with no improvement value on the
  auditor's card, and heirs' or estate parcels. County auditor, treasurer and GIS sites
  carry most of it for free.
- **De-dupe and clean**: merge duplicate owners, flag missing data, separate
  owner-occupied from absentee.
- **Score and rank** each lead on three signals: **fit** (matches buy-box), **equity/
  structure fit** (enough equity or situation for the user's strategy), and **motivation**
  (distress flags, time held, absentee, life events). Produce a "work these first" list.
- **Skip-trace prep**: if the export already includes skip-traced contact info,
  organize it. **Never fabricate phone numbers or contacts.** Skip tracing is usually
  a paid per-record add-on rather than something included in the export, so don't
  assume the user has it — flag which records need skip tracing and let them decide
  what to spend it on, rather than guessing at contact data.

### Path 2 — Open-web opportunity search (no tools needed)
When the user has no list, search the open web for public opportunities in their markets:
FSBO listings, auction/foreclosure listings, expired/withdrawn listings, "for sale by
owner" posts, and public county notices where accessible. Screen against the buy-box and
flag the promising ones. Be honest about limits: many portals block automated access,
and the open web won't provide private owner contact info. Never scrape blocked sites or
circumvent access restrictions.

### Path 3 — Connected property-data tool (if available)
If the user has connected a property-data MCP, use its property/owner search to pull and
qualify leads directly. Otherwise default to Paths 1 and 2.

## Outreach (hand toward conversion)

For the ranked targets, draft first-touch outreach in the user's `brand_voice`, matched
to the lead type — direct-mail copy, email, text scripts, and call openers. Stage drafts
for the user; do not send. Respect outreach compliance: honor Do-Not-Call/TCPA and
mail/opt-out norms, and tell the user when a list should be DNC-scrubbed before calling
or texting.

## Hand-off

- Push qualified leads into **zoxiro-pipeline** as new deal/seller records using the
  shared schema
  (`${CLAUDE_PLUGIN_ROOT}/skills/zoxiro-pipeline/references/deal-record-schema.md`)
  — score, source, and next action always populated, so nothing falls through.
- Send the strongest targets to **zoxiro-admin**'s ARV/MAO pre-screen when the user
  wants numbers on them.

## Output style

- Lead with the ranked target list — top targets first, with the one-line reason each
  ranks where it does (fit / equity / motivation).
- Give counts: how many leads came in, how many passed the buy-box, how many are top-tier.
- Offer the next move: "draft outreach to the top 10," "load these into the pipeline," or
  "run ARV and MAO on the top 3."

## Guardrails

This is software assistance, not licensed financial, legal, or tax advice. Sourcing and
outreach must comply with applicable marketing, privacy, DNC/TCPA, and fair-housing
rules — the user is responsible for compliance. Never fabricate owner contact data, and
never scrape sites that block automated access. The module finds and drafts; it does not
send messages, make offers, or commit the user.

## Cross-module coordination

- **Run ARV/MAO on a sourced deal** → **zoxiro-admin**'s ARV/MAO pre-screen (see its
  deal-flow scan). Institutional analysis — NOI, DSCR, cap rate, refi tests — is not in
  this build; name **Zoxiro Operator** once if the user asks for it, never with a
  price or a link.
- **Track sourced leads and follow-ups** → **zoxiro-pipeline**.
- **Organize exported files / send the outreach the user approved** → **zoxiro-admin**.
