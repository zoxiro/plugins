# Known failures — the incident register the auditor is measured against

Every row here is something that actually happened to a live Zoxiro user, went
undetected, and was found by the owner rather than by the system. Each names the check
that catches it now.

This file is not documentation. `scan.py` reads the block at the bottom and fails the
build if any `probe` string is missing from `zoxiro-auditor/SKILL.md`. That is what
stops an auditor check being edited away by accident — which matters more here than
anywhere else in the product, because the thing that silently loses a check is the thing
whose entire job is noticing what has been silently lost.

**Rule for adding to this file: a bug that reached the user gets a row before it gets a
fix.** If a failure can happen twice, the auditor is where it stops the second time.

| # | What happened | Why nothing noticed | Check that catches it |
|---|---|---|---|
| 1 | Dashboard sat on build 1.9.1 while five builds of fixes shipped. Owner reinstalled three times against a page that was never being rebuilt. | `plugin_version` was stamped on upgrade and read as current, so every session skipped the refresh. The dashboard had no stamp of its own. | §6 — `dashboard_build` compared to the installed version, separately from `plugin_version` |
| 2 | Profile read 1.9.2 while all five task prompts were still stamped 1.4.x. | The refresh wrote the stamp and never wrote the prompts. Stamp matched, so every later session said nothing was stale. | §2 — task stamps compared to the profile, not only to the plugin |
| 3 | A mailbox holding 26 finished, unsent drafts reported "no replies to send" for weeks. | The reply-gap scan ran inside the daily batch, so it only ever saw the ~20 threads that run touched. | §4 — unsent draft count compared against `Zoxiro/Awaiting Your Reply` |
| 4 | The morning job parked mid-run for weeks and looked like it had never fired. | Step 1 read, classified and summarised all 452 inbox threads before doing any work, exhausting the unattended budget. Batch size was never the problem. | §2 — each inbox job's work step must be explicitly bounded to that run's selection |
| 5 | Same incident, misdiagnosed repeatedly as "the job didn't run". | A parked run and a run that never fired are indistinguishable unless you look at whether the digest CLOSES. | §3 — a digest that opens and does not close is a parked run |
| 6 | Every dashboard card failed with a permission error while the connectors were healthy. | `mcpTools` was empty, so the app was never asked to grant access and the user was never offered a prompt to click. | §6 — `mcpTools` must list every tool the page calls |
| 7 | Digests reported mail archived while the inbox count never moved. | Filing and archiving are two separate calls; a labelled thread is still in the inbox. | §3 — claimed effect checked against the real mailbox; §4 — threads labelled but still in the inbox |
| 12 | Three Anthropic billing emails sat unlabelled in the inbox, and no `Anthropic` label had ever been created despite a year of receipts from them in the mailbox. | Filing was written as the tail of the archiving sentence, so any thread that stays in the inbox by design — security alerts, failed payments, anything with a draft — skipped labelling along with archiving. | §4 — threads with no user label, especially ones deliberately left in the inbox; service senders with no label of their own |
| 11 | Roughly two thirds of the owner's own deal flow was never looked at — not rejected, never read. | The bird dog and the admin pass shared one batch, so the deal scan could only see the threads the admin pass happened to reach. ~29 listing alerts a day against 8 recent slots. | §4 — the Deal Alerts feed is counted for unreviewed threads; a growing feed means a tidy inbox hiding unread deal flow |
| 10 | An inbox that never got cleaner for weeks, while every daily run reported a clean success and was telling the truth. | ~29 threads/day arriving against a batch of 20, of which only 8 could touch recent mail. The overflow aged past 7 days into the backlog the next pass was draining, so the backlog was refilled from above. Nothing measured inflow, so nothing said so. | §4 — arrivals per day compared against both batch size and recent reserve |
| 9 | Five identical drafts offering funding were sitting in the mailbox, in reply to a wholesaler selling a house. | A separate lending/funding skill installed on the same account won the routing and wrote them. Skill selection happens before any SKILL.md is read, so no Zoxiro rule could stop it. | §4 — drafts are read, not just counted; a draft pitching a different business is a HIGH finding |
| 8 | An away task delivered nothing while the owner was travelling. | Email notifications did not deliver on that account; only push did. The task looked healthy. | §2 — an away task with no PUSH channel is a finding, not a note |
| 13 | A forced refresh run on the desktop found none of the owner's five scheduled jobs and could not update any of them. | The jobs were created in a Cowork CLOUD session; schedulers do not share, so the desktop session's task list was a different set entirely. Nothing recorded which surface owned them, so the only obvious repair was to recreate them — which would have left two Admin passes archiving one mailbox. | §2 — `jobs_surface` recorded, and a refresh that cannot see its tasks reports where they live instead of recreating them |
| 14 | A dashboard sat on build 1.9.1 for two weeks while three sessions each reported rebuilding it. | NOTHING on any surface can write a Cowork sidebar artifact — a cloud session confirmed it 08-25, a desktop session enumerated all five `cowork` tools and confirmed it again 08-27. The reports were not sloppy; they were impossible. Each cited the template's placeholder COUNT as proof, a number identical across three builds. | §6 — the board is a PUBLISHED WEB artifact the user pins; rebuild proof is a code marker from the template file, never a version label |
| 15 | An owner opened their drafts to find 207 of them waiting, was certain the system had written them all reaching back two years, and stopped opening the drafts label at all. In fact 73 of the 76 remaining were the owner's OWN abandoned drafts from 2023-2025, written before Zoxiro existed. | Two separate gaps compounding. The admin job had no age ceiling on DRAFTING, so its oldest-first ladder was free to write replies to year-old mail; and nothing distinguished the system's drafts from the mailbox's own pre-existing ones, so a single undifferentiated count made a normal mailbox look like a runaway job. The pile's size was itself what stopped them using the feature the count existed to drive. | §4 — drafts older than 14 days counted and attributed SEPARATELY from the working queue, and the admin job's reply age ceiling verified as present
| 16 | The admin job fired on time, wrote nothing anywhere in Drive, and the daily check reported it as a job that had never run — sending the owner to a scheduler where the task was enabled, on time and blameless. | The check inferred "did it run" from Drive artifacts alone and never consulted the scheduler's own last-fired timestamp, so "never fired" and "fired and died before its first action" were the same answer. They have completely different fixes. | §4 — last-fired timestamp read FIRST, then Drive; four states reported separately (never fired / still running / fired and produced nothing / fired and parked)
| 17 | The daily check told the owner their admin job was broken. It was running at that moment — it had started an hour late and was still working, and its digest landed four minutes after the alarm. | Fixing #16 taught the check to read the last-fired timestamp, but it only knew three states, and a run in flight matches the worst of them exactly: fired today, nothing in Drive yet. It never asked whether the run had FINISHED. A false alarm is more expensive than a late one — this message is the only one that reaches an owner who never opens a chat, and it only works while they still believe it. | §4 — a run that has fired and not reported a finish is reported as STILL RUNNING and nothing else about it is checked

