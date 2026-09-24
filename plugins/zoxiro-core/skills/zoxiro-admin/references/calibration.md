# Calibration — turning closed deals into the user's own rehab bands

## What this is for

Rung 1 of the rehab ladder is `source: user` — bands that "come from jobs they actually
closed." Nothing in the system produces those unless finished deals are captured. Without
capture, a user runs on `derived` numbers forever, and the single figure with the most
leverage over the offer price never improves no matter how many houses they do.

Every completed rehab is one free, local, investor-grade data point. This is the loop that
picks it up.

## Capture — what to collect at close

Fill the `actuals` block of the deal-record schema. Ask for what is missing; never
estimate a figure and file it as an actual.

Required for calibration: `city_state`, `sqft` (above-grade only), `scope_level` **as
executed**, `rehab_actual`, `closed_on`.

Collected at the same time because it will never be easier to get: `purchase_actual`,
`days_held`, `sale_actual`, `selling_costs_actual`, `all_in_actual`, `net_profit_actual`,
`margin_actual`, and the matching projections copied off the original underwrite.

**`scope_level` is as executed, not as planned.** A light that opened up into a medium is a
medium data point. Filing it as light inflates the light band, which raises every light
rehab estimate in that city, which lowers every MAO, which kills good deals — silently,
because a wrong band looks exactly like a right one. Ask plainly: "did it stay a light, or
did it turn into something bigger?"

## The promotion rule

A city's band moves to `source: user` only when **all** of these hold:

1. **At least 3 closed deals** at that scope level in that city. One deal is an anecdote;
   two cannot outvote an outlier.
2. The band value is the **median** of the observed `rehab_psf_actual`, never the mean. One
   foundation surprise should not move a light band.
3. Every contributing deal passes the sanity ceiling below.

Promotion is per city AND per scope level. Three closed lights in Hamilton calibrate
Hamilton's light band. They do not calibrate Hamilton's heavy band, and they do not
calibrate Dayton.

Record alongside the band: sample count, the date range it spans, and the median. A band
built on 3 deals from two years ago is not the same claim as one built on 9 from last
quarter, and the user should be able to see which they have.

## Guards

**The sanity ceiling applies to actuals too.** An actual "light" over $30/sqft, or an
actual heavy/gut over $90/sqft, does not silently become a band. Either the scope was
mislabeled or something went wrong on that job. Flag it, ask, and exclude it from the
median until it is resolved. The ceiling exists to keep retail pricing out of the bands;
a mislabeled actual gets in the same way.

**Never promote across cities.** But a promotion does improve the neighbours: once a city
is `user`-calibrated, other cities deriving from a distant market should re-derive from the
nearest `user` city instead. Update `derived_from` and say that it changed.

**A promotion changes the MAO.** Every stored MAO in that city was solved on the old band,
so mark that city's PASS pile for recompute rather than compare — Second Look's third
staleness trigger. A profile change is not finished until the numbers built on it know.

**Actuals never overwrite a projection.** The projection is kept so variance is computable.
Losing it destroys the only evidence of model bias.

## The variance report — useful before you have three

Even at n=1 or n=2, projected-versus-actual is worth reporting, as a bias note rather than
a band change:

- **Rehab bias** — "your rehab estimates run 18% under actual across 4 closed deals."
- **Timeline bias** — projected hold versus `days_held`. Carry cost is priced off the
  projection, so a consistent 6-week overrun is a real and repeatable hole in the margin.
- **Exit bias** — projected sale versus `sale_actual`. Persistent optimism here means the
  ARV method is running hot, not that the market moved.
- **Margin delivered** — `margin_actual` against the target the deal was underwritten at.
  This is the number that says whether the whole model is working.

Report direction and size, and say how many deals it rests on. Two deals is a hint. Eight
is a finding.
