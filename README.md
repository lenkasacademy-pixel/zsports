# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | 8 Sep – 9 Oct 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 9 Oct 2026: the spend-limit block is gone, the account served for
exactly one more day, and then everything was paused.** Delivery resumed on
**1 October** — the Clinic took ₹346.45 and Dream Bat ₹268.38 — and has produced
nothing since. All six campaigns now read **PAUSED**, and Meta reports delivery
`off` with substatus `off` rather than `account_spend_limit_reached`. So
`STOP_REASON` is set back to `None`/`null`: there is no delivery fault left to
explain, only a decision. The page's own stop detector stands down for the same
reason — it only fires when a campaign is still ACTIVE and still delivering
nothing — so the "nothing has been served since" block no longer renders, and
that is correct. Lifetime account spend is now **₹15,021.60**.

**The clinic found a ninth session.** It closes at ₹6,723.51 for **9 purchases**
(₹747.06 each) — up from ₹6,377.06 and 8. The extra purchase is a late
attribution onto **25 September**, a day that had already been published with
zero; its 1 October ₹346.45 bought 24 page views and no sale. `omni_purchase`
for a settled day can still move, so re-pull the whole campaign rather than
appending.

**Dream Bat still has no sale.** ₹1,605.39 over four spending days, 252 landing
page views, 37 adds to cart, 15 site leads, **zero purchases**. Its 1 October
₹268.38 went entirely to `Dream · Carousel · 3 angles` (₹36.35 → ₹304.73) and
bought one more cart add and one more lead. That is the quiz funnel's old cart
problem showing up on a third campaign now, and it is worth saying before it
spends more.

**All three quiz campaigns remain PAUSED and have not spent since 21 September** —
**nine refreshes now at exactly ₹6,692.70 and 1,019 leads**, with not even a
late-attributed lead moving, and 18 days dark. They also read PAUSED on 20 and 21
September *while spending*, so do not assume a PAUSED status means a campaign is
finished — always re-pull the days, and the days either side of the last snapshot
too. The page works all of this out from `CAMPS_ALL` status and the `DAILY` rows
and says it in its own words — do not hand-write a note about it.

Non-quiz spend on this account is now ₹8,328.90, against the quiz's ₹6,692.70.

**Worth knowing while the ads are off: the shop kept selling.** The store pixel
recorded **21 Purchase events across 1–8 October** with no ad spend behind any of
them (9 on the 2nd, 6 on the 6th, 3 on the 4th, 3 on the 8th). Two AddToCart
spikes also stand out — **239 on 29 September** and 66 on 6 October, against a
single-digit-to-teens daily norm. Those counts cover all site traffic, not only
visitors from ads, so they are not an advertising result; but 239 in a day is far
enough outside the range to be worth checking against the shop's own orders in
case something on the page is firing AddToCart that should not.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** All six
campaigns on account `1811409506889048` are now first-class: `CAMPS_ALL`,
`DAILY_ALL` (a row per campaign per day) and `ADS_ALL` (a row per ad) drive a new
account section at the top, and every campaign gets its own creative table.

**Leads and purchases are never blended.** The account sells three different
things — quiz leads, ₹199 clinic sessions, and bats — so each group is priced in
its own currency and there is deliberately no single account-wide "cost per
result". `CAMPS_ALL[i][5]` says which result kind a campaign reports.

**Reach is carried, not derived.** `CAMPS_ALL[i][6]` is Meta's own de-duplicated
lifetime reach per campaign. It can never be obtained by adding the day rows up,
so the account table shows it per campaign and leaves the total blank.

The quiz keeps its own sections below the account view, because it is the only
funnel on the account with site events behind it — the placement, age and pixel
breakdowns exist for the quiz and nothing else. Those arrays (`DAILY`, `ADS`,
`PLACE`, `AGE`, `H`) stay quiz-only and are unchanged.

**The page detects a delivery stop on its own.** If every ACTIVE campaign serves
nothing on the trailing days, the account section says so, names the budget those
campaigns are still carrying, and gives Meta's own reason when `STOP_REASON` is
set (otherwise it points at the payment method). It is computed
from `CAMPS_ALL` status and `DAILY_ALL`, so it clears itself when delivery
resumes — do not hand-write or hand-remove that note. Explicit zero rows are
added to `DAILY_ALL` for live campaigns on dead days so the stop is visible as
data rather than as a gap.

**What the builder asserts before writing the file:** for every campaign, its day
rows and its ad rows must agree on spend, impressions, link clicks, page views,
adds to cart, purchases and leads; and the three quiz campaigns in `DAILY_ALL`
must reconcile against the quiz-only `DAILY` on both spend and leads. Reach is
excluded from these checks on purpose.

## Shape

`index.html` is a **single self-contained file**. Figures live in flat arrays in
the `<script>` block at the bottom; everything above them — every table, every
bar, **and every sentence** — is rendered from those arrays at load time. Nothing
calls the network. There is no build step and no separate internal version.

This is the same shape as the Elixir Creative Ledger and the O2 / Halcyon calls
reports, and it is what makes the page safe to refresh automatically: no prose
goes stale when a day is added, because no prose is written by hand.

## Refreshing it

Patch the arrays, nothing else:

| Array | Row shape |
|---|---|
| `DAILY` | `[date, spend, impressions, lpv, cart adds, leads]`, one per day |
| `REELS` | `[creative, spend, impressions, lpv, cart adds, leads, [campaign indexes]]`, sorted by spend desc |
| `PLACE` | `[platform, position, spend, impressions, lpv, leads]` |
| `AGE` | `[band, gender, spend, impressions, lpv, leads]` |
| `PIXEL` | `[date, ...counts in EV order]` — `EV` names the events |
| `SNAP_DAY` | the still-running day; `SNAP_TS` the snapshot stamp |

**The last `DAILY` row must be `SNAP_DAY`.** The page treats that day as still
accruing: it is shown separately, labelled "so far", and excluded from every
headline total and from the blended cost per lead. That is the guard against
Meta's ~48h revisions — a part-day must never set a headline number.

### Checks that must pass before publishing

- `REELS` totals equal `DAILY` totals on spend, impressions **and** leads.
- `AGE` totals equal `DAILY` totals on spend and leads.
- `PLACE` matches `DAILY` exactly on leads; a few rupees of spend drift is
  Meta's own breakdown rounding and is reported on the page (`PLACE_DRIFT`).

### Store pixel

Pull with `ads_get_dataset_stats`, `aggregation: "event"`, unix
`start_time`/`end_time`; max lookback 28 days. Events are stamped **−07:00**, and
an IST day D is the 24 buckets from (D−1) 12:00 through D 11:00. These counts
cover all site traffic, not only visitors from ads, so they run ahead of the Meta
lead count — the page says so.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.

**`aggregation: "event_total_counts"` is the cheap way to do this.** It returns one
block of per-event totals for the whole range instead of 24 hourly buckets, which
is all `PIXEL` needs — one call per IST day. It splits each event across several
rows (by source), so **sum the rows per event name**.

**`end_time` must be the top of the next hour, not `:59:59`.** A bucket is included
only when it ends at or before `end_time`, so an IST day D runs
`start = (D-1) 12:00:00 -07:00` to `end = D 12:00:00 -07:00`. With
`end = D 11:59:59` the 11:00 bucket is silently dropped — on 28 Sep that lost 26
PageViews, 4 BatFitStarted and 2 Leads out of 352/8/6. Verified on 9 Oct 2026 by
reproducing the published 28 Sep row exactly with the corrected end time.
