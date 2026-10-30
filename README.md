# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | 8 Sep 2026 – 30 Oct 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 30 Oct 2026: delivery has resumed and the account has doubled.**
The account spending limit that held it dark from 26 September has been lifted — spend
restarted on **1 Oct**, went quiet again 2–8 Oct, and has run more or less continuously
since **9 Oct**. `STOP_REASON` is therefore back to `None`, and the page clears its own
stop note from `CAMPS_ALL` and `DAILY_ALL`. Lifetime account spend is now
**₹29,950.74 across 53 days** (8 Sep – 30 Oct), over **8 campaigns, 7 of which have
spent**. 30 Oct is the part-day.

**Two new campaigns, and one of them is the best thing on the account.**

| Campaign | Ran | Spend | Result | Each |
|---|---|---|---|---|
| **Air Light · Direct sales** (new) | 14–27 Oct | ₹9,776.23 | 14 purchases | **₹698.30** |
| Clinic · Parel ₹199 sessions | 13 Sep–12 Oct | ₹9,298.21 | 11 sessions | ₹845.29 |
| Dream Bat · Direct sales | 23 Sep–14 Oct | ₹2,981.94 | 1 purchase | ₹2,981.94 |
| **Dream · No AI Bat Test** (new, LIVE) | 28–30 Oct | ₹1,201.66 | 1 purchase | ₹1,201.66 |

`Air Light` is a **fourth product line** and has its own entry in `GROUPS` — it sells bats
direct like Dream Bat but is not a Dream Bat, so folding it into that group would have
mislabelled it. `Dream · No AI Bat Test` is grouped under `dream`. Only that last campaign
is ACTIVE; everything else on the account is paused.

**Dream Bat finally made a sale.** The last cut recorded ₹1,337.01, 36 adds to cart,
14 site leads and **no purchase** — the quiz funnel's old cart problem reappearing on a
campaign that does not touch the quiz. It closed at ₹2,981.94 with **one** sale, booked on
13 Oct. One sale on nearly ₹3,000 is still the weakest line on the account; the flag stands.

**The quiz has not moved for a ninth refresh** — still exactly ₹6,692.70 and 1,019 leads,
with no late-attributed lead. All three campaigns read PAUSED and none has spent since
**21 September**. `DAILY`, `REELS`, `PLACE` and `AGE` are byte-identical to the last cut,
and `PLACE_DRIFT` is still −0.01. They also read PAUSED on 20 and 21 September *while*
spending, so do not assume a PAUSED status means a campaign is finished — always re-pull
the days, and the days either side of the last snapshot too.

**Non-quiz spend is now ₹23,258.04, three and a half times what the quiz has ever spent.**
When the quiz is dark, the quiz-only sections describe a smaller and smaller share of what
the account is doing — worth saying to the client rather than letting the page look idle.

**Meta late-attributed one ₹199 clinic session to 25 September** since the last snapshot.
That was the only revised historical row in the whole file; it is why the clinic's day
rows now sum to 11 purchases rather than 10, matching its creative rows and its lifetime
total. Every other pre-existing `DAILY_ALL` row came back identical.

**The store pixel has a three-day hole that cannot be filled.** `ads_get_dataset_stats`
looks back **28 days** only, and this refresh ran 31 days after the last one, so IST
**30 Sep, 1 Oct and 2 Oct** are out of reach for good. `PIXEL` simply has no rows for
them — absent, not zero — and the page now says so in its own words, computed by
comparing `PIXEL` dates against `ALL_DAYS`, so the sentence disappears once the gap ages
out of `ALL_DAYS`. **Refresh inside 28 days to avoid repeating this.** The Meta-side
arrays are unaffected.

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

**Max lookback is 28 days and it is a hard wall.** If more than 28 days have passed
since the last refresh, the days in between are gone — there is no archive call. Leave
those days out of `PIXEL` rather than writing zeros; the page detects the gap against
`ALL_DAYS` and explains it. Refresh at least every four weeks.
