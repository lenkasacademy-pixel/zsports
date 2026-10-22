# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 22 Oct 2026: delivery has resumed and the account spending limit
no longer binds.** A new campaign, **Air Light · Purchase · India** (`120253356448770067`),
was created in October, started delivering on **9 October** and has run every day
since. It reads `delivery: active`. `STOP_REASON` is therefore set back to `null`,
as this file has always said it should be once delivery resumes — the page works
the stop out from `CAMPS_ALL` status and `DAILY_ALL` and now shows none.

The account went dark 26–30 Sep, took ₹614.83 on 1 Oct, was dark again 2–8 Oct,
and has delivered daily from the 9th at roughly ₹600–1,070 a day. Lifetime account
spend is now **₹25,228.47** (it was ₹14,406.77 at the 29 Sep snapshot).

**Air Light is the best thing on the account.** ₹6,255.62 for **11 purchases at
₹568.69**, plus 99 adds to cart and 941 landing page views in nine days. For
comparison the clinic has taken ₹9,298.21 for its 11 sessions, **₹845.29 each**.

**Dream Bat finally sold one.** It closed at ₹2,981.94 for **1 purchase**, 72 adds
to cart and 24 site leads, and is now PAUSED. The cart problem flagged at the last
snapshot — adds to cart but no sales — did eventually break, but only once.

**The clinic is paused** at ₹9,298.21 and 11 purchases, and picked up two more
sales on 9 and 10 October before stopping.

**The quiz is still completely dark and completely unchanged** — ₹6,692.70 and
1,019 leads, the same figures as eight refreshes ago, with not even a
late-attributed lead moving. Nothing has spent on it since 21 September. Because
the quiz has not moved, `REELS`, `PLACE` and `AGE` are unchanged and still
reconcile to `DAILY`.

**One historical day was revised.** Meta added a purchase to **25 September** for
the clinic campaign after the last snapshot (0 → 1). A full re-pull of 8–25
September confirms every other historical row is unchanged. This is why the
README says to re-pull the days either side of the last snapshot — the one-day
overlap used at first was not enough to catch it.

## Result groups

`GROUPS` gained a fourth entry, `air`, so Air Light is priced in its own currency
rather than blended with Dream Bat. Both sell bats direct, but they are different
products and the account now reports **four** distinct result kinds. The page
counts campaigns and result kinds from the data, so the masthead, the intro
sentence, the table footer and the "blanks in the result column" note all update
themselves — three of those were hard-coded to six campaigns and to a specific
pair of blank rows, and were made data-driven in this refresh.

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
`start_time`/`end_time`; max lookback 28 days. **The 28-day limit is a hard
ceiling on what can be refreshed**: on the 22 Oct refresh only 25 Sep onward
could be re-pulled, so `PIXEL` rows before that date are carried forward from the
previous snapshot and must not be assumed re-verified. The response comes back as
hourly buckets, not days, so it has to be folded into IST days by the rule below. Events are stamped **−07:00**, and
an IST day D is the 24 buckets from (D−1) 12:00 through D 11:00. These counts
cover all site traffic, not only visitors from ads, so they run ahead of the Meta
lead count — the page says so.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.
