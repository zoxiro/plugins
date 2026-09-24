# Shared Deal-Record Schema

The common shape every module uses when handing a deal to another module
(Sourcing → Pipeline, Admin intake → Pipeline/Underwriting, Underwriting verdict →
Pipeline). Fill what's known; never invent. When writing to the user's CRM, map these
fields onto the CRM's native properties (create custom properties only with the user's
OK).

```yaml
deal:
  property_address:        # or "unlisted — [market]" if not yet known
  market:
  asset_type:              # SFR | multifamily | STR | commercial | land | MHP | RV | storage
                           # STR may be recorded here, but STR analysis is not in this build
  units_or_size:
  source:                  # propstream-list | web-fsbo | referral | inbound | other
  stage:                   # New → Contacted → Analyzing → Offer Out → Under Contract → Closed | Dead
  ask_price:
  est_value:
  est_equity:
  distress_flags: []       # pre-foreclosure, tax-delinquent, probate, vacant, code, absentee
  score:                   # from scoring model
  score_reasons:           # one line: fit / equity-structure / motivation (or engagement/urgency)
  strategy_fit:            # flip | hold | creative (Sub-To / seller carry / wrap / MLO) | wholesale
seller_contact:
  name:
  phone:                   # only from legitimate skip-trace sources — never fabricated
  email:
  mailing_address:
  dnc_status:              # unknown | scrubbed-clear | do-not-call
activity:
  last_touch:              # date + channel
  next_action:             # what + due date — REQUIRED on every handoff
  notes:
underwriting:              # filled by Underwriting after analysis
  verdict:                 # BUY | PASS | ONLY WORKS WITH STRUCTURE | Conditional
  price_range:
  key_risks:
  dd_items: []
second_look:               # what a PASS must carry to be re-screened cheaply.
                           # TOP LEVEL on purpose — Core fills it from the 70%-rule
                           # screen and Operator from the financed MAO, so a user who
                           # upgrades keeps one ledger instead of starting over.
  mao:                     # the price at which THIS deal becomes a BUY.
                           #   Operator: the financed MAO at the target margin.
                           #   Core: the 70%-rule MAO (ARV × mao_percent − rehab − fee).
                           #   LAND: solved at land_margin_target on all-in INCLUDING
                           #   CARRY over the hold the deal actually requires, and
                           #   computed locally — the Zoxiro service prices flips, not
                           #   parcels, so a land row carries no service source line.
                           #   WHOLESALE LAND: not a margin MAO at all — the ceiling is
                           #   land_max_pct_of_market x comp value, and the deal is
                           #   screened on the spread against land_min_spread. Record
                           #   that ceiling here and say so in margin_target_used.
  mao_at_floor:            # the stretch price — BUY at the floor margin.
                           #   Core has no floor margin: leave empty.
  price_when_screened:     # the ask on the day it was screened
  gap_to_mao:              # price_when_screened − mao. The number that has to close.
  margin_target_used:      # what the MAO was solved at — a margin in Operator,
                           # `mao_percent` (and the assignment fee) in Core
  margin_floor_used:       # the floor margin mao_at_floor was solved at; empty in Core
  arv:                     # the ARV used.
                           #   LAND: the comp-derived value of the parcel at its exit —
                           #   there is no "after repair" on a parcel. Basis is
                           #   $/USABLE acre, and usable acreage belongs in the notes.
  arv_basis:               # comp-derived | avm-derived | land-comp-usable-acre
  rehab_band_used:         # light | medium | heavy
  rehab_psf_used:          # the $/sqft figure applied
  rehab_source:            # user | city-researched | derived | national-fallback
  screened_on:             # date — starts the staleness clock
  listing_url:             # where the current price can be re-read
  kill_reason:             # the ONE thing that killed it
  rescreen:                # eligible | ineligible
  rescreen_blocked_by:     # required when ineligible — the price-independent fail
actuals:                   # filled by Admin close-out capture, ONLY on closed deals
  closed_on:
  city_state:              # keyed like the rehab bands — "Hamilton, OH"
  sqft:                    # ABOVE-GRADE only, same rule the ARV uses
  scope_level:             # light | medium | heavy — AS EXECUTED, not as planned
  purchase_actual:
  rehab_actual:            # hard costs actually spent
  rehab_psf_actual:        # rehab_actual ÷ sqft — the calibration number
  days_held:
  sale_actual:
  selling_costs_actual:
  all_in_actual:
  net_profit_actual:
  margin_actual:           # net_profit_actual ÷ all_in_actual
  # the projections copied from the underwrite, so variance is computable
  rehab_projected:
  days_projected:
  sale_projected:
  margin_projected:
```

Rules: every record handed off MUST carry `stage`, `source`, and `next_action` — a deal
without a next action is how deals die. Scoring reasons travel with the score so the
user always sees *why*.

## Why the Second Look block exists

A PASS is not a dead record — it is a priced record. Most fix-and-flip deals fail on one
thing: the ask is above the MAO. Prices get cut. If the MAO is stored at screen time, a
re-screen is one subtraction instead of a whole underwrite, which is what makes it small
enough to run unattended.

Two fields carry the weight. `mao` is the answer to "what would this have to cost";
`rescreen` is the answer to "could any price ever fix this". Both are written by
Underwriting at screen time, never guessed later.

## Why the actuals block exists

Rung 1 of the rehab ladder is the user's own bands, `source: user` — "from jobs they
actually closed." Nothing produces those unless closed deals are captured. Every finished
rehab is one free, local, investor-grade data point for the number with the most leverage
over the offer price. Uncaptured, it is thrown away and the bands stay derived forever.

`scope_level` is recorded **as executed**. A light that turned into a medium is a medium
data point. Filing it as light is how a light band inflates and starts killing good deals.
