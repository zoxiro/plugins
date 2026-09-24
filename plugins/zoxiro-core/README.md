# Zoxiro

**The Operating Platform for Real Estate Investors.**

Zoxiro is an all-in-one back office that runs inside Claude. It onboards each user
once, learns their company, markets, and buy-box, then handles deal analysis, pipeline,
and administrative work through a single intelligent system. Each user connects their
own tools (CRM, email, calendar, file storage) — their data stays in their own accounts.


**This build: Zoxiro Core (v1.13.27)** — modules included: orchestrator, admin, pipeline, sourcing.

v1.13.27 — no more support drafts or Drive shares.

Core is not yet connected to the Zoxiro service, so a chat-side issue report is written
as one document into "Zoxiro/Feedback Reports" in your own Drive and you are told where
it is — nothing is shared and no Gmail draft is created. When a `backend.key` is present
in the profile the report goes to the service instead (`/issue`), the same way Operator
1.14.47 does it. Product-change requests that a chat is asked to make follow the same
rule.

v1.13.26 — the away-mode switch is now a switch.

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


v1.13.25 — the work summary runs nightly now, and the cron trap it walked into.

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
creation — Sonnet 5 for all of them, with no exception in this tier, which has no
Underwriter — one model, every task.
The rule now says in so many words that a template added later inherits the default and
that not knowing which model a job takes is never a reason to skip setting one. A task left
unset runs on whatever the account default happens to be and changes underneath you when
that default moves.


v1.13.24 — a written record of your own work, twice a week.

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


v1.13.23 — Deal Review now writes where this tier actually reads, and testers can reach us.

The Deal Review bird dog was still ending its run by appending every candidate to
underwrite-queue.csv — the file this tier is explicitly forbidden to write, because the
underwriting module that reads it ships in Operator, not here. A previous release fixed the
Deal Pipeline card's version of that mistake and missed the scheduled prompt, which is the
copy that runs unattended every morning. So the job worked, reported success, and filed the
owner's deals into a file nothing in their build would ever open, while the Deal Pipeline
Tracker they were told to watch stayed empty. Deal Review now logs through the pipeline
module — the connected CRM if there is one, otherwise the Tracker — mapped onto the
Tracker's own columns, with the asset type and any "pro forma, unverified" or "type
uncertain" flag carried in Notes / Watch Trigger.

Five smaller references followed the same file and were corrected with it: the Drive
folder-resolution fallback stem, the parent-folder file inventory, the deal-events address
join, the cleanup sweep's worked example, and the digest wording that reported deals as
"queued for the Underwriter" — a job this tier does not install. The first-run comp-domain
approval warning named the Underwriter too; in this tier the job that leaves the connectors
for the open web is Deal Review, and that is now what it says.

Also new: the scheduled prompts carry the same escalation and build-hygiene clauses Operator
has had — stale-build detection, prompt provenance with a support address, the legacy-rename
guard, and the canonical-filename clause, rewritten for this tier's file set. Until now a
Core tester hitting a problem in an unattended run had nowhere for the report to go.


v1.13.10 — a walkout basement is not square footage, and not an ordinary basement either.

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


v1.13.9 — comps now have a square-footage band.

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


v1.13.8 — comps are now matched on what a buyer actually pays for.

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


v1.13.7 — the Underwriter no longer waits for a click nobody will make.

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


v1.13.6 — the weekly cleanup job now does the tidying instead of asking you to.

Every connector write leaves a duplicate, because Drive cannot overwrite a file in place —
so the working folder gained roughly one extra copy of zoxiro-profile.yaml and anything else
a run wrote per day. Until this build the weekly job could only remind the owner to clear
them by hand, on the reasoning that a scheduled run cannot reach a local disk. That
reasoning was half right: it cannot, and it does not need to. Drive for Desktop mirrors the
folder, so a file moved in Drive moves on the owner's computer too. One move, both places.

The job now finds or creates a "_to_delete" folder inside the working folder and parks
superseded copies there. It parks; it never deletes. Deleting stays with the owner, because
an unattended run cannot see what it is about to destroy and cannot undo it.

Four brakes make that safe to run unwatched, and the build now fails if any of them goes
missing. It never moves the newest copy of a file — that is the live one every other job
resolves. Nothing is eligible until it has been superseded for seven full days, so a bad
write can never carry off the good copy in the same week. Files are moved and never renamed,
because renaming resets a file's modified time and Drive search is folder-blind — a renamed
file in the holding folder becomes the newest match for its own name and the next job picks
the discarded copy. And before touching a name at all, the sweep compares the newest copy's
size against the largest older one; if the newest has collapsed to under half, it moves
nothing for that name and says so at the top of its report. That last check is the one case
where "newest wins" is wrong, and it gives the system a standing weekly detector for a
silent truncation that otherwise surfaces weeks later, if at all.

The task is renamed from "Weekly file-cleanup reminder" to "Weekly cleanup sweep", since it
no longer only reminds. Downloads and anything else on the machine still need a live
session; the job says so in its closing line.


