# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 18 Nov 2026: delivery resumed and the account has more than tripled.**
Lifetime spend is **₹51,618.90** against ₹14,406.77 at the September snapshot. The
`account_spend_limit_reached` block is gone — `STOP_REASON` is back to `None` — and one
campaign (`Air Light · Purchase (Oct, Copy)`, ₹1,500/day) is live and spending today.

**Air Light is now the account.** Two new campaigns sell the Air Light bat and between
them have taken **₹28,860.49 for 55 purchases at ₹524.74 each** — far and away the best
cost per sale on the account, against Dream Bat's ₹2,964.99. The newer copy is doing
**₹301.97 a sale** across 24 sales on ₹7,247.37, which is the single best line here. They
are added to `CAMPS_ALL` as indexes 6 and 8, with a new `air` product group so they get
their own KPI tile; `Dream · No AI Bat Test (Oct)` joins the `dream` group as index 7.

**The quiz is still completely dark — this is the ninth refresh at exactly ₹6,692.70 and
1,019 leads.** It last spent on **21 September**; that is 58 days with no delivery and not
one late-attributed lead. All three quiz campaigns read PAUSED. The page works this out
from `CAMPS_ALL` status and the `DAILY` rows and says it in its own words — do not
hand-write a note about it. Non-quiz spend is now **₹44,926.20 against the quiz's
₹6,692.70**, so the quiz is 13% of what the account does; the page says so itself.

**Dream Bat finally made a sale** — one, on ₹2,981.94. The cart problem the September
snapshot flagged is no longer "no purchase at all", but one sale on three thousand rupees
is still the worst cost per result on the account.

**The clinic is at ₹9,298.21 for 11 sessions (₹845.29 each)**, up from ₹6,377.06 and 8.
`Clinic · Parel ₹199 (Copy)`, which had never been switched on, finally ran on 2–3
November for ₹837.52 and produced **no sale**. It is the one blank in the result column,
and the page now derives that sentence rather than carrying it as prose (see below).

### Two hardcoded things that had gone stale, now derived

The README's claim that *every* sentence is rendered from the arrays was not quite true,
and both exceptions broke on this refresh:

1. **The account note hardcoded `CAMPS_ALL[4]` and `CAMPS_ALL[5]`** and asserted "the two
   blanks in the result column are deliberate". Campaign 4 has since spent, and campaign 5
   has since made a sale, so the sentence was simply wrong. It now computes which
   campaigns never ran and which ran without a result, and pluralises itself.
2. **The campaign count was written as "six"/"All six"/"6 campaigns"** in three places.
   With nine campaigns those read false. There is now a `word()`/`Word()` helper and the
   counts derive from `CAMPS_ALL.length`.

Adding a campaign to a new product line also needs **one line in `GROUPS`** (key, display
name, what it sells, unit/units). Without it the campaign still appears in the account
table but gets no KPI tile and is silently dropped from "N of them with any spend" — which
is how ₹28,860 of Air Light spend first came out invisible above the table.

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
- **The live campaign's part-day moves between calls, so make it the residual.** On the
  18 Nov refresh the campaign-level daily pull and the ad-level pull for the one ACTIVE
  campaign disagreed by ₹6.49, 26 impressions, 2 page views and 1 add to cart purely
  because `SNAP_DAY` kept accruing between requests. Re-pulling just moves the
  disagreement. Set the `SNAP_DAY` row of `DAILY_ALL` to the ad-level totals minus the
  settled days, so "day rows == ad rows" holds exactly and the whole adjustment sits in
  the one day the page already excludes from every headline and labels "so far". Expect
  the account-level daily total for `SNAP_DAY` to differ from the campaign sum for the
  same reason.

### Store pixel

Pull with `ads_get_dataset_stats`, `aggregation: "event"`, `granularity: "hour"`, unix
`start_time`/`end_time`; **max lookback 28 days**.

**Bucket on an absolute boundary, not a local-clock one.** An IST day D is the 24 hourly
buckets from **19:00 UTC on D−1 to 19:00 UTC on D**. That is exactly what the old rule
("stamped −07:00, (D−1) 12:00 through D 11:00") meant, so it reproduces the historical
figures unchanged — but it keeps doing so after **1 November 2026, when the dataset's
stamps moved from −07:00 to −08:00** (US daylight saving ended). Reading the local clock
literally after that date shifts every day by an hour. Parse each timestamp with its own
offset, convert to UTC, then bucket.

**The 28-day limit loses days permanently.** This refresh could only reach back to
22 October, so **30 September – 21 October is a 22-day hole in `PIXEL`** and can never be
recovered. The rows are left out rather than zero-filled — the shop was trading in that
window, so zeros would be a lie — and the page detects the break in the date run and
explains it in its own words. **Refresh `PIXEL` at least every 28 days or lose days for
good.**

A twelfth event, `Search`, has started firing (3 events in the last 28 days). It is not in
`EV` and is not carried; add it only when it has volume worth a column, and note that
historical rows cannot be backfilled for it.

These counts cover all site traffic, not only visitors from ads, so they run ahead of the
Meta lead count — the page says so.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.
