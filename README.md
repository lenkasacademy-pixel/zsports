# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | 8 Sep 2026 — 27 Nov 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 27 Nov 2026: delivery resumed on 9 October and the account has
quadrupled.** The September block is over - the account spending limit that
stopped everything on 26 Sep has been raised or removed, nothing on the account
reports `account_spend_limit_reached` any more, and `STOP_REASON` is back to
`None`. Lifetime spend is now **₹65,157.82 across 81 days** (8 Sep - 27 Nov)
over **11 campaigns with delivery**, against ₹14,406.77 over 6 campaigns at the
last snapshot. The page works the stop-and-restart out from `CAMPS_ALL` status
and the `DAILY_ALL` rows, so its delivery note has cleared itself - do not
hand-write or hand-remove it.

**A new product line now dominates the account: Air Light.** Four campaigns
started on 14 October and have taken **₹42,399.41 for 79 purchases, ₹536.70
each** - 65% of everything the account has ever spent, and 79 of its 92 sales.
This is the first thing on this ad account that converts at any scale.

| Air Light campaign | Spend | Purchases | Each | Ran | Status |
|---|---|---|---|---|---|
| Air Light · Purchase | ₹21,613.12 | 31 | ₹697.20 | 14 Oct - 13 Nov, 27d | PAUSED |
| Air Light · Purchase (Copy) | ₹11,678.14 | 29 | **₹402.69** | 13-27 Nov, 10d | **ACTIVE** |
| Air Light · Purchase (Copy 2) | ₹8,898.50 | 17 | ₹523.44 | 21-26 Nov, 6d | PAUSED |
| Air Light · Bid cap test | ₹209.65 | 2 | ₹104.83 | 20-22 Nov, 3d | PAUSED |

**The copy is beating the original by a wide margin** - ₹402.69 a purchase
against ₹697.20 - and it is the one campaign still running. **One creative is
carrying the line:** `Air Light - Hook1` has taken ₹34,585.71 and produced
**68 of the 79 purchases** at ₹508.61 each. `Hook3` is ₹631.23 across 10,
`Hook5` has one, and `Hook2` and `Hook4` have spent ₹636.25 between them for
**no purchase at all**.

**The bid cap test reads ₹104.83 a purchase - do not act on it yet.** That is
two purchases on ₹209.65 over three days. It is the cheapest number on the
account and also the thinnest; it needs a real budget behind it before it means
anything.

**The quiz has not spent since 21 September.** Nine refreshes now at exactly
₹6,692.70 and 1,019 leads, ₹6.57 each, with not even a late-attributed lead
moving. All three quiz campaigns read PAUSED. It has **never produced a single
purchase**, and it is now only **10.3% of account spend** - the quiz sections
below describe a tenth of what this account is doing. The remaining ₹58,465.12
is Air Light, the clinic and Dream Bat, which the account section at the top
covers. Note the quiz campaigns also read PAUSED on 20 and 21 September *while
spending*, so do not treat a PAUSED status as proof a campaign is finished -
always re-pull the days, and the days either side of the last snapshot too.

**The old cart problem is now answered, and the answer is Air Light.** Dream Bat
was flagged last time at ₹1,337.01 with no purchase; across both Dream
campaigns the line has now spent **₹5,929.98 for 2 sales** (₹2,964.99 each) and
stopped. The clinic closed at **₹9,298.21 for 11 ₹199 sessions** (₹845.29 each)
and last delivered on 12 October. Air Light does the same job for ₹536.70.

**Only one campaign is ACTIVE** (`Air Light · Purchase (Copy)`). The other ten
are paused, so if that one is paused too the account goes dark again.

**The store pixel table is frozen at 8-29 September.** `ads_get_dataset_stats`
only serves the last 28 days, so the 30 Sep - 29 Oct window can never be
re-pulled, and appending a recent window would hide a 30-day hole inside the
table's own "Period" total. The table therefore keeps the span the quiz actually
ran in and says so on the page. If store-pixel coverage of the Air Light funnel
is wanted, that is a new section reading a recent 28-day window, not an
extension of this one.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** Every
campaign on account `1811409506889048` is first-class: `CAMPS_ALL`,
`DAILY_ALL` (a row per campaign per day) and `ADS_ALL` (a row per ad) drive a new
account section at the top, and every campaign gets its own creative table.

**A new product line needs a `GROUPS` entry.** `CAMPS_ALL[i][2]` is a group key
and `GROUPS` names it, what it sells and the singular/plural of its result. A
campaign whose group key is missing from `GROUPS` drops out of the account view
silently, so adding a product line means adding one `GROUPS` row. `airlight` was
added on 27 Nov 2026 for exactly this reason. The campaign counts in the
masthead and the account intro are derived from `CAMPS_ALL.length` - they used
to be the hand-written word "six" and went stale the moment a seventh appeared.

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