v1.11.3 — mail was being archived but never filed.

On top of 1.11.2: filing and archiving are now two separate decisions, because writing them as one cost
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

v1.11.2 — the update stops asking permission to do its job.

On top of 1.11.1: the update refresh no longer asks permission — it just happens.

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

v1.11.1 — updates offer what's new instead of silently skipping it.

On top of 1.11.0: upgrades now offer new features instead of silently skipping them.

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

v1.11.0 — setup ends with proof, and the invisible work becomes visible.

On top of 1.10.3: five additions, all aimed at the same thing: the user FEELING the system work.

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

v1.10.3 — two job prompts were shipping unstamped.

On top of 1.10.2, a full diagnostic pass found two real defects, both now fixed.

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

v1.10.2 — the deal scan was being throttled by the inbox clean-up.

On top of 1.10.1, the deal scan and the inbox clean-up are no longer competing for the same budget, and
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

v1.10.1 — the inbox was not broken, it was outnumbered.

On top of everything in 1.10.0, the daily run now measures how much mail is ARRIVING and compares it to how much it
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

Core also gains the age ladder itself, which it never had. Core's inbox job told the run to
work "the batch chosen by the passes above" and there were no passes above — the ladder,
the two-pass split, the by-design-stayers rule and the run-budget ceiling were all present
in Operator and simply absent here, so a Core run had no selection strategy at all. All of
it is now shared, which is exactly the class of gap moving to one source was meant to
surface.

v1.10.0 — your automations now tell you when they are out of date, and Core gets the
upgrade check it never had.

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

Core also had no version check at all. Operator has re-run its dashboard and rewritten
its task prompts on every upgrade for several builds; Core simply didn't, so a Core user
who upgraded kept their old prompts and their old dashboard indefinitely. Core now runs
the same refresh.

v1.9.3 — a reply that fits five different emails is not a reply.

A wholesaler emailed offering a house for sale and the drafted reply talked about
funding — five identical copies of it, across five different properties. The direction
of an email now has to be worked out before a word is written: someone offering you
something gets a reply about that thing; someone asking you for something gets an answer
to what they asked. Replies never introduce or speak for a lending or transactional
funding desk. A draft that would fit five different emails is discarded, not saved.

v1.9.2 — who is waiting on you, including your own unsent drafts.

The reply-gap scan only looked at the threads that morning's run happened to work, so it
could report "no replies needed" while finished drafts sat unsent for weeks. It now runs
across the whole mailbox every morning, and it covers unsent drafts as well as unanswered
mail.

Every unsent draft gets its thread labelled Zoxiro/Awaiting Your Reply — a standing list
in your own Gmail sidebar, because a draft in the Drafts folder is invisible and Gmail
gives no badge for it. Drafts with no recipient, or with a mistyped domain that will
bounce, are named explicitly. Nothing is ever sent or edited.

v1.9.1 — a dashboard that failed to load looked exactly like an empty inbox.

Every count chip set itself only on SUCCESS, so a failed fetch left the chip showing
its "-" placeholder while the card printed a small grey error further down. At a
glance that is indistinguishable from honest zeros — which is how a mailbox holding
26 real reply drafts read as "no replies to send".

A failed probe now writes "!" into its chip. Each card's error names what it is NOT
("this is not 'no replies waiting'"). And when all four probes fail together, one
banner says the page has not been given access, with the Allow-then-Reload path,
rather than four messages blaming four connectors — four simultaneous outages
essentially never happen, while a cleared permission grant happens every time the
artifact is updated.

Also: the drafts body check read plaintextBody only and was blind to drafts that
carry htmlBody instead; it now falls back. Drafts with no recipient are counted and
named instead of silently dropped.


v1.9.0 — two compounding loops: the deals you passed on, and the jobs you finished.

Second Look. Most deals get passed for one reason: the ask is above the MAO. That is not
permanent — prices get cut. Every pass is now written to pass-ledger.csv with the price it
would have to reach, which costs nothing because the deal-flow scan already computed the
MAO to reach the verdict. A weekly pass reads current asks and does one subtraction against
the stored number; only deals that cross get a fresh comp screen, capped at three per run
so the job finishes unattended.

Eligibility is what keeps it small. A price cut can only fix a deal killed by price. Flood
risk, pre-1950, auction, and any condition or location fact are marked ineligible with the
blocker named, and never read again.

A stored MAO also goes stale three ways, all silent: age past 90 days, a change to the MAO
percent or assignment fee, or a change to that city's rehab band. A crosser against a stale
MAO is a candidate, never a verdict, and the pass says which trigger fired.

The ledger is deliberately the same shape Zoxiro Operator uses, so a user who upgrades
keeps their pile instead of starting over.

