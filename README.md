# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 19 Oct 2026: delivery has resumed and the account spending limit
is no longer biting.** The block described in the previous snapshot is over — the
account served again on 1 Oct, then continuously from 9 Oct. `STOP_REASON` is back
to `null` and the page's own stop-detector has cleared itself. Lifetime account
spend is now **Rs 22,862.89**.

**A seventh campaign has started: `Air Light - Purchase - India - Rs1000/day - Oct
2026`** (`120253356448770067`), live since 14 Oct and the **only ACTIVE campaign on
the account** (Rs 700/day at Meta, despite the Rs 1000/day in its name). It has taken
Rs 3,890.04 for **7 purchases** on five creatives, `Air Light - Hook1` carrying 71%
of the spend. It is appended to `CAMPS_ALL` as index 6 with its own group key
`air`, so no historical row re-labels itself.

**The Clinic and Dream Bat campaigns are now both PAUSED.** The Clinic closed at
Rs 9,298.21 and **11 purchases**; it picked up nine new `Reel * Hook * v2` creatives
that ran 9-12 Oct alongside the original `Video 4 - Copy 3 Local offer`. Dream Bat
closed at Rs 2,981.94 and **finally took its first purchase** (12 Oct) after the
cart problem flagged in the previous snapshot - 454 landing page views and 72 adds
to cart for that one sale, so the problem is real but no longer absolute.

**All three quiz campaigns remain PAUSED and none has spent since 21 September** —
still exactly Rs 6,692.70 and 1,019 leads, unmoved across the whole October window,
with not even a late-attributed lead. They also read PAUSED on 20 and 21 September
while spending, so do not assume a PAUSED status means a campaign is finished —
always re-pull the days, and re-pull the days either side of the last snapshot too.
The page works all of this out from `CAMPS` status and the `DAILY` rows and says it
in its own words — do not hand-write a note about it.

**Worth knowing:** the store pixel records far more activity than the ads explain —
the shop sells with no ad spend behind it. One figure needs a human eye: **IST
29 Sep booked 239 AddToCart events, 231 of them in two hours** (05:00-06:00 at
-07:00) on a day the account spent nothing at all. That looks like a bot or a
catalog sync rather than demand; it is carried in `PIXEL` as measured.

Non-quiz spend on this account is now ₹16,170.19, well over twice what the quiz
has ever spent (₹6,692.70). When the quiz is dark, this report describes a smaller and
smaller share of what the account is doing — worth saying to the client rather
than letting the page look idle.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** All seven
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
