# Zoxiro

**The Operating Platform for Real Estate Investors.**

Zoxiro is an all-in-one back office that runs inside Claude. It onboards each user
once, learns their company, markets, and buy-box, then handles deal analysis, pipeline,
and administrative work through a single intelligent system. Each user connects their
own tools (CRM, email, calendar, file storage) — their data stays in their own accounts.


**This build: Zoxiro Operator v1.14.47** — modules: orchestrator, admin, pipeline, sourcing, underwriting, auditor.

v1.14.47 — tester error reports go to the Zoxiro service, not to Drive or your drafts.

When the daily check or weekly audit finds a real fault, or you say "something's wrong"
in a chat, the report is now ONE call to api.zoxiro.com (`/issue`), using the same access
key the Underwriter already carries. Nothing is written into your Drive, nothing is
shared, and no draft appears in your Gmail. On the support side every tester's reports
land in one private place, grouped by what went wrong, so the same fault from twenty
installs is one item, not twenty emails.

Reports carry a fixed error code (GMAIL_DEAD, JOB_NOT_FIRED, QUEUE_STALLED, ...), the
build and installed versions, a one-line summary and up to three measured numbers. They
never carry your key, your email address, names, or the contents of any message.

If the service cannot be reached the run says so in its own output and does not fall
back to a file or a draft. If your Zoxiro access has ended, it says that instead.

Drafts and "Zoxiro/Feedback Reports" files left behind by 1.14.31–1.14.46 are not
counted against your own backlog and are not created any more. You can delete them.

v1.14.46 — the away-mode switch is now a switch.

The Away mode card showed a pill reading ON. It was a decorative span. The owner clicked
it, nothing happened, and the reasonable conclusion was that away mode could not be turned
off at all — the off setting was there, but folded inside a dropdown you had to open,
below a badge that looked like the control. That is the whole bug: the thing that looked
like the switch was not the switch.

The pill is now a real button and it reads "ON - tap to turn off" / "OFF - tap to turn on",
so it says what it does rather than only what state it is in. The row of fields dims when
away mode is off, so a glance tells you it is not armed.

Turning OFF takes one click and asks for nothing else. That is deliberate: the moment you
want away mode off is the moment you have just walked back through your own door, and that
is the worst possible time to be made to fill in a form. Turning ON still needs a window,
so with no dates set it says "Pick your leaving and returning dates, then press the switch
again" and writes nothing — it will not invent a trip. Both paths write the same file the
Save button writes, so the dropdown and the switch cannot disagree.

Verified in a browser rather than by reading the code: off-to-on with no dates refuses and
saves nothing, on with dates saves, on-to-off takes one click and skips validation, and the
label, the pressed state and the dimming all follow. Three saves for four clicks, which is
the correct count, and no console errors.


v1.14.45 — the work summary runs nightly now, and the cron trap it walked into.

Yesterday's Work Summary fired Tuesday and Thursday and did arithmetic to cover the week
without dropping a day. It now runs at the END of each working day, Monday to Friday, and
all of that arithmetic is gone. One report, and its window is simply EVERYTHING SINCE THE
LAST SUMMARY — it reads the Work-Summaries folder to find where it left off. That is what
makes it self-healing: a failed run, a day off, a holiday or a week away and the next run
picks up everything since the last one actually landed. Anchored to "today" instead, every
missed run would silently delete a day. Monday's run covers the weekend the same way, which
is right — the owner does not usually work weekends, but when they do it still counts.

It runs at 9:00 PM, not at the end of the afternoon. Usually done by six is not always done
by six, and a report that closes the books at six drops the evening it was wrong about.

THE PART WORTH KNOWING. This is the first task whose DAY of the week matters, and at 9:00
PM local the UTC date ALWAYS rolls forward in every US timezone. Nine PM Monday through
Friday in US Eastern is Tuesday through Saturday in UTC: `0 2 * * 2-6` on EST, `0 1 * * 2-6`
on EDT. Store the local weekdays `1-5` and the job fires a day early, every day, forever —
and nothing in its own output looks wrong, because it reads the clock it woke up on, finds
a window and reports it honestly. The template now carries that conversion as a worked
example and requires the run to state both the local days it intended and the cron it
actually stored, so a mismatch shows up immediately instead of a month later.

ALSO: the model rule is now absolute. Every scheduled task gets an explicit model at
creation — Sonnet 5 for all of them, with the Underwriter on Opus 5 as the single
deliberate exception, because it is the only job producing numbers you would offer against.
The rule now says in so many words that a template added later inherits the default and
that not knowing which model a job takes is never a reason to skip setting one. A task left
unset runs on whatever the account default happens to be and changes underneath you when
that default moves.


v1.14.44 — a written record of your own work, twice a week.

Zoxiro has always reported what IT did — the digests every morning, Weekly Wins on Friday.
Nothing recorded what YOU did, or why. A new "Work Summary" task fires Tuesday and
Thursday and produces that record: for each thing, the change, the reason it mattered, and
a number or filename that proves it. It ends with "Worth telling" — the two or three items
from that window that have an actual story in them, which is what you reach for when you
sit down to write or record something.

The day windows skip the weekend and between them lose nothing. Tuesday reports last
Thursday, Friday and Monday. Thursday reports Tuesday and Wednesday. That covers all five
working days with no day dropped and no day counted twice, verified across a full year of
dates. The three-day Tuesday window is what picks up Thursday's work, so a future run that
"simplifies" it to two days would delete every Thursday from the record — the template says
so in those words, to stop exactly that.

Feeding it is a new file, "zoxiro-worklog.csv", written by ordinary chats as work finishes:
date, area, headline, detail, artifact. The detail column is the why, and it is the only
thing in the whole system that records one — the digests know what the automations did and
deal-events knows what the deals did, and neither knows what you decided or what turned out
to be wrong. Scheduled runs read that file and never write to it, so the report can't end
up summarising its own activity back to you as if it were yours.

One thing this build is careful about: it is the first task whose DAY of the week matters,
and the scheduler stores cron in UTC. At a morning hour the conversion stays on the same
day, but an evening time rolls past midnight and the day-of-week field has to roll with it
— otherwise the task fires a day late forever while every report still looks internally
consistent. The template spells that out, and the run branches on your local weekday rather
than UTC.


v1.14.43 — preferred equity partners, and the operating businesses.

Deal Review and the Admin inbox pass could invent a filename for work that already had
one, and nothing stopped them. The Underwriter and Second Look have carried a rule
against it for months; the two jobs that FEED them did not. So a run would screen the
deal flow, write the rows to "underwrite-queue-additions-<date>.csv" or
"ACTION-REQUIRED-merge-into-<date>.csv", and report SUCCEEDED — while the Underwriter
opened the real queue, found nothing new, and correctly reported a quiet morning. Every
link green, the deals nowhere.

Found on a live account on 2026-09-21: seven side files dated 09-13, 09-14, 09-16,
09-17, 09-19 (twice) and 09-21 — roughly 65KB of screened deals stranded across nine
days. The guard that should have caught it asks whether Deal Review RAN, and it had; it
was built for a job that never fires, not for one that fires and files its work in the
wrong place.

The rule is now a standard clause carried by every job that writes a file of record, naming the four
files of record (underwrite-queue.csv, pass-ledger.csv, zoxiro-profile.yaml, the dated
digest), stating what a side file actually costs, and saying what to do when a write
genuinely fails: report it at the top of the output and put the rows in the digest text,
never in a file nothing reads. It also restates the one that makes appends safe — read
the NEWEST same-name copy first, because this connector cannot overwrite in place and
appending to an older copy silently drops everything written since. Templates 1 and 1B
now carry it; 2 and 8 already stated their own.

v1.14.33 — the offer math moved to the Zoxiro service.