Calibration. Rung 1 of the rehab ladder is "the user's own bands — from jobs they actually
closed," and until now nothing produced them. Admin now captures actuals at closing: final
rehab spend, above-grade sqft, scope as EXECUTED rather than as planned, sale, days held.
After three closed deals at one scope level in one city, that band is promoted from derived
to the median of what the work actually cost. Median, not mean — one foundation surprise
should not move a light band. The retail sanity ceiling applies to actuals too, because a
mislabeled scope poisons a band the same way a retail quote does.

Also fixed: the Deal Pipeline card told Core users with no CRM to wait for deals to appear
in underwrite-queue.csv — a file this tier is explicitly forbidden to write, so the message
could never come true. It now names the tracker where Core deals actually go. And the
profile-change table listed tasks that only exist in Operator.


v1.8.4 — Away Mode now proves the notification arrives before the trip.
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

v1.8.3 — changing your buy-box now actually changes what the jobs do.
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

v1.8.2 — the scheduled jobs were too big to finish. Batch cut to 20.
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

v1.8.1 — a generation contract, so a tester never opens a half-built dashboard.
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

v1.8.0 — the separate home screen is gone. The Morning Dashboard is the front door.
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

v1.7.0 — home screen restructured around an Ask box.
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

v1.6.2 — reverting 1.6.1: nothing links out of the app.
1.6.1 made tiles open claude.ai with the prompt pre-filled. Tested with the owner and it
failed badly: it left the desktop app for a browser session, required a sign-in and three
bot challenges, then showed a red "use caution before running this prompt" banner, because
a prompt arriving by URL is treated as untrusted external content. Being thrown out of the
app to do something the app does is the wrong shape regardless of the friction.

Tiles copy their phrase again — one paste, one Enter, no navigation — and say so in a bar
at the top of the page. "Check my system" still runs live inside the widget, which is the
right pattern and the one to extend: window.cowork.callMcpTool reaches every connected
tool, so more of this can be done in place rather than handed off at all.

v1.6.1 — tiles and buttons actually launch now.
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

v1.6.0 — every surface rebuilt on the launch-site design system.
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

v1.5.0 — one brand across every surface, and the home screen finally lives in the sidebar.
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

v1.4.0 — the home screen never worked. Fixed.
Every tile called `sendPrompt()`, which was never defined anywhere and has no host API
behind it, so every click threw and did nothing. The launcher has looked interactive and
been inert since it shipped. Tiles now call launch(), which hands over the exact phrase —
copied to the clipboard where the browser allows it, selected in a field where it does
not — and the dashboard buttons do the same. New rule in the orchestrator: never render a
control that only appears to launch something. A widget cannot put text into the chat, so
a button either does its work inside the widget or says plainly what to say.

v1.3.6 — the dashboard is the primary surface, not the home screen.
Correcting 1.3.5: once a user turns connectors on, the Morning Dashboard replaces the
clickable launcher and they may never see the home screen again — so tiles added there
are invisible to every connected user, which is every real user. "Going away" and "Check
my system" are now buttons on the dashboard itself, with the home-screen tiles kept only
for the zero-connector case. The rule in 9c now says dashboard first. Also: the dashboard
footer and template header no longer hardcode a version string that goes stale, and the
tier is spelled Commander everywhere.

v1.3.5 — discoverability: a capability behind a magic phrase does not exist.
A buyer who just installed this has read nothing and will never guess "I'm going on
vacation" or "audit my system". Away Mode and the system check now have home-screen tiles
("Going away", "Check my system"), the "What can you do?" answer must enumerate every
installed module and named mode rather than being answered from memory, and Away Mode is
offered once — never nagged — when the connected calendar shows a multi-day block coming
up. New standing rule: adding a capability without a visible surface is an incomplete
change.

v1.3.4 — Away Mode: two vacation notifications, opt-in.
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

v1.3.3 — rehab cost is keyed by CITY, not county, and it is an investor number.
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

v1.2.7 — from live testing: the assignment fee is stored as a flat dollar NUMBER
(default $10,000, never prose) because the MAO formula subtracts it; the MAO-percentage
question now explains how it differs from the return target and rejects an answer
outside 50-90%; the price-range question carries a worked example; and the weekly
cleanup sweep parks the stale same-name Drive copies that connector writes leave
behind.

**This build: Zoxiro Core (v1.13.27)** — the wholesaler tier. Modules included:
orchestrator, sourcing, admin, pipeline. Complete for finding, screening, pricing
(ARV + MAO) and tracking a deal. Institutional underwriting — NOI, DSCR, cap rate, refi
tests, asset-class engines and the Excel models — is in Zoxiro Operator; Bookkeeping is
in Zoxiro Commander.


v1.2.6 — hardened from live beta: connector-native scheduled jobs with file-first
folder resolution, full-Drive-access + tool-permission requirements documented,
instruction-integrity protection on every automation, internal-address awareness,
full-mode inbox automation from day one (file and archive, never
delete; review mode opt-in only), the scheduled-task change protocol, ARV from web
comps with every comp cited, auto-generated Morning Dashboard at onboarding, and
Bookkeeping shown as the
coming-soon tier upsell (STR removed from the home screen).
