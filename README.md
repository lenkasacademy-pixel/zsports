# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat &mdash; every Meta campaign | from 8 Sep 2026, rolling (now to 13 Oct) | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 13 Oct 2026: delivery has resumed. The account spending limit is no
longer biting.** The account was dark 26-30 Sep; it served again on 1 Oct, went
quiet 2-8 Oct, and has run every day from 9 Oct to today. Lifetime spend is now
&#8377;18,641.67 (was &#8377;14,406.77). `STOP_REASON` is set back to `None` and the
page's stop note has cleared itself from the data, exactly as it is meant to.

**The clinic campaign is now PAUSED and the Dream Bat is the only ACTIVE campaign.**
That is a reversal of the 29 Sep picture. Before it stopped, the clinic took
&#8377;9,298.13 for **11 &#8377;199 sessions** (&#8377;845.28 each, up from 8 sessions at
&#8377;797.13) and picked up **nine new "Reel N Hook X v2" creatives** that between them
spent &#8377;2,559.07 for 2 purchases - the original `Video 4 · Copy 3 Local offer`
still carries 9 of the 11 at &#8377;748.78 each. Its two old `Copy 1 Measured` and
`Copy 2 Stop guessing` ads are now ARCHIVED; they never spent and are kept as zero
rows rather than dropped.

**Dream Bat is now the thing to watch, and the cart problem has not gone away.**
&#8377;2,650.84 over 7 spending days: 414 landing page views, 58 adds to cart, 21 site
leads, **still no purchase at all**. It was &#8377;1,337.01 with no purchase at the last
cut, so it has doubled its spend without converting once. `Dream · Value · front`
is the biggest line (&#8377;1,546.84, 48 of the 58 cart adds, no sale). This is the
quiz funnel's old cart problem on a campaign that does not touch the quiz, and it
is now the largest open question on the account.

**All three quiz campaigns are still PAUSED and have not spent since 21 September** -
eleven refreshes at exactly &#8377;6,692.70 and 1,019 leads, and the re-pull reproduces
every quiz day, creative, placement and age row to the paisa. They also read PAUSED
on 20 and 21 September while spending, so do not assume a PAUSED status means a
campaign is finished - always re-pull the days, and re-pull the days either side of
the last snapshot too. The page works all of this out from `CAMPS` status and the
`DAILY` rows and says it in its own words - do not hand-write a note about it.

Non-quiz spend on this account is now &#8377;11,948.97 against the quiz's
&#8377;6,692.70 - nearly twice it. When the quiz is dark, this report describes a
smaller and smaller share of what the account is doing; worth saying to the client
rather than letting the page look idle.

**Store pixel: the overlap days moved.** The 28-day lookback means 8-15 Sep cannot be
re-pulled and those `PIXEL` rows are carried forward as published. Everything from
16 Sep was re-pulled, and the IST bucketing convention reproduces several days to the
unit (16 Sep 1,472/604/77/15 and 21 Sep 1,701/646/93/21 are exact) while others have
grown - 20 Sep went 1,504 to 1,748 page views and 74 to 92 leads, 24 Sep 1,213 to
1,243, and Purchase moved on 17, 21, 25 and 27 Sep. That is Meta revising its own
dataset stats after the fact, not a bucketing error; the convention is unchanged.
The shop is still selling with no ad spend behind it.

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

**Only the last 28 days can be re-pulled.** Rows older than that are carried forward
from the previous build - do not drop them and do not try to re-fetch them. The event
is named `BatFitStarted`, not `BatFitStart`; getting that wrong silently zeroes the
`BFS` column. A `Search` event also fires in small numbers and is deliberately not in
`EV`.
