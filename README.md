# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 11 Oct 2026: the account is serving again, and the stop is over.**
The Z-Bat Clinic campaign is ACTIVE with delivery `active` — no substatus, no
`account_spend_limit_reached`. **`STOP_REASON` is now `None`**, and the page clears
its own stop note from `CAMPS_ALL` and `DAILY_ALL`; nothing about it was hand-removed.

The sequence, all from the activity log:

| When | What |
|---|---|
| 1 Oct, 2:16 pm | **₹10,000 added to the balance.** The spending-limit block lifted and delivery resumed the same day (₹614.83). |
| 1 Oct, 8:18 pm | **Abhijeet Lenka paused both the Clinic and Dream Bat** (Active → Inactive). This — not the limit — is why 2–8 October is dark. |
| 9 Oct, 10:35 am | **Clinic daily budget raised ₹600 → ₹1,000**, and nine new creatives created. |
| 9 Oct, 10:36 am | Clinic reactivated (Pending Process → Active). It has served on the 9th, 10th and 11th. |

Dream Bat has stayed paused since 1 October and is now PAUSED at Meta.

**Account lifetime spend is ₹16,684.11** across 8 Sep – 11 Oct. The gap in the middle of
the chart is two separate things, and the page distinguishes them from the data: 26–30
September was the spending limit, 2–8 October was a deliberate pause.

**Z-Bat Clinic is the only campaign with results to show.** ₹8,386.02 for **11 purchases
at ₹762.37** — up from ₹6,377.06 and 8 purchases. Nine new creatives (`Clinic ₹199 · Reel
1/2/3 Hook A/B/C v2`) joined `Clinic ₹199 · Video 4 · Copy 3 Local offer` on 9 October and
have taken ₹1,651.79 between them for 2 purchases so far. Two zero-spend ads that used to
sit under this campaign (`Video 4 · Copy 1 Measured`, `Video 4 · Copy 2 Stop guessing`) are
no longer returned by Meta and have been dropped from `ADS_ALL`.

**Dream Bat closes at ₹1,605.39 and still no purchase** — 252 landing page views, 37 adds
to cart, 15 site leads, zero sales, across four days with delivery (23-25 Sep and 1 Oct). That is the quiz funnel's cart
problem a third time, on a campaign that never touched the quiz, and it is now a closed
experiment rather than a warning: it has been paused since 1 October.

**All three quiz campaigns read PAUSED and none has spent since 21 September** — **nine
refreshes now at exactly ₹6,692.70 and 1,019 leads**, with not even a late-attributed
lead moving. They also read PAUSED on 20 and 21 September while spending, so do not assume
a PAUSED status means a campaign is finished — always re-pull the days, and re-pull the
days either side of the last snapshot too. The page works all of this out from `CAMPS`
status and the `DAILY` rows and says it in its own words — do not hand-write a note
about it.

**Non-quiz spend on this account is now ₹9,991.41**, half again what the quiz has ever
spent (₹6,692.70). When the quiz is dark, this report describes a smaller and smaller
share of what the account is doing — worth saying to the client rather than letting the
page look idle.

**Worth knowing:** the store pixel kept recording sales with no ad spend behind them
through the dark days — 29 September alone shows **239 AddToCart events** on an account
that spent nothing. The shop sells on its own; it is the advertising that stops and starts.

**One revision to a published figure.** 25 September now shows **1 purchase** on the
Clinic campaign where the 29 Sep cut published 0. Meta attributed it late. That is why the
Clinic's lifetime purchases read 11 rather than 10.

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
`start_time`/`end_time`; max lookback 28 days — and the range must be **strictly under**
28 days, not exactly 28, or the call is rejected. Events are stamped **−07:00**, and
an IST day D is the 24 buckets from (D−1) 12:00 through D 11:00. These counts
cover all site traffic, not only visitors from ads, so they run ahead of the Meta
lead count — the page says so.

**The bucketing convention is verified again on this pull.** It reproduces five of the
fifteen overlapping published days exactly (16, 19, 22, 26 and 28 Sep) and on every other
day the new count is **greater than or equal to** the published one on every event — the
signature of late-arriving events, not of a wrong day boundary. The biggest mover is
29 September, which the last cut published as a part-day at 11:10 am; complete, it is
350 PageViews and 239 AddToCarts rather than 83 and 3. Because the lookback is 28 days,
rows before 15 September cannot be re-pulled and are carried forward unchanged.

**A new event has started firing: `Search`** (3 events in the window). It is **not** in
`EV` and is not carried, because adding a column would mean back-filling it for days that
can no longer be pulled. If it gains volume, add it to `EV` and rebuild `PIXEL` from a
window that covers everything you want to show.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.
