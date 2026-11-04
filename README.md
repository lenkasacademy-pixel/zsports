# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 4 Nov 2026: the account is serving again, and the quiz is still frozen.**
The account spending limit that stopped everything on 26 September has been lifted.
Lifetime spend has gone from ₹14,406.77 to **₹33,866.37**, and `STOP_REASON` is back to
`None` — the page's stop note computes itself from `CAMPS_ALL` and `DAILY_ALL`, so it
cleared on its own with no prose to remove.

**Two campaigns that did not exist at the last snapshot now carry most of the account:**

| campaign | ran | spend | sales | each | status |
|---|---|---|---|---|---|
| `Air Light · Purchase · India · ₹1000/day · Oct 2026` | 14 Oct – 4 Nov | ₹12,177.39 | 18 | **₹676.52** | ACTIVE |
| `Dream · No AI Bat Test · Purchase · ₹700/day · Oct 2026` | 28–31 Oct | ₹1,878.81 | 1 | ₹1,878.81 | PAUSED |

**Air Light is the best cost per sale this account has produced** — ₹676.52 against the
clinic's ₹845.29 and Dream Bat's ₹2,981.94 — and it is the only ACTIVE campaign. One ad
carries it: `Air Light - Hook1`, ₹8,973.85 for 13 of the 18 sales. It is also the only
line with a live part-day, which is why 4 November is held out of every total.

**Dream Bat finally has a sale.** The old note said ₹1,337.01 and *"still no purchase"*;
it closes at **₹2,981.94 for 1 purchase**, 24 site leads and 72 adds to cart. One sale on
₹2,982 is not a solved funnel, but the cart problem flagged last time is no longer a flat
zero.

**The clinic is paused and has nine new creatives behind it.** It went from ₹6,377.06 and
8 purchases to **₹9,298.21 and 11**, ₹845.29 each, and nine `… v2` reel ads were added
(Reel 1/2/3 × Hook A/B/C). The best of them, `Reel 1 Hook C v2`, took ₹1,131.14 for one
sale; the original `Video 4 · Copy 3 Local offer` still carries 9 of the 11.

> **A hand-written sentence went stale, and it is now computed.** The account note said
> *"`Clinic · Parel ₹199 (Copy)` was built and never switched on"*. It has since spent
> **₹837.32** with no sale, no add to cart and no lead. That sentence named campaigns by
> index (`CAMPS_ALL[4]`, `CAMPS_ALL[5]`), which is exactly the thing this report is built
> not to do. It now derives the list: any campaign with spend and no result of its own
> kind is a deliberate blank, and any campaign with no spend at all is reported as never
> switched on. `ACCT_RES_KINDS` and the campaign count in the intro (hard-coded as
> *"Six"*) were fixed the same way.

**All three quiz campaigns are still frozen at exactly ₹6,692.70 and 1,019 leads** — the
ninth refresh running, and now **44 days** since the last day the quiz spent (21 Sep).
Not a single late-attributed lead has moved. `REELS`, `PLACE` and `AGE` are therefore
byte-identical to the previous snapshot; only `DAILY` grew, with zero rows out to the new
`SNAP_DAY`. Three zero-spend duplicate ads have appeared inside the Add-to-Cart campaign
and are carried as "never ran", per the convention that every ad is listed.

**Non-quiz spend is now ₹27,173.67 — 80% of the account.** When this report was built the
quiz was the account; it is now a fifth of it. The account section at the top carries the
rest, but the placement, age and pixel breakdowns below it remain quiz-only, so four
fifths of the spending has no creative-level detail on this page. Worth saying to the
client rather than letting the quiz sections look like the whole story.

**The store pixel has a hole that cannot be filled.** Meta keeps dataset event stats for
28 days only, so **30 September to 7 October is gone for good** — the previous snapshot
ended on 29 September and this one could not reach further back than the 8th. `PIXEL_GAP`
carries the two dates and the page states the hole itself, so it reads as absent rather
than as zero traffic.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** **Every**
campaign on account `1811409506889048` is first-class — eight of them as of
4 Nov 2026: `CAMPS_ALL`, `DAILY_ALL` (a row per campaign per day) and `ADS_ALL`
(a row per ad) drive the account section at the top, and every campaign gets its own
creative table. **Append new campaigns to the end of `CAMPS_ALL`** — `DAILY_ALL[0]`
and `ADS_ALL[1]` index into it, so reordering it re-labels every historical row.

**Leads and purchases are never blended.** The account sells three different
things — quiz leads, ₹199 clinic sessions, and bats — so each group is priced in
its own currency and there is deliberately no single account-wide "cost per
result". `CAMPS_ALL[i][5]` says which result kind a campaign reports, and
`CAMPS_ALL[i][2]` which `GROUPS` entry it belongs to.

**A new product line needs a new `GROUPS` entry.** Air Light got one on 4 Nov so it
is priced separately from Dream Bat. Both *sell* the same thing, so the "they sell N
different things" count reads `GROUPS[].sells` distinctly rather than counting groups
— otherwise adding a group silently inflates that sentence.

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
| `PIXEL_GAP` | `[first, last]` day the 28-day pixel window could not reach, or `[]` |

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

Pull with `ads_get_dataset_stats`, unix `start_time`/`end_time`, one call per IST
day; max lookback 28 days. Events are stamped **−07:00**, and an IST day D is the 24
buckets from (D−1) 12:00 through D 11:00 — i.e. `start = (D−1) 19:00 UTC`,
`end = D 18:59:59 UTC`. **That unix window stays correct across the US DST change on
1 November**; the response simply reports it as `11:00-0800` instead of `12:00-0700`.
These counts cover all site traffic, not only visitors from ads, so they run ahead of
the Meta lead count — the page says so.

- **Use `aggregation: "event_total_counts"`, not `"event"`.** Both give the same day
  totals, but `event` returns 24 hourly buckets per day and `event_total_counts` returns
  one block — about a tenth of the output for 28 days of pulls. It splits each event name
  across rows (web and server), so **sum the duplicates**: a day with
  `PageView 209, PageView 89, PageView 44, PageView 26` is 368 page views. Verified
  against the hourly figures on 8 October for all seven events present.
- **The 28-day window is a hard edge, and the gap it leaves is permanent.** If the last
  snapshot is more than 28 days old, the days in between can never be pulled. Put them in
  `PIXEL_GAP` as `[first, last]` and the page reports the hole; leave `PIXEL_GAP` empty
  when there isn't one. Do **not** write zero rows for days you could not read — the
  funnel would then show real zero traffic that never happened.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume. Across the days now on the page they stand at
343 bookings opened and 121 slots scheduled.
