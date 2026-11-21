# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 21 Nov 2026: the account is live again.** Delivery resumed on
1 Oct and has run near-continuously since 9 Oct. The account spending limit that
stopped it on 26 Sep is no longer binding, so `STOP_REASON` is back to `None` and
the page drops its stop notice on its own. Lifetime spend is now **₹54,991.98**
(was ₹14,406.77 at the 29 Sep cut).

**Only one campaign is ACTIVE:** `Air Light · Purchase · Bid Cap Test ₹340/₹320 ·
Nov 2026` (`120254004552940067`), two ad sets carrying **₹3,000/day each** with bid
caps of ₹340 and ₹320. It took only **₹122.94 on 20 Nov** and ₹22.32 so far on the
21st — roughly 2% of the budget it is carrying. The bid caps, not the budget, are
setting the pace; that is the thing to watch on the next refresh.

**Air Light is now the account's biggest line by a wide margin:** ₹32,234.20 for
**60 purchases, ₹537.24 each**, across three campaigns. The clinic is at
₹10,135.73 for 11 sessions (₹921.43); Dream Bat, now two campaigns, at ₹5,929.98
for **2 sales** — the cart problem flagged on 29 Sep has not gone away, and
₹5,930 has now been spent behind two sales.

**The quiz has not moved at all.** Still exactly ₹6,692.70 and 1,019 leads, as at
every refresh since 21 September; all three quiz campaigns read PAUSED. Its
arrays still run to `SNAP_DAY`, padded with zero rows, so the part-day invariant
holds — do not trim them back to the last day the quiz spent.

**The part-day moves while you work.** On this refresh the 21 Nov figure read
₹21.67, then ₹21.69, then ₹22.32 across three calls about twenty minutes apart,
and the ad-level and campaign-level reads of it disagreed by one add to cart
until the campaign read caught up. Reconcile the settled days exactly and let
the part-day drift — it is out of every headline anyway.

**Non-quiz spend is now ₹48,299.28 against the quiz's ₹6,692.70** — the quiz is
roughly an eighth of what the account has ever spent. The quiz-only sections
describe a shrinking slice of this account; worth saying to the client rather
than letting the page look like the quiz is the whole story.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** All
campaigns on account `1811409506889048` are first-class — 11 of them as at
21 Nov 2026, 10 with any spend behind them: `CAMPS_ALL`,
`DAILY_ALL` (a row per campaign per day) and `ADS_ALL` (a row per ad) drive a new
account section at the top, and every campaign gets its own creative table.

**Leads and purchases are never blended.** The account sells four different
things — quiz leads, ₹199 clinic sessions, Dream Bats and Air Light bats — so
each group is priced in its own currency and there is deliberately no single
account-wide "cost per result". `CAMPS_ALL[i][5]` says which result kind a
campaign reports and `CAMPS_ALL[i][2]` which group it belongs to.

**Adding a product line = one entry in `GROUPS`.** `airlight` was added on
21 Nov 2026, when three Air Light campaigns appeared. Air Light is a different
bat from the Dream Bat, so folding it into the `dream` group would have priced
two products as one. Nothing else is group-specific.

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

**`PIXEL` is deliberately still the quiz's own window, 8–29 Sep 2026.** The
28-day lookback means anything older than about four weeks can never be fetched
again, so 30 Sep to 23 Oct 2026 is permanently unavailable and `PIXEL` cannot be
extended without a hole in the middle of it. The section it feeds is the *quiz*
funnel and the quiz has not run since 21 Sep, so the window it covers is the
right one — but it is older than the rest of the page, and that is a known
limitation rather than a stale array. If the quiz restarts, begin `PIXEL` again
from that day instead of trying to bridge the gap.
