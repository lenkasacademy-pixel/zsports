# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 25 October 2026: delivery has resumed, and the account looks nothing
like it did a month ago.** The account spending limit that stopped everything on
26 September has been lifted — the first spend after the stop landed on 1 Oct, and
the account has run on most days since. `STOP_REASON` is back to `None` and the page
clears its own stop notice. Lifetime spend is now **₹26,996.59** across 48 days, up
from ₹14,406.77.

**A seventh campaign has appeared: `Air Light · Purchase · India · ₹1000/day · Oct 2026`**
(`120253356448770067`), started 14 Oct and now the only ACTIVE campaign on the account.
It is at **₹8,023.74 for 12 purchases, ₹668.65 each** — more sales than every other
campaign on the account put together, in 12 days. Five hooks run under it and one does
nearly all the work: `Air Light - Hook1` has taken ₹5,888.26 of the ₹8,023.74 and 9 of
the 12 sales, while `Hook2` has spent ₹167 across twelve days for nothing at all. It
sells bats direct, like Dream Bat, but it is a different product, so it gets its **own
group** rather than being folded into the Dream Bat line — blending them would price
two products as one.

**The clinic finished strongly and then stopped.** ₹9,298.21 and **11 ₹199 sessions**
(₹845.29 each, against 8 sessions on ₹6,377.06 at the last snapshot). Nine new `v2`
reel creatives were added on 9 Oct; between them they took ₹2,559.07 for 2 sales, so
the original `Video 4 · Copy 3 Local offer` is still carrying the campaign at 9 sales
on ₹6,739.14. It last spent on 12 Oct and now reads PAUSED.

**Dream Bat finally converted — once — and is also paused.** ₹2,981.94 over 8 spending
days for **1 purchase**, 72 adds to cart and 24 site leads. The cart problem flagged
last time is still the story: 72 carts produced one sale. It last spent on 14 Oct.

**All three quiz campaigns remain PAUSED and have not spent since 21 September** — the
ninth refresh running at exactly ₹6,692.70 and 1,019 leads, with not one late-attributed
lead. They have now been dark for **34 days**. `DAILY`, `REELS`, `PLACE` and `AGE` are
identical to the last snapshot apart from the zero rows carried forward to `SNAP_DAY`;
every reconciliation check still passes on them. The page works this out from `CAMPS`
status and the `DAILY` rows and says it in its own words — do not hand-write a note
about it.

**The quiz now describes a small and shrinking share of the account.** Non-quiz spend
is ₹20,303.89 against the quiz's ₹6,692.70 — three times as much. Worth saying to the
client rather than letting the quiz sections look like the whole report.

**One presentation point for a human.** Because `DAILY` must carry one row per day up
to `SNAP_DAY`, the quiz day-by-day chart now prints 34 consecutive ₹0.00 bars. That is
the invariant doing its job, and the surrounding prose explains it, but if the quiz
stays dark much longer the chart is worth collapsing — a design decision, not a
refresh one.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** Every
campaign on account `1811409506889048` is now first-class: `CAMPS_ALL`,
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
| `CAMPS_ALL` | `[id, name, group, status, daily budget, result kind, reach]`, **append only** |
| `DAILY_ALL` | `[campIdx, date, spend, impressions, reach, link clicks, lpv, cart adds, purchases, leads]` |
| `ADS_ALL` | `[name, campIdx, status, spend, impressions, reach, link clicks, lpv, cart adds, purchases, leads]` |
| `ALL_DAYS` | the shared calendar, first day to `SNAP_DAY` |
| `ACCOUNT_SPEND` | must equal the sum of `DAILY_ALL` spend |

**`CAMPS_ALL` order is an index, not a list.** `DAILY_ALL` and `ADS_ALL` both reference
it by position, so a new campaign is **appended** and nothing is ever reordered — the
same rule as `CID` in `elixir-reports`. A new product line also needs an entry in the
`GROUPS` array in the page script; Air Light got its own rather than being folded into
Dream Bat, because each line has to be priced in its own currency.

**Omit ARCHIVED ads that never spent**, and where Meta returns two ads with the same
name under one campaign (it does for the quiz ATC campaign and for Dream Bat), keep the
one that spent. Everything else, including paused and zero-spend ads, is listed.

**A live campaign's day rows and ad rows will not agree if you pull them separately.**
Meta revises the part-day between calls — on this refresh Air Light's `SNAP_DAY` moved
₹0.90 between a campaign-level call and an ad-level call minutes later. Build both the
day rows and the ad totals for a still-delivering campaign from **one** ad-level daily
pull so they agree by construction, then reconcile the settled days against Meta's own
campaign and account totals.

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

**The 28-day lookback is a hard wall, so older rows can only be carried forward, never
re-derived.** On the 25 Oct refresh the window reached back to IST 28 Sep, and that
day's re-derived row came back **identical to the published one**
(`352, 80, 8, 6, 15, 8, 5, 0, 12, 10, 10`) — that is the check that the −07:00
bucketing is still right. 8–27 Sep are carried from the previous snapshot unchanged.
29 Sep was a part-day when it was last written and has now been rebuilt in full: it
went from 3 to **239 AddToCart events** on 350 page views, far out of line with every
day either side. Nobody has explained that spike; it is left in the data as it stands.

**The store keeps selling with no ads behind it.** Right through the dark stretch the
pixel went on recording Purchase events with zero ad spend against them, and it still
runs ahead of the Meta count now that delivery is back. The page already says these
counts cover all site traffic, not only visitors from ads.
