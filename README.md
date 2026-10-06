# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 6 Oct 2026: the spend-limit stop is over, and the advertiser has
switched the whole account off.** The sequence, all from the activity log:

| When | What |
|---|---|
| 26–30 Sep | nothing served; Meta's reason was `account_spend_limit_reached` |
| 1 Oct, 2:16 pm | **₹10,000 added to the balance** (UPI) |
| 1 Oct (part-day) | delivery resumed — the account took **₹614.83** on 4,289 impressions |
| 1 Oct, 8:18 pm | Abhijeet Lenka **paused the Clinic and Dream Bat campaigns** (Power Editor) |
| 2–6 Oct | nothing served; **no campaign on the account is ACTIVE any more** |

So the limit was the cause and topping up the balance fixed it, but the account
ran for only part of one day before the last two live campaigns were paused by
hand. **`STOP_REASON` is now `null`** — the page's delivery-stop note is computed
from the ACTIVE campaigns and there are none, so it clears itself. Nothing was
hand-removed. A paused account is a decision, not a fault, and the page should
not be shouting about a stop.

**Nothing is running. All six campaigns read PAUSED** (plus one ARCHIVED
`OLD · DO NOT USE` campaign that never delivered and stays out of the arrays).
If the client expects delivery, that is the thing to raise.

**Worth knowing while the ads are off:** the store pixel keeps recording sales
with no ad spend behind them — 2 October logged **9 Purchase and 9
AddPaymentInfo events on a day the account spent ₹0**. The shop is selling; it
is the advertising that has stopped.

**The quiz has not moved at all — ninth refresh running.** All three quiz
campaigns are PAUSED and none has spent since 21 September: still exactly
**₹6,692.70 and 1,019 leads**, and `REELS`, `PLACE` and `AGE` came back
byte-for-byte identical to the last pull. They also read PAUSED on 20 and 21
September *while spending*, so do not assume a PAUSED status means a campaign is
finished — always re-pull the days, and the days either side of the last
snapshot too. The page works all of this out from `CAMPS` status and the `DAILY`
rows and says it in its own words — do not hand-write a note about it.

**Dream Bat** is at ₹1,605.39 over four part-days: 252 landing page views, 37
adds to cart, 15 site leads, **still no purchase**. That is the quiz funnel's old
cart problem appearing again on a campaign that does not touch the quiz, and it
is now ₹1,605 deep — worth flagging before it spends more.

**The clinic is at ₹6,723.51 and 9 purchases.** Note that the ninth is a *late
attribution*: Meta added a purchase to **25 September** after that day had been
published as a zero, taking the campaign from 8 to 9. The day row was corrected
rather than the difference being parked on the newest day. Expect more of this on
purchase counts and re-check settled days, not only recent ones.

Non-quiz spend on this account is now ₹8,328.90 against the quiz's ₹6,692.70, so
**this report now describes less than half of what the account has spent**. Worth
saying to the client rather than letting the page look idle.

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

**The bucket convention is verified, not assumed.** On the 6 Oct 2026 refresh the
window `[(D−1) 19:00 UTC, D 19:00 UTC)` — which is `(D−1) 12:00` to `D 11:00` at
−07:00 — reproduced the previously published 29 Sep row exactly: its 83 PageViews
are the sum of that window's first eight populated buckets, which is precisely how
far the 11:10 am IST snapshot had got. Keep the window as it is.

**Use `aggregation: "event_total_counts"` for the day totals.** It returns one
stats entry per window instead of 24 hourly ones, so a day is one small response
rather than a large one. Two gotchas: its `timestamp` is the time of the *query*,
not of the data, and it splits each event across several rows with the same
`value` — you must group by event name and sum. Spot-checked against the hourly
form on four days; the totals agree exactly.

**29 September settled into an anomaly that needs a human.** Published as a
part-day at 83 PageViews, it closed at **350 PageViews and 239 AddToCart events —
on a day the ad account spent nothing at all.** 231 of those 239 arrived in two
hours (05:00 and 06:00 at −07:00, i.e. 17:30–18:30 IST): 96 then 135. The quiz
had been dark since the 21st and no campaign served anything that day, so this is
not ad traffic. It looks like a bot, a scraper or a cart loop on the site rather
than real demand, and it inflates the pixel column the page already warns runs
ahead of the Meta lead count. Worth checking the store logs before anyone quotes
an add-to-cart number from the pixel.