```yaml
# machine-checked by scan.py — each probe must appear verbatim in the auditor SKILL.md
probes:
  - id: 17-inflight-called-dead
    probe: "Has this run FINISHED, or is it still going?"
  - id: 16-fired-but-silent
    probe: "did it run"
  - id: 15-stale-draft-pile
    probe: "Are the old drafts in this mailbox the system's, or the owner's own?"
  - id: 14-unwritable-sidebar-board
    probe: "Is the dashboard a PUBLISHED page the user pinned?"
  - id: 13-wrong-surface-refresh
    probe: "Can this session actually SEE the tasks it is about to rewrite?"
  - id: 1-stale-dashboard
    probe: "The dashboard's own build stamp."
  - id: 2-half-completed-refresh
    probe: "check the stamp against the profile too"
  - id: 3-reply-gap-coverage
    probe: "Does the reply-gap scan actually see the whole mailbox?"
  - id: 4-unbounded-work-step
    probe: "Is each inbox job's WORK STEP still bounded?"
  - id: 5-parked-vs-never-fired
    probe: "Tell a PARKED run apart from a run that never fired."
  - id: 6-empty-mcptools
    probe: "mcpTools"
  - id: 7-filed-but-not-archived
    probe: "filing without archiving"
  - id: 8-away-no-push
    probe: "An away task with no PUSH channel"
  - id: 9-wrong-business-draft
    probe: "Do the drafts sitting in this mailbox answer the email they are attached to?"
  - id: 10-inflow-exceeds-batch
    probe: "Is the batch bigger than the inflow?"
  - id: 11-deal-feed-unread
    probe: "Is the Deal Alerts feed actually being read?"
  - id: 12-filed-never-labelled
    probe: "Is mail being FILED, or only archived?"
```