Every Fix & Flip offer figure — MAO at target and floor, the all-in ceiling, the cost
stack, the verdict at the asking price, the stressed margin — now comes from the Zoxiro
service at api.zoxiro.com, fetched with the owner's access key. The plugin gets the inputs
right (comp-derived ARV, the rehab band, the owner's hold period, all six carrying costs)
and prints what comes back, with one line under every set of figures saying where it was
made. Onboarding gains Step 3b, which asks for the key and checks it live before saving
it; "add my Zoxiro key" re-runs that step later. The profile gains a `backend:` block; the
Underwriter task carries the key in its prompt and is the only task that does.

The call puts its changing nonce IN THE PATH — /v1/<key>/fixflip/<nonce> — not in the
query string. Observed 2026-09-18: the fetch cache keys on the path alone, and a second
deal sent to the same path with different inputs came back with the FIRST deal's numbers
for about 25 minutes. A changing query parameter did not defeat it. This build requires
Zoxiro API 0.3.1 or later, which serves that route.

Four outcomes, and nothing falls between them. A usable 200: print what it returned. A
403: produce NO offer figures at all and say access has ended — comps, ARV, rehab and the
spread ranking still get done, and a scheduled row waits as "Pending Zoxiro Access". A
422: the inputs were wrong (almost always carrying costs left at zero) — fix them and call
again. Anything else, a 404 and a mid-deploy included: work the model locally and label
every figure "computed locally — Zoxiro service unreachable". An outage is nobody's fault
and should not cost a morning; an ended key is a decision, and the plugin no longer carries
a second copy of the offer math to route around it. The key is never printed anywhere.

The daily check pings the service's health; the weekly audit runs whoami, re-stamps
`backend.verified`, and confirms the Underwriter's prompt carries the same key as the
profile. The Fix & Flip workbook still ships and is now filled FROM the service's answer.

Orchestrator §6b is the protocol; underwriting, admin (Offer Sheet), the auditor and
Templates 2, 4, 5 and 8 refer to it.

v1.14.32 — the Drive share from 1.14.31 now actually tells support it happened.

1.14.31 shared each failure report with support's Drive silently — a deliberate choice
at the time, to avoid 55 testers' notification emails piling up. That was wrong: a
silent share reaches nobody, because nothing on support's side was watching for new
files to appear. The three feedback paths (daily check, weekly audit, "report an
issue") now leave Google's own share-notification email turned ON. The moment a report
is shared, Google emails support directly — the same email you'd get if any Drive user
shared a Doc with you. No new code, no polling, nothing to watch. That email is the
notification.

v1.14.31 — a failure report reaches support even if you never open your Gmail drafts.

The daily system check, the weekly audit, and "report an issue" already put a Gmail
draft to support on a real failure — but a draft only reaches anyone once the owner
opens their drafts folder and presses send, and most owners don't. Every one of those
three now ALSO writes the same report straight into a "Zoxiro/Feedback Reports" folder
in the owner's own Drive and shares that one file with support directly, so it shows up
under support's "Shared with me" with no owner action at all. Both paths fire together
on a real failure, and neither replaces the other: the Gmail draft still needs the
owner's button before anything leaves their name, the Drive share is a file permission
and doesn't wait on them. A healthy run still does neither — silence on success is
unchanged, and a support draft or Drive file is never counted against the owner's own
unsent-draft backlog. Nothing sends without the owner's say-so; the Drive share was
never mail to begin with.

v1.14.30 — deleting is a two-step act, always.

Nothing is deleted on a single instruction any more — not a scheduled task, a chat, a
dashboard, a pinned item, a Drive file or a Gmail label. The chat lists what would go,
by name, asks, and acts only on a separate yes. Ordinary work is untouched; this fires
only when a deletion is being asked for. Added after an owner's voice-to-text tool,
aimed at a second computer, dictated into an open Zoxiro chat and all eleven scheduled
tasks were deleted in one pass (2026-09-17). Orchestrator §6a; the auditor now points
at the same rule.

v1.14.29 — the hold period is the buyer's choice, and carrying cost means all of it.

The Fix & Flip calculator asked for a holding period and then costed only three things
against it: property tax, insurance and utilities. That is not what it costs to own a
vacant house. The model now takes the months the buyer expects to hold it — buy to sale,
defaulting to 6 rather than 5 — and costs every monthly item over that period: taxes,
insurance, utilities, lawn and snow, HOA or condo dues, and an "other" line for alarm
monitoring, board-up, dumpsters and the rest.

Loan interest stays on its own line and is deliberately not folded in. It scales with the
amount financed rather than with time, and a cash buyer carries the same house at the same
monthly cost with no interest at all; merging them would make one house show two different
carrying costs depending on who bought it.

The workbook now prints a warning on the carrying-cost row when an ARV has been entered
and every carrying input is still blank — the exact condition that produces an offer about
$2,950 too high on a deal like the one in the Offer Sheet example. Verified by real
recalculation, not formula inspection: the restructured model reproduces the known-good
$78,229.58 target and $84,117.92 floor to the cent, a $150/month lawn line moves the offer
by $1,054 as the math says it should, and the blank template still opens with no errors.


v1.14.28 — the offer-sheet worked example now adds up, and holding costs can never be zero.

The Offer Sheet's worked example printed a subtraction chain that did not reach its own
answer: the "points, interest, carry" line read $18,835 where the deal's real figure is
$15,977, so the four lines subtracted to $75,372 above a stated MAO of $78,230. The other
lines were right and the MAO was right — only that one line was wrong, which is the worst
version of the bug, because the number an owner would act on looked correct while the
arithmetic under it did not close.

Corrected, and the example gained the rule behind it: holding costs are never zero and
never omitted. Holding is taxes, insurance and utilities over the hold — $3,360 across
eight months on the example deal. Because the model solves the purchase price out of the
cost stack, every dollar of omitted holding raises the offer by about 88 cents, so a
forgotten $3,360 recommends paying roughly $2,950 more than the deal supports. Omitting a
cost is not conservatism; it points the error at overpaying.


v1.14.27 — two rules restored to the daily system check.

Nothing in this build changes what the plugin does. It fixes the shipped copy of
`references/scheduled-job-templates.md`, which is the file a refresh regenerates every
scheduled prompt from. Two rules had gone missing from TEMPLATE 5 (Daily system check)
and were caught only by diffing generated prompts against the live ones before pushing:

- The weekly-audit freshness test must read the SCHEDULER'S last-fired timestamp, not
  search Drive. The audit returns its report as task output and writes no Drive document
  at all, so a Drive-based test can only ever fail — reporting a healthy audit as missing,
  every week.
- The unsent-draft count must page the whole mailbox. A draft list caps a page at 50 and
  returns a `nextPageToken`; a single call silently ignores everything past the first 50.
  The live incident this rule exists for was 207 drafts.

The live scheduled prompts already carry both rules — they were restored before the
1.14.26 push. This build makes the same true of the template they are regenerated from,
so a future refresh reproduces them instead of dropping them again.


v1.14.26 — the setup-restarts-itself bug, and feedback that reaches somebody.

The orchestrator now resolves the profile the way every scheduled job already did:
a file-first Drive search on the `zoxiro-profile` stem, with a connected local folder
as the second source rather than the only one. Looking locally first is what made the
lookup fail in cloud chats and in desktop chats that had lost their folder grant — and
a failed lookup was being read as "new user", which is how a returning owner with
eleven live tasks got offered setup.

A failed lookup is no longer evidence of anything. Onboarding now has to PROVE the user
is new before it asks a single question (§3c): Drive, the scheduler, Gmail labels and
the local folder are all checked first, any hit stops the wizard, a check that could not
run is never counted as an empty result, and the profile stem is searched once more
immediately before the write. If the user clicks "I'm new" while the evidence says
otherwise, the evidence wins and they are asked to confirm.

Feedback now has somewhere to go. The provenance paragraph in every job prompt gained a
verb — it used to say a product change "belongs back with whoever maintains Zoxiro",
which is not an action; it now writes the request up and leaves a draft in the owner's
drafts. The daily system check and the weekly audit do the same on a real failure, so a
fault reaches support even when the owner never mentions it. All of it is draft-only;
the never-send guarantee is unchanged. A passing run drafts nothing, repeat faults are
not re-drafted, and these drafts are excluded from the unsent-draft counter that would
otherwise alarm every morning about mail the system wrote to itself.


v1.14.25 — a scheduled prompt now says what it is.

Every generated job prompt carries a short provenance paragraph under its build stamp: it
came from a template, a future install rewrites it, and a change belongs in the profile or
back with Zoxiro rather than in the prompt text. The orchestrator gained a matching rule
(§3b) for routing "make the job do X" requests, and the weekly audit now reports a prompt
that has lost one of its guaranteed paragraphs as informational — the edit works, and the
next install erases it.


v1.14.24 — the board templates caught up with the live boards.

Every refinement made directly on the live Morning Dashboard and Pipeline Board since
early September is now in the shipped templates, so a fresh install gets the same pages:
three real columns on the pipeline board with Passed third and passes ageing off after 30
days; a baked snapshot on the dashboard so it shows real numbers the instant it opens;
an "Open Connectors" link on every connector error; the standing work block as an
optional profile setting (`work_block`); and Calendar kept out of the connector manifest
until its consent prompt behaves, with the switch documented in the template.


v1.14.22 / v1.14.23 — the templates caught up with the jobs.

Every fix made directly to a scheduled job since the rename now lives in
`scheduled-job-templates.md` too, so an install no longer quietly rewrites a job back to
an older version of itself. Admin now asks "is this the business, or is it the owner's
life?" before it asks who sent a message: receipts are always kept and filed under
`Receipts`; consumer marketing goes to `Bulk Promotional` and never gets a folder of its
own; only senders that touch the real estate operation or the capital desk get a label
named after them. New optional job, Second Look: re-checks the asking price on
properties the Underwriter already passed on and queues any that come back into range.
1.14.23 restores the audit's profile shape check (a known city must resolve to three
rehab numbers) that 1.14.22 shipped without.


v1.14.14 — any asset, and the business that comes with it.

Two rules, both about refusing to hand back one confident number.

THE ASSET LIST IS NOT A GATE. A billboard easement, a cell-tower ground lease, a marina, a
parking lot, farmland — anything income-producing gets underwritten now, never skipped as
"not covered". Name the income stems separately, sort them by durability (contractual,
then seasonal, then operating margin — revenue is not income), normalize on actuals, find
the expense floor, then find what could END the income: a ground lease shorter than the
hold, a permit that doesn't run with the land, a single tenant who IS the revenue. Then
say which rules you improvised, because an improvised number reads exactly like a tested
one and that sentence is the only difference anyone can see.

AND ASK FIRST WHETHER A BUSINESS IS ATTACHED. A campground with a store and cabins, a
motel, a car wash, a laundromat — the real estate under them was always covered; the
business stapled to it never was. The tells are FF&E, inventory, employees, "turnkey",
"owner will train", a franchise name, a P&L carrying the owner's own salary, or revenue
that stops the day the seller walks out.

When one is there you get two numbers and they never blend. The real estate, underwritten
in full at your cap rate on durable property income. And the business — named, quantified,
and deliberately not valued — with the verdict leading "REAL ESTATE ONLY — going concern,
business not valued" so nobody mistakes a floor for a purchase price.

Never cap business income at a real-estate cap rate. It doesn't transfer automatically, it
often depends on the seller personally standing behind the counter, and running it through
an 8-9% cap pays a real-estate price for a stream that behaves nothing like rent. Ignoring
it entirely can undervalue a park that was worth the ask. Separate, state, and let the
investor decide.

v1.14.13 — every module now knows what build it is.

Nothing in a plugin runs when you install it. A plugin is files on disk; there is no
installer and no startup hook. So the refresh that brings your dashboard and your
scheduled job prompts current can only happen on the first MESSAGE after an upgrade —
and until this build, only a message that reached the orchestrator counted.

That left a hole you could walk through on day one. Install the update, then open a chat
and say "do my inbox" or "underwrite 412 S 2nd" — those go straight to a module, the
version check never runs, and the dashboard you open every morning quietly stays on the
old build while every report reads as a success.

All four working modules — Admin, Underwriting, Pipeline and Sourcing — now read the
installed version against the profile before they do anything. Matching: they say nothing.
Different: an interactive session runs the orchestrator's refresh first and then does the
work; a scheduled or headless run, which cannot create artifacts at all, says so in one
line instead of claiming a rebuild it never performed.

No module may write a build stamp. Only the orchestrator does that, and only after every
update it attempted actually returned — a stamp written on intention is what left one
dashboard five builds behind while its owner reinstalled three times.

And a stale build never blocks the work. It gets named and fixed; the thing you asked for
still happens.

v1.14.12 — three comps, next door, in the last six months.

The rule the owner has always used by hand is now the run's own: THREE COMPS IN THE
SUBJECT'S OWN NEIGHBORHOOD, SOLD IN THE LAST SIX MONTHS. Find those three and the search
is finished — no reaching out for a fourth, no going back a month for something better.
Most screens end right there.

Everything below only happens when three cannot be found. Two ladders, and the run climbs
them ONE RUNG AT A TIME,
checking at every rung and stopping at the first one that works. If six months is empty,
go back one month, check, then one more. If the neighborhood is empty — which happens on
rural and semi-rural stock, and is normal rather than a failed search — step out half a
mile, then one, then two, then five. Never open the search wide because the subject looks
rural; a run that starts at five miles and twelve months will always find three comps, and
the ARV it produces looks exactly like a well-supported one.

The three are comps that SURVIVE the ±150 band and the attribute match — not three raw
sales that get excluded on inspection. Count what survives.

Distance widens first, but only inside the same submarket. The moment the next rung would
cross a school district, a municipality, a highway or a river, the run goes back a month
instead. A stale comp in the right submarket beats a current comp in the wrong one.

Every comp set now states the rung it landed on in one line — "comps within 0.5 miles,
sold in the last 6 months", or "had to reach 1 mile and 8 months". When both ladders run
out short of three, two surviving comps is the floor and it is reported as a thin set;
fewer than two is Pending ARV Confirmation, never a stretched comp.

v1.14.10 — a walkout basement is not square footage, and not an ordinary basement either.

Basements were one line: none, unfinished, or finished. They are now three tiers — walkout
(an exterior door at floor level), daylight or lookout (above-grade windows, no door), and
standard (all four walls below grade).

None of the three count as above-grade square footage, and none may be used to close the
±150 comp band. ANSI Z765-2021, which Fannie Mae mandates for conventional appraisals,
requires a level's finished floor to sit at or above grade on ALL sides to count as gross
living area — a walkout has at least one wall below grade by definition, so the whole level
is below-grade finished area on the 1004 however well it is finished. That is not a house
rule. It is the standard the appraisal supporting the exit will use, so counting it in gross
living area walks the ARV away from the number that comes back at closing.

What does change is the value. Finished below-grade space adjusts at roughly 50-75% of
above-grade price per foot; a walkout sits at the top of that band, a standard finished
basement at the bottom, daylight between, and the run says which tier it used. Comps are
matched tier to tier — a walkout subject screened against flat-lot basement comps is
under-valued, and the reverse over-valued. Listings use the word "walkout" loosely, so the
run verifies it: the tell is a door at floor level.

And a separate entrance is priced on its own rather than as discounted square footage,
because it is an in-law suite, a lock-off or a rental unit. On the right property that is
worth more than the entire below-grade adjustment.


v1.14.9 — comps now have a square-footage band.

Plus or minus 150 square feet, above-grade against above-grade, and a comp outside it is
normally excluded. If a thin market forces one in, the run says so and does not let that
comp's price per square foot set the band. Fewer than two comps surviving the band routes
the row to Pending ARV Confirmation rather than widening it — stretching to reach three
comps is how a thin market produces a confident number.

The error this catches runs in a predictable direction and looks fine either way: smaller
houses carry an inflated price per square foot and larger ones a deflated one, so an
undersized comp drags the blend up and an oversized one drags it down. Both read as
reasonable in the write-up.

It was measured, not assumed. On the developer's own screens the day before this shipped, a
1,463 sqft condo was blended with two 1,175 sqft units and an 1,155 sqft sale — 288 and 308
square feet under — and the run then hand-adjusted the band downward to undo the small-plan
inflation it had just imported. A 1,161 sqft house took a 925 sqft comp whose $191.89 per
foot was the highest of its five, and the write-up had to note the high comps were inflated
by small-home effects. In both cases the band was doing its work in hindsight. Now it does
it at selection.


v1.14.8 — comps are now matched on what a buyer actually pays for.

Beds, baths, square footage, build era and submarket were being matched every time, and
matched well — same-complex sales, submarket boundaries respected, non-arms-length sales
excluded. Everything else a buyer pays for was not. Measured across 26 screened properties
on the developer's own ledger: "ranch" appeared twice, garage four times, lot size three.
Stories, basement and pool, never.

That is the same failure shape as a misread property type. A ranch and a two-story of
identical square footage are different houses and do not trade at the same price per foot,
but blend one into the other and the ARV comes out looking exactly like a good one.

Comps are now matched on stories, basement (none / unfinished / finished, and finished
basement square footage is never above-grade square footage), garage and car count, lot
size, and pool or any other separately-paid improvement. Where a comp cannot be matched on
one, the underwrite adjusts and says so in the ARV text — an adjustment folded silently
into a per-square-foot blend is indistinguishable from an error. Where a listing does not
state a field, it says that instead of guessing.

And it now looks at what is next door rather than only at the house. A business, gas
station, car lot, industrial use, busy road, railway or boarded neighbour moves the price
and appears in no listing field. The rule of thumb it starts from — a business directly
adjacent costing roughly $10,000 — comes from the owner's own experience in these markets,
and the run is told to revise it where the comps say otherwise. The same test runs on every
comp: one that sold cheap because it backs onto commercial drags the blend down for a
subject that does not.


v1.14.7 — the Underwriter no longer waits for a click nobody will make.

The Underwriter is the only job that leaves the connectors and goes out to the open web:
sold comps come from listing sites. Where this environment asks permission per domain, an
unattended run stopped at the first one and waited. Observed on the developer's own account
2026-09-10: it fired at 12:13 and finished at 13:00 — 46 minutes against 8 and 12 for the
two jobs ahead of it — and only because the owner opened the task and clicked through the
prompts. The scheduler recorded SUCCEEDED. Every health check read clean.

That is the worst shape a failure can take here. A run waiting on a dialog looks exactly
like a run doing careful work, and the watchdog is correctly forbidden from re-firing
anything still running, so nothing intervenes and no alarm fires.

Three changes. Setup now asks you to approve the listing sites once, before the first
Underwriter run, alongside the existing connector-permission check — this is a first-run
problem, so it belongs in setup rather than in a later audit. The Underwriter itself never
waits on a blocked fetch: it records the domain, moves to the next comp source, leads its
report with "BLOCKED ON APPROVAL" and the list, and marks any verdict resting on a single
comp as thin. And the daily check now names an approval block by name instead of reporting
a slow run.

Also in this build: the pipeline board's logo. Every word in that file said Zoxiro — title,
heading, even the image's alt text — while the picture in its header was still the old SX
mark, 68KB of base64 that no text search can read. It shipped in Core from the rename until
now. It is replaced with the shield, and the build now decodes every embedded image and
fails on any whose bytes are not on an approved list, because a text scan cannot see a
picture. The board template also drops from 102KB to 17KB, which retires the warning about
opening it.


v1.14.6 — the weekly cleanup job now does the tidying instead of asking you to.

Every connector write leaves a duplicate, because Drive cannot overwrite a file in place —
so the working folder gained roughly one extra copy of underwrite-queue.csv, pass-ledger.csv
and zoxiro-profile.yaml per day. Until this build the weekly job could only remind the owner
to clear them by hand, on the reasoning that a scheduled run cannot reach a local disk. That
reasoning was half right: it cannot, and it does not need to. Drive for Desktop mirrors the
folder, so a file moved in Drive moves on the owner's computer too. One move, both places.

The job now finds or creates a "_to_delete" folder inside the working folder and parks
superseded copies there. It parks; it never deletes. Deleting stays with the owner, because
an unattended run cannot see what it is about to destroy and cannot undo it.

Four brakes make that safe to run unwatched, and the build now fails if any of them goes
missing. It never moves the newest copy of a file — that is the live one every other job
resolves. Nothing is eligible until it has been superseded for seven full days: on
2026-09-08 a run wrote a 1,422-byte copy over a 73,438-byte queue, and without that floor
the sweep would have carried off the good copy days before the loss was noticed, taking 23
property rows with it. Files are moved and never renamed, because renaming resets a file's
modified time and Drive search is folder-blind — a renamed file in the holding folder
becomes the newest match for its own name and the next job picks the discarded copy. And
before touching a name at all, the sweep compares the newest copy's size against the largest
older one; if the newest has collapsed to under half, it moves nothing for that name and
says so at the top of its report. That last check is the one case where "newest wins" is
wrong, and it gives the system a standing weekly detector for a silent truncation that
otherwise surfaces weeks later, if at all.

The task is renamed from "Weekly file-cleanup reminder" to "Weekly cleanup sweep", since it
no longer only reminds. Downloads and anything else on the machine still need a live
session; the job says so in its closing line.


v1.14.5 — the deal scan now names the property type, and nothing offers to build a board that cannot be built.

Two defects a live morning run surfaced on 2026-09-10.

The bird dog was mis-typing properties, three times in one week, and a wrong type is the
one error that produces a confident verdict instead of an obviously bad one. An 1895
duplex was queued as a "4 bed / 2 bath" single-family because the listing added the two
units together — screened against 4-bed suburban houses it read 40-70% more valuable than
it was. A condo in a named community was queued as a detached house, with its HOA listed
at $175/month while three verified in-community sales reported $230-250: about $3,000 a
year of carrying cost that never entered the model. The deal scan now settles the type
BEFORE it screens anything, watches for the specific tells (doubled bed/bath pairs, "each
side", separate meters, two kitchens, a rent roll; "condo", "townhome", "attached",
"villa", "unit #", a community name, any HOA or maintenance fee), records the stated fee,
and is required to write "type uncertain" rather than guess. The ship gate fails a build
whose deal-scan template no longer forces that call.

And every prompt that offered to build a Pipeline Board has stopped offering. Nothing on
any surface has a tool that creates or updates a Cowork sidebar artifact — a cloud session
established that on 2026-08-25, a desktop session enumerated every tool it had and
confirmed it on 08-27, and three sessions in between reported building one anyway. The
instruction shipped regardless, so a real morning report ended by telling the owner to open
the desktop app and ask for a board that could never appear. An impossible next step at the
bottom of an otherwise correct report costs more than the board would have been worth. The
queue file is the pipeline of record; the surfaces that read it already work. The gate now
fails any build that promises the board again.

v1.14.2 — the morning is two jobs now, and a watchdog restarts whatever fails to start.

The inbox pass and the deal scan used to be one scheduled job. On this product's own
account it fired at 06:01, produced nothing for four hours and forty-eight minutes, and
finished at 11:19 — five and a quarter hours for about thirty minutes of work, three
mornings running. Every other task on that account finished in minutes throughout, so the
scheduler was never the problem: the single oversized prompt was. Split into two, the same
work on the same mailbox took 9m 17s and 11m 00s the next morning. Both templates now ship
separately and the ship gate refuses a build that merges them back.

A run watchdog ships for the first time. It runs at one minute past every hour, reads the
scheduler, and starts any job whose slot closed without the job ever running. It names no
specific job, so it is correct for three jobs or thirty and stays correct when jobs are
renamed or re-timed. It waits a full hour before judging a slot, because these runs
routinely start ten or fifteen minutes late and a second copy of an inbox job would draft
and archive the same mail twice. It never fires alongside a run that has started but not
finished. And it says nothing at all when everything is healthy, which is almost every
hour — a watchdog that speaks hourly is one you mute, and then the hour that matters is
muted too.

The Underwriter can now tell an empty queue from a broken upstream. It used to report "no
new rows, nothing to underwrite" as a clean day when the job that fills its queue had
never run — every report reading healthy while a full day of deal flow went unscreened. It
now checks that Deal Review both fired and finished before calling an empty queue normal,
and says so plainly when it did not.

The morning jobs no longer name each other by clock time. They refer to each other by name
and position in the chain, and carry an explicit instruction to read the scheduler rather
than assume an hour. Prompts that hardcoded 06:00 and 07:00 became lies the first time the
schedule moved, and the ship gate now rejects any template that ties a named job to a time.

Also: the platform rules now cover daylight saving. Crons are stored in UTC and do not move
when the owner's clock does, so twice a year every fixed-hour job lands an hour away from
where it was put — silently. Onboarding now warns about it and offers a one-shot correction
on each changeover date.

v1.14.0 — the flip screen now has to show its downside case, and the admin caps went to 50.

Two arithmetic defects were found in the underwriting workbook while checking that claim
and both are fixed. The COMBINED stress test — the "survive a recession" one that moves
all four variables at once — multiplied operating expenses by a percentage typed inside
the formula instead of reading the visible knob, in 25 cells across Cash_Flow_10yr and
Refi_Timeline. It was a leftover from when the defaults were 10% and 15%. Every
single-variable test followed the knob; the combined test ignored it. Measured before the
fix, setting the OpEx knob to 8% and to 20% both produced the same -45,000; after, they
produce -41,000 and -65,000. The danger was asymmetric and that is why nobody caught it:
the stale 10% ran HARSHER than the 8% knob, so the file never looked wrong — until
someone raised the knob to be more careful, at which point the worst-case test silently
became the least severe of the five. Six tab banners also still carried the pre-rename
brand name in capitals, which a case-sensitive sweep had passed as clean.

The ship gate now fails the build if any stress formula multiplies by a typed percentage
rather than its knob cell, and the old-brand check reads inside the workbooks and ignores
case.

Underwriting was already conservative on the commercial side: a park has to show what the
price yields if actual NOI lands 20% under the pro forma, and the cap-rate floor is
measured on in-place T-12 rather than a projection. The flip side had no equivalent test,
which quietly made the residential process — the higher-volume one — the looser of the
two. Every BUY or WATCH on a flip now states a second set of numbers at the same purchase
price, with ARV down 5% and rehab up 15% applied together, and says whether the stressed
margin still clears the floor. It is not a kill switch: a deal that passes the base case
and fails the stress keeps its verdict and gets labelled "thin under stress", because the
owner decides, not the model.

The two figures are not arbitrary. ARV is the softest input in the model and everything
downstream is computed from it — on a $185K ARV a 5% overstatement is $9,250, about a
third of a $30K target profit. Rehab is the second softest while its band is still
derived rather than calibrated from the owner's own closed jobs. They move together in
reality, so they are stressed together.

Also in this build: the admin inbox batch went from 20 to 50 and the Deal Alerts feed cap
from 30 to 50, after a heavy day out-ran both; each admin run now stamps a finish time in
its digest so the next change to those caps is decided on a measured run rather than a
hunch.


v1.12.3 — mail was being archived but never filed.

On top of 1.12.2: filing and archiving are now two separate decisions, because writing them as one cost
real filing.

The instruction to label mail used to hang off the end of the archiving sentence. So any
thread the job deliberately LEAVES in the inbox — a security alert, a failed payment, an
email with a draft waiting on the owner — skipped labelling along with archiving. Found
on a live account: three Anthropic billing emails sat in the inbox unlabelled, and no
`Anthropic` label had ever been created despite a year of receipts from them in the
mailbox. The owner reported it as "it fails to create a label," and she was right.

Every thread now gets its label answered FIRST and independently of whether it leaves the
inbox: bank mail by institution, service and tool-account mail — receipts, invoices,
billing notices, payment failures, subscription notices — by the service's own name,
creating the label if it does not exist. Staying in the inbox is a statement about
visibility; it was never a statement about filing. The digest now reports labels CREATED
separately from labels applied, because a new label is the system learning the owner's
world and worth a line.

Two related fixes ride along. Account-STANDING notices (a failed payment, a paused
subscription, an expiring card) are now named as their own kind of alert — they are
financial AND service AND urgent, and whichever bucket they landed in the filing fell
through. And the Deal Alerts feed query gained a 90-day guard: importing the portal
filters with "apply to existing mail" sweeps years of archived history into the label —
over a thousand threads on the live account — and a feed read oldest-first without the
guard would spend weeks stale-skipping 2024 listings while this week's deals starved.

The build gained a guard of its own after this one. Twice now a shared-source edit has
used a single-occurrence replace against text that appears once per tier, landing the
change in ONE tier and silently skipping the other — the Second Look ledger in 1.10.2,
the Deal Alerts date guard here. The ship gate now asserts by name that the load-bearing
passages exist in BOTH tiers, so a half-applied edit fails the build instead of shipping.

v1.12.2 — the update stops asking permission to do its job.

On top of 1.12.1: the update refresh no longer asks permission — it just happens.

The last build put a pop-up in front of the refresh: "Zoxiro updated — refresh your
jobs now?" The owner test-drove it as a new user would and called it what it was: a
redundant step. If the user has to say something to wake the system anyway, a button
asking them to approve the thing they obviously want is friction dressed up as
consent — the phrase plus a click instead of just the phrase.

So the refresh is automatic again, the way it originally was, with the one improvement
that survives from the pop-up experiment: it announces itself in one plain line —
"You've updated to X, I've brought your scheduled jobs and dashboard current" — at the
top of the reply to whatever the user actually said. Say "good morning" after an
install and the update simply happens on the way to your briefing. Normal use is the
update path; there is no phrase to know and no button to click.

The rule that fell out of it, kept for every future feature: ask about NEW things,
never ask about keeping existing things correct. Creating a job the user never approved
still asks — that is a real decision. Refreshing what they already have does not.

v1.12.1 — updates offer what's new instead of silently skipping it.

On top of 1.12.0: upgrades now offer new features instead of silently skipping them.

The upgrade refresh rewrites the scheduled jobs a user already has — which means it
could never GIVE them a job they never had. A feature shipped as a new scheduled
template (Weekly Wins was the first) would reach new users at onboarding and existing
users never, and nobody would notice, because a job that was never created fails
nothing. Now, after every upgrade refresh, the system compares this build's templates
against the user's automations and offers anything new in one pop-up — yes or not now,
recorded either way so nobody is nagged twice in one build.

Also: the Weekly Wins job now backfills quietly on accounts with history. A user
upgrading with forty deals already in their queue does not get "your first deal ever!"
celebrated months late — pre-existing milestones are recorded silently, and only what
happens from now on earns the sentence.

v1.12.0 — setup ends with proof, and the invisible work becomes visible.

On top of 1.11.4: five additions, all aimed at the same thing: the user FEELING the system work.

**Setup now ends with proof, not a promise.** Onboarding used to finish with "the magic
happens tomorrow at 7am" — fifteen pop-ups answered, nothing received yet. Now, before
the home screen appears, Zoxiro runs a small live sample of the morning job while the
user watches: their five most recent threads classified, today's calendar, any deal in
that mail screened against the buy-box they just set. Nobody finishes setup without
having already seen the product work once.

**Weekly Wins.** The system's best work is invisible — mail handled before the user is
awake, deals screened, a backlog draining. Nobody renews for work they never saw. An
optional Friday-afternoon note, ten lines or fewer, says what actually happened this
week: emails handled, drafts written, properties screened and queued, how much lighter
the inbox got, who is still waiting on a reply. Facts only; a quiet week is reported as
a quiet week.

**Show the Math.** When the user challenges a verdict — "why did you pass on 123 Main?"
— the system replays the STORED screen: the ARV and its source, the rehab band and where
it came from, the arithmetic line, the gap, the recorded reason. Never a fresh guess
dressed up as a memory: if no screen was stored, it says exactly that and offers to run
one. Trust in the forty verdicts the user didn't check rests on the one they did.

**Report an Issue.** "Something's wrong" now triggers a capture, not a quiz. One
question — what did you expect, what happened — then the system gathers its own
evidence: versions, task stamps, the last digest, connector reachability, into a single
document ready to send to whoever supports the install. If the evidence already names
the cause, it says so and offers the fix on the spot.

**Milestones.** First deal ever queued, first time the backlog hits zero, first rehab
band calibrated from the user's own closed deal, first time the waiting-on-you list
clears. Each is recognized once, ever, in the Weekly Wins note — and never repeated,
because canned praise is worse than none.

v1.11.4 — two job prompts were shipping unstamped.

On top of 1.11.3, a full diagnostic pass found two real defects, both now fixed.

Two scheduled-job prompts were shipping WITHOUT their build stamp on the first line —
the daily inbox job and the weekly cleanup sweep in Core. An unstamped prompt is
invisible to every staleness check this system has: the upgrade rewrite, the daily
build-stamp warning, and the audit all read that first line, so a task created from an
unstamped template could never be told apart from a current one. The rule said "every
prompt, no exceptions"; two prompts were exceptions. Both now open with the stamp, and
the diagnostic that caught it checks every fenced prompt, not a sample.

Also fixed: the Operator template file listed the Weekly System Audit before the Weekly
Cleanup Reminder, so the numbered templates read 1, 2, 4, 3. Cosmetic — tasks are created
by heading name, not file order — but a numbering that reads as an error erodes trust in
everything around it.

v1.11.3 — the deal scan was being throttled by the inbox clean-up.

On top of 1.11.2, the deal scan and the inbox clean-up are no longer competing for the same budget, and
listing alerts can now bypass the inbox entirely.

The two jobs used to run over the same batch of threads, which meant the deal scan could
only ever see the mail the admin pass happened to reach. On an account taking around 29
listing alerts a day against 8 recent slots, roughly two thirds of the owner's own deal
flow was never looked at — not rejected, never read. That is a worse problem than a
cluttered inbox and it was completely invisible, because the digest only reported on what
it had seen.

They are separate now, for a reason worth stating plainly: the two jobs do not cost the
same. Pulling an address and an ask out of a listing alert is cheap. Classifying,
drafting, filing and archiving a human email is expensive. Tying the cheap work to the
expensive work's ceiling is what capped sourcing.

So portal mail — Zillow, Realtor.com, Crexi, Redfin, Trulia — gets routed by a mail
filter into a `Deal Alerts` label that skips the inbox on arrival. A mail rule does that
instantly and for free; having a language model read each one to decide it is a listing
alert was paying a premium for a sorting job. The bird dog reads that label at its own
cap, defaulting to 60 a run rather than sharing the inbox's 20. The admin pass then works
an inbox holding human and service mail only.

One rule guards the obvious risk. A real person can write from a portal address, and a
filter that archives on arrival would bury them. So anything in the feed that is not a
bulk digest — it addresses the owner directly, asks a question, reads like one human
writing to another — gets put BACK in the inbox and named at the top of the digest. When
it is unclear, it is treated as personal: a false alarm costs one line, a miss costs a
deal.

The audit counts the feed too. A `Deal Alerts` label filling up with unreviewed threads
means the filters are working and the scan is not — a clean inbox with the deal flow
piling up behind a label nobody opens. That is the failure mode this design could have
introduced, so it is checked for by name.

v1.11.2 — the inbox was not broken, it was outnumbered.

On top of everything in 1.11.1, the daily run now measures how much mail is ARRIVING and compares it to how much it
handles, and says so in one line of every digest.

This was found on a live account and the numbers matter. Roughly 29 threads a day were
arriving. The batch was 20, and of that 20 only 8 slots could touch recent mail. So the
recent rung grew by about 21 threads every day, aged past seven days, and dropped into the
backlog the next pass was trying to drain. The backlog was not a pile being worked down —
it was being refilled from above faster than it emptied. Every run reported a clean
success and every run was telling the truth: 20 handled, 20 verified out of the inbox,
nothing missed. The owner saw an inbox that never got cleaner for weeks and reasonably
concluded the automation was broken. It was not broken. It was outnumbered, and nothing
anywhere said so.

That is now a headline finding rather than an invisible one, with the two real fixes
named: filter the repetitive senders at the mail provider so they never reach the batch,
and size the recent reserve to measured arrivals instead of a constant. The audit runs the
same arithmetic and reports what share of the inbox is portal and notification mail — on
that account it was 201 threads of 451, each being read individually by a language model
to do what a mail filter does instantly and for free.


v1.11.1 — the auditor is now measured against the failures it missed.

Every failure this system has had in the last month was found by its owner, not by the
module whose entire job is finding them. So the auditor now ships with a register of
those nine incidents, and the build refuses to ship if any of the checks that catch them
has gone missing from the auditor's instructions. The register's rule is that a bug which
reached a user gets written down before it gets fixed.

Four checks were genuinely absent and are now in:

The reply-gap scan is checked for COVERAGE, not just for running — unsent drafts in the
mailbox counted against threads marked awaiting reply, which is how 26 finished drafts
read as "no replies to send" for weeks.

Each inbox job's work step is checked for being BOUNDED. A prompt can carry a current
build stamp and still be shaped to fail: the morning job parked for weeks because its
first step read all 452 threads before doing any work. The batch size was right the whole
time, which is why changing it never helped.

A parked run is now told apart from a run that never fired. They look identical from
outside and have opposite causes — a parked run's digest opens and never closes.

And drafts are READ, not counted. A draft that talks about funding in reply to someone
selling a house is a high finding, quoted, with the recommendation to delete rather than
send it. This one exists because another assistant installed on the same account can win
a request before any Zoxiro instruction is read — the audit is the only place that sees
what it wrote.

v1.11.0 — your automations now tell you when they are out of date.

Installing a new version of Zoxiro has never rewritten your scheduled jobs, and it
still doesn't — a task's prompt text lives in the scheduling platform, not in the
plugin. Nothing about installing touches it. So a job could keep running rules that
shipped months ago, fire on time every morning, report success, and be wrong, with
nothing anywhere saying so. The only way to fix it was to know a phrase nobody had
told you.

The Morning Dashboard now reads the build your jobs were last written from and, when
it is behind what you have installed, says so at the top of the page — naming both
versions and the exact words to say, which you can click to select. The stamp is
written only after every task update actually returns, so it can report "behind" but
never a false all-clear.

Three things now say it, and they cover different people. Starting a new chat after an
upgrade puts a pop-up in front of you — the same kind onboarding uses — asking whether to
refresh now, rather than doing it quietly and mentioning it in a paragraph you scroll
past. Opening the Morning Dashboard shows the banner. And every scheduled job — not just
the inbox one, which was the only one carrying this until now — puts the warning at the
top of what it reports.

That last one is the one that matters most, because it needs nothing from you: a
scheduled job runs in a fresh session every morning whether or not you open a chat or a
dashboard. There is one gap that cannot be closed from inside the plugin, and it is worth
knowing: a conversation you opened BEFORE installing is still running the old
instructions for as long as you keep using it, because a session loads its skills when it
starts. After an update, start a new chat.

Under it: Core and Operator are now built from one source instead of two hand-copied
trees. Every fix had to be made twice before, and the copies drifted — three fixes
shipped on 25 August existed in Operator and were simply absent from Core, so a Core
user would have hit the identical dashboard failure with none of the fixes. The build
now refuses to ship a file with an unresolved tier marker, and a check confirms each
tier still contains everything it did before the move.

v1.10.6 — a reply that fits five different emails is not a reply.

Five wholesalers emailed off-market houses for sale — "115K! OFF-MARKET SFH IN Bethel",
"125K! BRICK SFH IN GERMANTOWN" — and every one of them got the identical draft back:
"Thanks for reaching out. We'd be happy to discuss funding for your deal. Send over your
purchase contract, expected closing date, deal structure." They were selling a house.
Nobody had asked for money.

Two rules now stand in front of every draft.

WHICH DIRECTION DOES THE EMAIL RUN? Someone OFFERING a property gets a reply about that
property — the address, the price, the question that decides it, or a plain decline.
Someone ASKING for something gets an answer to what they asked. Backwards is not a small
error; it proves nobody read the email, to the exact person you wanted to do business
with.

A DRAFT MUST CONTAIN SOMETHING THAT COULD ONLY HAVE COME FROM THAT EMAIL — the address,
the number, their name, the question. If it doesn't, it isn't a reply, it's a form
letter, and the job flags the email for you to write instead. A missing draft costs a few
minutes; a wrong one goes out under your name.

And drafts never pitch a lending or transactional-funding desk, name one, or attach a
funding intake list. This writes for your investing business and nothing else — a
separate capital business is separate mail, and blending them aims a sales pitch at
someone who was trying to sell you a house.

v1.10.5 — the thing that catches overlooked replies could not see the overlooked replies.

The reply-gap scan only looked at the 20 threads the morning run happened to work. On
2026-08-26 it reported "no replies needed" while seven finished drafts sat unsent — the
oldest 14 days — because none of them fell in that morning's slice. The one feature whose
entire job is stopping things from being overlooked was structurally blind to everything
actually being overlooked.

It now runs across the whole mailbox, every morning, and it covers the half it never
covered before: YOUR OWN UNSENT DRAFTS.

That half matters more than it sounds. A draft sits in the Drafts folder, which is not a
place anyone looks, and Gmail gives no badge for it. Having replies waiting to send is a
new habit for most people — you write and send in one motion, or you always did. A draft
that is not surfaced is a draft that never goes out, and the system is what created it.

So every unsent draft now gets its thread labelled Zoxiro/Awaiting Your Reply. That
label is the mechanism: a standing, browsable list in your own Gmail sidebar you can open
any time, rather than a line in a digest you read once. The digest is the daily nudge; the
label is where they live.

Two failure shapes are now called out by name, because both look perfectly fine sitting in
the Drafts folder: a draft with no recipient, which cannot send at all, and a mistyped
recipient domain — .ocm, .con, a missing TLD — which will bounce silently.

Nothing is ever sent, edited or deleted. Surfacing is the whole job; sending stays yours.

v1.10.4 — your dashboard was never being rebuilt, and the version stamp was hiding it.

The profile carried one version number, `plugin_version`, and it was written once the
scheduled tasks had been rewritten. Nothing required the DASHBOARD rebuild to have
succeeded first. So a refresh could update the tasks, skip or fail the dashboard, stamp
the profile as current, and from then on every session looked at the matching stamp and
concluded nothing was stale.

Found live: a profile reading 1.10.3 with all five task prompts correctly current, while
the dashboard was still the artifact generated on 1.9.1. Five builds of fixes to that page
had never reached it. The owner reinstalled and refreshed three times and nothing changed,
because nothing was ever going to.

The dashboard now carries its own stamp, `dashboard_build`, written only after the
artifact call actually returns. The rebuild triggers off that field rather than
`plugin_version` — the two drift apart precisely when this breaks. A session that cannot
create artifacts at all now says so and leaves the stamp alone instead of silently
recording a refresh it did not do. The weekly audit checks the gap.

This is the same failure as the 1.9.5 task-prompt bug, one artifact over: a stamp written
ahead of the work it claims to represent, which then suppresses the fix forever.

Also: when a dashboard shows nothing, the first question is now the footer version. Behind
the installed build means it never rebuilt. Matching it means it rebuilt and cannot reach
its tools. Different problems, different fixes, and they had been confused for each other.

v1.10.3 — the dashboard could never ask for permission, so it could never work.

The Morning Dashboard reads your mail, calendar and Drive through tool calls, and an
artifact has to DECLARE which tools it needs when it is created. If that list is empty,
the app is never asked to grant access — so no Allow prompt is ever offered, there is
nothing to click, and every card fails while the connectors themselves are perfectly
healthy. It looks exactly like a permission the owner forgot to give, and reloading
never fixes it.

The requirement was written in the orchestrator's generation contract, but not in the
dashboard template's own header — which is what a generator actually reads while
substituting values. So a build could follow the header to the letter and still ship a
page that can never be granted anything. The header now names the requirement and lists
the exact tools, in the loudest terms in the file.

SNAPSHOT MODE. A dashboard can now be built with its numbers baked in. It makes no tool
calls at all, so it works with no permission whatsoever, and says plainly at the top that
it is a picture of a moment rather than a live page. That is what you hand someone whose
live dashboard is broken, or who wants yesterday's morning kept.

v1.10.2 — the morning job was reading your whole inbox before doing any work.

The real cause of the stalled mornings, and it was not batch size. Step 1 of the
then-merged Admin/Deal Review job said to review every email currently sitting in the inbox and
classify each one. On a 452-thread inbox that single instruction consumed the entire
unattended window: the run read mail until it ran out of room and ended before drafting,
filing or archiving anything. It fired on time, produced nothing, and left the inbox one
day bigger — which made the next morning worse. Self-worsening, which is why it looked
like it was getting worse rather than better after each fix.

Cutting the batch from 50 to 20 never touched it. That number governs how many threads
get filed and queued, not how many get read. Step 1 now works only the threads the run
selected, and says so as the loudest line in the step. The whole inbox is still the
eligible set — the age ladder reaches it a batch per day. Coverage is the ladder's job
across days, not one morning's job.

The digest is now written FIRST, not last. It used to be step 13, so a run that ended
early left nothing at all: no partial work, no error, no record of where it stopped —
indistinguishable from a run that never fired. That is why these failures went
unexplained for weeks. The doc is now created before any work starts and appended after
each step, so an interrupted run leaves a report that names where it stopped.

Also fixed: the Second Look ledger step shipped in 1.10.0 landed in the wrong job. It was
written into the then-merged Admin/Deal Review job, which does not produce PASS verdicts, instead of the
Underwriter, which does — adding work to the one job that could not finish. Moved.

v1.10.1 — a dashboard that failed to load looked exactly like an empty inbox.

Rae's dashboard showed a dash in every count chip. Her mailbox had 26 real reply
drafts waiting at that moment. Nothing was zero; nothing had loaded.

The cause was in the error paths. Each card set its count chip only on SUCCESS, so a
failed fetch left the chip at its "-" placeholder and the card printed a small grey
error further down the page. At a glance that is indistinguishable from four honest
zeros — the same failure shape as every serious defect in this product's history: a
thing that reports nothing while producing no effect.

Three fixes. A failed probe now writes "!" into its chip instead of leaving the
placeholder, so a broken count can never be read as an empty one. Each card's error
names what it is NOT — "this is not 'no replies waiting'". And when all four probes
fail at once, the page shows a single banner saying the page has not been given
access, with the Allow-then-Reload path, instead of four separate messages blaming
four connectors. Four simultaneous connector outages essentially never happen; a
cleared permission grant happens every time the artifact is updated. That rule was
already written into the orchestrator skill and had never been implemented in the
template.

Also in the drafts card: the body check read plaintextBody only, and 6 of 50 live
drafts arrive with htmlBody instead, so the check was blind on those — it now falls
back. And drafts with no recipient filled in were dropped silently; they are now
counted with a line saying they cannot send, which is worth knowing about a draft
you thought was waiting to go.

preflight.py gained the same honesty it enforces: the sandbox's TLS interception
rejects the Google Fonts certificate, and that console message carries an error code
with no URL, so the existing host-name filter never matched it and a correct page
failed. Confirmed against an untouched shipped build before changing anything.
Network-class errors are now reported as a warning naming the cause, not a failure.


v1.10.0 — two compounding loops: the PASS pile, and your own closed jobs.

Second Look. Most fix-and-flip deals are passed for one reason: the ask is above the MAO.
That is not permanent — prices get cut. Every PASS is now written to pass-ledger.csv with
the price it would have to reach, which costs nothing because the MAO was already computed
to reach the verdict. A weekly job reads current prices and does one subtraction against
the stored number; only deals that cross get a full re-underwrite, capped at three per run
so the job finishes unattended.

Eligibility is the part that keeps it small. A price cut can only fix a deal killed by
price. Flood risk, pre-1950, auction and a failed DSCR refi test are price-independent —
the refi loan sizes off ARV, not purchase, so paying less does not improve it. Those are
marked ineligible with the blocker named and never read again.

A stored MAO also goes stale three ways, all silent: age past 90 days, a margin change, or
a change to that city's rehab band. A crosser against a stale MAO is a candidate, never a
verdict, and the job says which trigger fired.

Calibration. Rung 1 of the rehab ladder is "the user's own bands — from jobs they actually
closed," and until now nothing produced them. Admin now captures actuals when a deal closes:
final rehab spend, above-grade sqft, scope as EXECUTED rather than as planned, sale, days
held. After three closed deals at one scope level in one city, that band is promoted from
derived to the median of what the work actually cost. Median, not mean — one foundation
surprise should not move a light band. The retail sanity ceiling applies to actuals too,
because a mislabeled scope poisons a band the same way a retail quote does.

Both loops are visible rather than hidden behind a phrase: a Second Look card on the Morning
Dashboard reads the ledger and shows what each deal still has to come down by, and the
auditor gained two checks — is the ledger accumulating, and are closed deals reaching the
bands. A band marked "user" with fewer than three samples behind it is flagged loudly, since
it now outranks everything else on the ladder.


v1.9.7 — a plugin scanner, and the three real things it found.
Added build/scanplugin.py: it opens the packed .plugin and checks the mistake classes that
have actually shipped — manifest valid, every skill's frontmatter name matching its folder,
descriptions under the 1024 cap, every referenced file present in the package, every
placeholder documented in the placeholder table, no hardcoded version string that can go
stale, no dead references to removed modules, key numbers stated consistently, and every
workbook still opening.

It found three: the templates file header still read "(build 1.3.8)" — a second version
number that only ever goes stale and lies, now removed since every prompt already carries
its own stamp; the README build line still said v1.9.4; and the dashboard template used
{{DRIVE_SEARCH_TOOL}} and {{DRIVE_READ_TOOL}} without documenting them in its own header,
so a generator following the header alone would leave the pipeline card blind.

It also cried wolf twice, on batch size and margin target, by treating any number near a
keyword as a declaration. Those checks now only count explicit defaults. A scanner that
raises false alarms trains you to ignore it, which is the exact failure it exists to catch.

v1.9.6 — push belongs on the alarms, not on the working jobs.
Push notification stays on the two health checks — the daily system check and the weekly
audit — and deliberately nowhere else. Those two exist to reach the owner when something is
wrong, and a phone alert is their whole delivery mechanism. The working jobs (Admin/Deal
Review, Underwriter, the cleanup sweep) write digests the owner reads when they choose;
pushing those every morning turns a useful alarm into noise, which is exactly how the one
notification that matters gets swiped away unread.

So a working job with NO notification channel is no longer treated as a defect. The auditor
will not report it, and will not suggest adding push to the daily jobs. It flags a missing
channel only on a health check or an away-mode task. The decision is recorded in the profile
as automations.notification_policy so it survives a rebuild and a future audit.

v1.9.4 — Away Mode now proves the notification arrives before the trip.
Two things were measured on a live account rather than assumed. Push DID deliver a
deliberately unremarkable "everything is fine, nothing needs you" message, which settles the
real worry — the platform's noteworthy filter does not suppress an all-clear, so the design
holds. Email did NOT arrive at all: not the inbox, not spam, not trash.

So Away Mode stops asking which channel the owner wants — offering email would let someone
choose the one that does not deliver and leave believing they were covered. Both channels are
enabled, push is treated as the one that works, and setup now ends by firing a one-shot test
and having the owner confirm it reached their phone. This matters more than it sounds:
notifications can only be set when a task is CREATED, so a wrong channel cannot be corrected
once they have gone. The auditor no longer accepts email alone as a notification channel.

v1.9.3 — changing your buy-box now actually changes what the jobs do.
Saying "change my margin to 25%" rewrote the profile and confirmed, and that felt complete.
It was not. Scheduled task prompts are text stored on the platform with the buy-box, markets,
margin and batch size baked into the wording at the moment the task was written — they are
not read from the profile at run time. So a settings change updated what the owner saw and
left tomorrow's 7am job running yesterday's numbers, with nothing anywhere disagreeing.

That is not hypothetical: a live run reported deals as failing "the 30% target" for an owner
whose standard is 20/15, which is about $16k of offer price on a $250k ARV, on a deal that was
otherwise worth an offer.

New section 3a makes any change to buy_box, markets, asset_types, batch_size,
internal_addresses, company or connected tools rewrite the affected task prompts in the same
turn — in place, same task, same history — and say which tasks were updated. The auditor now
independently compares every task prompt against the profile and treats a mismatch as a high
finding rather than a note, because the failure is silent and expensive.

v1.9.3 — a reply that fits five different emails is not a reply.

Wholesalers marketing off-market houses were each getting the identical "happy to discuss
funding for your deal, send your purchase contract" draft back. They were selling a
house; nobody had asked for money.

Drafts now work out the direction first — someone OFFERING a property gets a reply about
that property; someone ASKING gets an answer to what they asked. Every draft must contain
something that could only have come from that email, or it isn't drafted at all and gets
flagged for you instead. And drafts never pitch a lending or transactional-funding desk.

v1.9.2 — the scheduled jobs were too big to finish. Batch cut to 20.
Measured, not guessed. A one-write test task fired unattended and completed in 76 seconds with
nobody watching, proving scheduled runs finish fine on their own. The real jobs do not: the
daily inbox review fired at 07:01, processed 49 threads, and had still not written its digest
at 08:38 — it completed only when the owner opened the task. Every heavy job was doing this,
which is why timestamps looked healthy while results never appeared.

So batch_size drops from 50 to 20 and the recent-mail reserve from 15 to 8, and a RUN BUDGET
rule goes into every template: work in small committed chunks, persist as you go instead of
saving everything for the end, and if the run is getting long, stop taking new work, finish
what is in hand, write the output, and say what remains. Stopping early is a success. Parking
is not. The weekly audit — the heaviest job and the one caught mid-stall — now reports after
each check rather than composing one report at the end, and attempts no repairs until every
check it intends to run has been reported.

v1.9.1 — a generation contract, so a tester never opens a half-built dashboard.
Both persisted artifacts are built by substituting values into a template, and a missed
substitution does not error — it renders. Someone opens their brand-new dashboard and reads a
literal placeholder in the header, or a blank where their company name belongs, or a logo that
never drew. New section 9c makes the orchestrator resolve every value from the profile first,
with a named fallback for each, then verify the OUTPUT rather than its own intention: no
placeholder survives, the logo symbols and wordmark are both present, every id the script
touches exists, every onclick target is defined, no icon depends on an unloaded font, and
mcpTools lists every tool the page calls. The artifact id is recorded only after the save
returns.

The auditor now re-checks all of that on live artifacts, so a page that was generated badly
before this build gets caught and regenerated rather than sitting there looking broken.

Also fixed: a .logo rule left behind when the navy hero was removed was still clamping the new
header to a 26x26 box with overflow hidden, cropping the mark to a fragment.

v1.9.1 — the morning job was reading your whole inbox before doing any work.

Step 1 said to review every email currently sitting in the inbox and classify each one.
On a large inbox that single instruction consumes the entire unattended window: the run
reads mail until it runs out of room and ends before drafting, filing or archiving
anything — firing on time, producing nothing, and leaving the inbox one day bigger, which
makes the next morning worse. Step 1 now works only the threads the run selected. The
whole inbox is still the eligible set; the age ladder reaches it a batch per day.

The digest is also written FIRST now rather than last, so a run that ends early leaves a
report naming where it stopped instead of leaving nothing at all.

v1.9.0 — the separate home screen is gone. The Morning Dashboard is the front door.
The launcher was removed on the owner's verdict, and the reasoning is now written into §9a so
it does not get rebuilt. A saved page can call the user's connectors — that is real — but it
cannot invoke Claude. So a tile can fetch and display; it can never underwrite a deal or draft
a follow-up, because those need reasoning and reasoning only happens in a conversation. Every
version of that launcher therefore ended as a button whose job was to tell the user what to
type. Everything a tile could honestly have done, the dashboard already does without being
clicked.

One thing was worth keeping and it moves to the dashboard: Going away. That button now reads
the calendar, names the trip it found, and explains in full what the two vacation
notifications do before the owner commits — the daily business summary, the daily all-clear
that sends even on success, that nothing is ever sent on their behalf, and that saying "I'm
back" ends it early.

New standing rule: a control either renders its own answer in place, or it does not exist.

v1.8.0 — home screen restructured around an Ask box.
Tiles are smaller and sit three across. The paste bar is gone from the top; underneath the
tiles there is now an "Ask Zoxiro" box — a real editable field. Clicking a tile drops its
wording into that box, copies it, and scrolls to it. The user pastes into the chat and
presses Enter, or edits the wording first, or ignores the tiles and types their own.

The box teaches, which the bare phrases never did: it carries a worked example as
placeholder text and four sample-prompt chips. Both are now profile-driven placeholders —
{{EXAMPLES_JSON}} and {{EXAMPLE_PLACEHOLDER}} — and must be written from the owner's own
markets, asset types and buy-box. Generic filler teaches nothing; showing someone a prompt
about a street in a city they buy in does.

System-check results render inside that same box rather than spawning a second panel.

v1.7.2 — reverting 1.7.1: nothing links out of the app.
1.7.1 made tiles open claude.ai with the prompt pre-filled. Tested with the owner and it
failed badly: it left the desktop app for a browser session, required a sign-in and three
bot challenges, then showed a red "use caution before running this prompt" banner, because
a prompt arriving by URL is treated as untrusted external content. Being thrown out of the
app to do something the app does is the wrong shape regardless of the friction.

Tiles copy their phrase again — one paste, one Enter, no navigation — and say so in a bar
at the top of the page. "Check my system" still runs live inside the widget, which is the
right pattern and the one to extend: window.cowork.callMcpTool reaches every connected
tool, so more of this can be done in place rather than handed off at all.

v1.7.1 — tiles and buttons actually launch now.
Every tile is a link to https://claude.ai/new?q=<prompt>&surface=cowork&composer=mini, which
opens a composer with the message already typed. The user presses Enter. This replaces
handing over a phrase to copy, which was the honest fallback while I believed no launch
path existed — it does exist, and it is the documented one.

Two exceptions stay in place deliberately: "Check my system" runs live inside the page,
because opening a chat to do something the widget can do itself is slower and worse. And
the result panel moved ABOVE the tiles, so clicking one never scrolls you to the bottom of
the page to find the answer.

Standing rule updated: a control either does its work in place, or it launches a composer
with the prompt filled in. Handing the user text to retype is no longer an acceptable
outcome, and rendering a control that does neither never was.

v1.7.0 — every surface rebuilt on the launch-site design system.
The blue squares on the home screen were empty icons: the tiles called Tabler icon classes
and the icon font was never loaded, so each one rendered as a coloured box with nothing in
it. Icons are now inline SVG and cannot silently fail that way again.

All three surfaces — home, dashboard, pipeline board — now share the site's construction:
sticky header with the real four-node mark and the product wordmark, the site hero
gradient with Archivo headings and the blue eyebrow, and module cards built like the
website's rather than chunky tiles. Two large base64 PNG logos are gone in favour of one
inline SVG symbol: the dashboard dropped 110KB to 31KB and the pipeline board 102KB to
16KB, which also removes the reason scheduled jobs were told never to open the board file.

The pipeline board template is no longer CRM-only — it reads the underwrite queue from
Drive when no CRM is connected or the CRM is empty, which is the normal case.

v1.6.0 — one brand across every surface, and the home screen finally lives in the sidebar.
Every rendered surface now uses the design tokens lifted verbatim from the live site:
Lato body, Archivo headings, blue #2b7ffd, navy #0b1424, the ink and line ramps, 12/8px
radii. The dashboard's old indigo and the pipeline board's slate are gone, and the stage
colours are a blue ramp rather than seven unrelated hues. The beta leaderboard already
matched and was left alone.

The home screen is now a PERSISTED artifact recorded as `home_artifact`, so it sits in the
sidebar permanently instead of existing only inside whichever chat rendered it — which is
why it kept disappearing. And its buttons do real work: "Check my system" probes every
connector live through window.cowork.callMcpTool and reports what actually answered;
"Going away" reads the calendar, finds the next multi-day block, and hands back the phrase
with the dates already filled in. Where a widget genuinely cannot finish the job — creating
scheduled tasks — it says so plainly instead of pretending.

v1.5.0 — the home screen never worked. Fixed.
Every tile called `sendPrompt()`, which was never defined anywhere and has no host API
behind it, so every click threw and did nothing. The launcher has looked interactive and
been inert since it shipped. Tiles now call launch(), which hands over the exact phrase —
copied to the clipboard where the browser allows it, selected in a field where it does
not — and the dashboard buttons do the same. New rule in the orchestrator: never render a
control that only appears to launch something. A widget cannot put text into the chat, so
a button either does its work inside the widget or says plainly what to say.

v1.4.9 — the dashboard is the primary surface, not the home screen.
Correcting 1.4.8: once a user turns connectors on, the Morning Dashboard replaces the
clickable launcher and they may never see the home screen again — so tiles added there
are invisible to every connected user, which is every real user. "Going away" and "Check
my system" are now buttons on the dashboard itself, with the home-screen tiles kept only
for the zero-connector case. The rule in 9c now says dashboard first. Also: the dashboard
footer and template header no longer hardcode a version string that goes stale, and the
tier is spelled Commander everywhere.

v1.4.8 — discoverability: a capability behind a magic phrase does not exist.
A buyer who just installed this has read nothing and will never guess "I'm going on
vacation" or "audit my system". Away Mode and the system check now have home-screen tiles
("Going away", "Check my system"), the "What can you do?" answer must enumerate every
installed module and named mode rather than being answered from memory, and Away Mode is
offered once — never nagged — when the connected calendar shows a multi-day block coming
up. New standing rule: adding a capability without a visible surface is an incomplete
change.

v1.4.7 — Away Mode: two vacation notifications, opt-in.
The owner says "I'm going on vacation" and gets two things until the date they name. A
DAILY SUMMARY (business): anything time-sensitive first, new seller mail worth naming,
deal-flow movement, and what is waiting on them — nothing is ever sent on their behalf.
And an ALL-CLEAR (system): the same health measurements the daily check runs, except it
SENDS ON SUCCESS. That inverts the silent-on-success rule deliberately — at home silence
is safe because you'd notice a dead system within a day; on vacation you would not, and
silence is indistinguishable from the checker having died. Both tasks require a push or
email channel, both self-disable on the return date, and a real failure always breaks
cadence and reports immediately. The auditor treats a channel-less away task as a
finding: an owner who thinks they are covered and is not is worse off than one who knows
they are not.

v1.4.6 — rehab cost is keyed by CITY, not county, and it is an investor number.
Three changes, one root cause. (1) Bands now key by "City, ST" — county lumped markets
that are not one labor market, ZIP has no data behind it. ARV and comps key by ZIP
instead; cost and value no longer share geography. (2) THE SOURCE RULE: a web search for
renovation cost returns homeowner RETAIL pricing, three to five times investor rehab cost
— a Dayton remodeler's own 2026 page quotes $60-90/sqft for a light update against an
investor light band nearer $18. Retail sources are named and rejected, and a sanity
ceiling (light over $30, heavy over $90) catches anything that slips through. Importing
retail pricing inflates rehab, collapses the MAO and kills good deals silently. (3) Source
labels are now honest and closed: user | city-researched | derived | national-fallback,
with `derived_from` required on anything derived. A band researched for one city can no
longer be filed as researched for another. The auditor checks all of this on every run.

v1.3.0 — ONE underwriting standard: the financed MAO is solved out of the full cost
stack against a margin expressed as a percent of ALL-IN cost (20% target / 15% floor,
both profile fields). The flat-dollar profit floor and the competing 70%-rule MAO row
are gone — two numbers that disagree is how a user offers the wrong price. Rehab is now
estimated per market ($/sqft bands researched once per market and cached, user
corrections always win), ARV names its web-comp sources, and Admin gains
an on-demand Offer Sheet and gated LOI drafting.

v1.2.6 — hardened from live beta: connector-native scheduled jobs with file-first
folder resolution, full-Drive-access + tool-permission requirements documented,
instruction-integrity protection on every automation, internal-address awareness,
full-mode inbox automation from day one (file and archive, never
delete; review mode opt-in only), the scheduled-task change protocol, ARV from web
comps with every comp cited, auto-generated Morning Dashboard at onboarding, and
Bookkeeping shown as the
coming-soon tier upsell (STR removed from the home screen).
