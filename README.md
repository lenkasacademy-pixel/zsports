# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | 8 Sep 2026 – 13 Nov 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 13 Nov 2026: the account spending limit has been cleared and the
account is spending again.** Delivery resumed on 1 Oct and has run near-daily
since 9 Oct. `STOP_REASON` is back to `null` and the page's own stop note has
cleared itself — it is computed from `CAMPS_ALL` status and `DAILY_ALL`, so do
not hand-write or hand-remove it.

Lifetime account spend is now **₹44,384.25** across 8 Sep – 13 Nov, up from
₹14,406.77 at the 29 Sep snapshot. **Nine campaigns** have now run, not six.

**The account has moved on from the quiz.** Three campaigns have been added since
the last snapshot, all selling bats:

| Campaign | Spend | Result | Each | Status |
|---|---|---|---|---|
| Air Light · Purchase · Oct 2026 | ₹21,612.95 | 31 purchases | ₹697.19 | PAUSED |
| Air Light · Purchase · Oct 2026 – Copy | ₹12.89 | none yet | — | **ACTIVE** |
| Dream · No AI Bat Test · Oct 2026 | ₹2,948.04 | 1 purchase | ₹2,948.04 | PAUSED |

Air Light is now the largest single line on the account — more than three times
what the quiz has ever spent — and at ₹697 a sale it is the account's only
campaign buying purchases at anything like a sane price. Its Copy was created at
07:54 on 13 Nov and is the one ACTIVE campaign; everything else is paused.

**The clinic ran again in October and its creative was replaced.** The three
`Video 4 · Copy N` ads are archived or spent out, and **nine new `Reel N Hook X
v2` ads** have taken ₹2,559.07 between them. The clinic is at ₹9,298.21 for
**11 purchases** (₹845.29 each) and last delivered on 12 Oct.

**Dream Bat finally made a sale.** The 29 Sep note said "still no purchase" after
₹1,337.01; it is now ₹2,981.94 for 1 purchase, 72 adds to cart and 24 site
leads. The cart problem is still real — 72 adds to cart for one sale — and the
new `Dream · No AI Bat Test` campaign repeats it: 88 adds to cart, 1 sale.

**The quiz has not moved at all.** Nine refreshes now at exactly ₹6,692.70 and
1,019 leads. All three quiz campaigns read PAUSED, the last quiz spend was
21 September, and 13 Nov is the **53rd** dark day. Do not assume PAUSED means
finished — these same campaigns read PAUSED on 20 and 21 September while
spending — but re-pulling the days either side confirms nothing has been
revised. The page works this out from `CAMPS` status and the `DAILY` rows and
says it in its own words; do not hand-write a note about it.

Non-quiz spend on the account is now ₹37,691.55, five and a half times what the
quiz has ever spent. The quiz sections below describe a shrinking fraction of
what the account is doing — worth saying to the client rather than letting the
page look idle.

## The store pixel has a permanent 17-day hole

`ads_get_dataset_stats` will not return anything older than **28 days**. No
refresh landed between 29 Sep and today, so the window has closed over
**30 Sep – 16 Oct** and those IST days can never be recovered. The earliest day
still obtainable on 13 Nov is 17 Oct.

`PIXEL` has therefore been **left at its contiguous 8–29 Sep window** rather than
extended across the hole. Appending 17 Oct – 13 Nov would put a 17-day gap in
the middle of the funnel table, and the "Period" totals row — and the sentence
the page builds from it — would then describe a range that does not exist. A
discontinuous total is worse than a dated one. **This is a judgement call and a
human should confirm it.**

The lesson for the schedule: the pixel series can only be kept alive by a
refresh that actually lands inside the 28-day window. Every run from here should
append the days since the last `PIXEL` row while they are still reachable.

## Figures that are not what their names suggest

- **`DAILY_ALL[i][5]` is link clicks, not total clicks.** The column is headed
  "Link clicks" and must be filled from `link_click`. `clicks` is larger (482
  against 349 on 8 Sep) and using it silently inflates the column.
- **Two campaign names carry the wrong daily budget.** `Air Light · ...
  ₹1000/day` has a ₹700 daily budget at Meta, and `Dream · No AI Bat Test ·
  ... ₹700/day` has ₹1,000. The names were swapped when the campaigns were
  built. `CAMPS_ALL[i][4]` is filled from the API value, not the name. Worth
  correcting at Meta.
- **`PLACE_DRIFT` was published with the wrong sign** (−0.01 where placement
  spend is ₹0.01 *above* the day rows). The page prints `Math.abs`, so nothing
  rendered wrongly, but the constant is now +0.01.

## A live part-day moves while you read it

The Air Light Copy campaign was read three times within a few minutes on 13 Nov
and returned ₹11.31, then ₹12.62, then ₹12.89. Its day row is filled from the
ad-level read — the last and finest grain — so the campaign reconciles against
its own ads. The account-level daily read for 13 Nov, taken first, says ₹71.34
where the campaign rows total ₹72.92. **Both numbers are right for the moment
they were taken.** This is exactly why `SNAP_DAY` is held out of every headline:
nothing on this page is allowed to rest on a day that is still counting.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** All nine
campaigns on account `1811409506889048` are now first-class: `CAMPS_ALL`,
`DAILY_ALL` (a row per campaign per day) and `ADS_ALL` (a row per ad) drive a new
account section at the top, and every campaign gets its own creative table.

**Leads and purchases are never blended.** The account sells four different
things — quiz leads, ₹199 clinic sessions, Dream bats and Air Light bats — so
each group is priced in its own currency and there is deliberately no single account-wide "cost per
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
