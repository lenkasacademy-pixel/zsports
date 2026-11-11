# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 11 Nov 2026: delivery is back, and the account is now mostly
selling bats.** The account spending limit that stopped everything on 26 September
has been raised or removed — `STOP_REASON` is back to `None` and the page's
stop note clears itself. Lifetime spend is now **₹41,618.70** across 8 Sep–11 Nov,
up from ₹14,406.77.

**Two new campaigns opened in October and one of them is now the whole account.**

- `Air Light · Purchase · India · ₹1000/day · Oct 2026` (`120253356448770067`) is
  the only **ACTIVE** campaign. It has taken **₹19,929.52** since 14 October —
  48% of everything the account has ever spent — for **31 purchases at ₹642.89**,
  on a ₹1,200/day budget. It is still serving today.
- `Dream · No AI Bat Test · Purchase · India · ₹700/day · Oct 2026`
  (`120253597607430067`) ran 28–31 October only: ₹1,878.81, 1 purchase.

**The clinic came back and then stopped again.** `Z-Bat Clinic · Parel · ₹199
Session Sales` ran again 9–12 October and closes at ₹9,298.21 for **11 purchases**
(₹845.29 each, up from 8 at the last cut). Its copy campaign spent for the first
time: ₹837.52, no purchases. The clinic was relaunched with nine new `Reel … v2`
creatives alongside the original `Video 4 · Copy 3 Local offer`, which still
carries ₹6,739.14 of the ₹9,298.21 and 9 of the 11 purchases.

**`Dream Bat · Direct sales` finally bought something.** ₹2,981.94 over 8 days
and **1 purchase** — it was ₹1,337.01 and zero at the last cut. One sale on
₹2,982 is not a result, but it is no longer a flat zero, and the cart problem the
last refresh flagged is still the story: 72 adds to cart, one order.

**The quiz has not spent a rupee since 21 September.** Nine refreshes now at
exactly ₹6,692.70 and 1,019 leads, with not even a late-attributed lead moving.
All three quiz campaigns read PAUSED. `DAILY`, `REELS`, `PLACE` and `AGE` are
therefore unchanged in substance — `DAILY` only gained trailing zero rows out to
the new `SNAP_DAY`, which is what makes the dark run visible as data rather than
as a gap. The page works this out from `CAMPS` status and the `DAILY` rows and
says it in its own words — do not hand-write a note about it.

**Non-quiz spend is now ₹34,926.00 against the quiz's ₹6,692.70** — the quiz is
16% of what the account does. When the quiz is dark this report describes a
smaller and smaller share of the account, which is worth saying to the client
rather than letting the quiz sections look idle.

**The store pixel keeps selling without ads behind it.** 134 Purchase events in
the 28-day window against 33 Meta-attributed purchases, on 28,824 page views. The
shop is doing more than the advertising can account for.

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

The third group's key changed from `dream` to **`bats`** on 11 Nov 2026 and its
display name from "Dream Bat" to **"Direct bat sales"**, because three campaigns
now sell bats direct — Dream Bat, Air Light and Dream · No AI Bat Test — and
labelling the group after one of them was misleading. The grouping is by *what a
campaign sells*, not by product name: a new bat campaign joins `bats` rather than
getting a group of its own.

**The campaign count is derived, not written.** The masthead and the account
intro read `CAMPS_ALL.length`. They used to say "6 campaigns" and "Six campaigns"
in literal text, which went stale the moment a seventh opened.

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
  It is currently **+₹0.01** (it was −₹0.01; the sign flips between pulls).
- Campaign day rows sum to each account-level daily total, **except on
  `SNAP_DAY`**. On 11 Nov 2026 the part-day read ₹187.76 at campaign level against
  ₹177.29 at account level minutes earlier, and a second account read minutes
  later gave ₹192.10 — the day moved **₹4.34 between two calls**. The settled day
  before it (10 Nov) disagreed by ₹0.18 on the first read and matched exactly on
  the second. Expect this on the last day or two and do not chase it; it is the
  whole reason `SNAP_DAY` is held out of every headline.

### Store pixel

Pull with `ads_get_dataset_stats`, `aggregation: "event"`, `granularity: "hour"`,
unix `start_time`/`end_time`; **max lookback 28 days**. Events are stamped
**−07:00**, and an IST day D is the 24 buckets from (D−1) 12:00 through D 11:00.
These counts cover all site traffic, not only visitors from ads, so they run
ahead of the Meta lead count — the page says so.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.

**The quiz-start event is `BatFitStarted`, not `BatFitStart`.** It maps to `BFS`.
Getting it wrong silently zeroes the start rate and the completion rate for every
new day, because a missing key just reads as 0.

**A `Search` event also fires** (3 in the 28-day window). It is not in `EV` and is
deliberately dropped — `EV` order must not change, or every historical `PIXEL`
row re-labels itself. If it is ever worth showing, **append** it to `EV`.

**28 days is a hard wall, and a late refresh leaves a hole.** This refresh ran 43
days after the previous one, so **30 Sep – 14 Oct 2026 can no longer be read at
all** and is simply absent from `PIXEL`. The page detects the hole from the dates
in `PIXEL` itself and says which days are missing and that they are *absent, not
empty* — do not fill them with zeros, which would read as a fortnight of no
traffic. The lesson: **refresh the pixel at least every 28 days** even if nothing
else has changed.
