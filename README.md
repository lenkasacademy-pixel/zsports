# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 7 Nov 2026: the account spending limit was lifted and delivery is
back.** The stop that held the account dark from 26 Sep is over - the first day
with delivery again was **1 October**, and the account has spent on 36 of the 61
days in the window. `STOP_REASON` is now `None` and the page's stop note has
cleared itself, as it is built to.

Lifetime account spend is **Rs 36,557.98** across 61 days and **8 campaigns**
(up from Rs 14,406.77 and 6 campaigns at the last snapshot - the account has
spent **Rs 22,151.21 more**, two and a half times everything before it).

**Two new campaigns, and one of them is now the whole account.**

| Campaign | Status | Ran | Spend | Result | Each |
|---|---|---|---|---|---|
| `Air Light - Purchase - India - Rs 1000/day - Oct 2026` | **ACTIVE** | 21 days, 14 Oct-7 Nov | Rs 14,868.80 | 21 purchases | **Rs 708.04** |
| `Dream - No AI Bat Test - Purchase - Rs 700/day - Oct 2026` | PAUSED | 4 days, 28-31 Oct | Rs 1,878.81 | 1 purchase | Rs 1,878.81 |

**Air Light is the only ACTIVE campaign and the only one serving.** It is
carrying Rs 1,200/day, it spent on the part-day, and at Rs 14,868.80 it is now
the largest line the account has ever run - bigger than the whole quiz. Within
it, `Air Light - Hook1` has taken Rs 11,419.66 for 16 purchases (Rs 713.73) and
`Air Light - Hook3` only Rs 1,947.72 for 4 (**Rs 486.93**, the account's best
cost per sale). Hooks 2, 4 and 5 are paused on Rs 1,501.42 between them for one
sale. Worth considering more budget behind Hook3.

**The clinic came back, spent, and stopped again.** `Z-Bat Clinic - Parel - Rs 199
Session Sales` ran 1 Oct and 9-12 Oct and closed at **Rs 9,298.21 for 11 sessions
(Rs 845.29)**, against Rs 6,377.06 and 8 at the last cut. Eight new
`Reel ... v2` creatives were added to it and took Rs 2,559.07 between them for
2 purchases; the original `Video 4 - Copy 3 Local offer` still carries the
campaign at Rs 6,739.14 for 9. It is now PAUSED. Its sibling
`... Sep 2026 - Copy`, which had never spent, finally ran on 2-3 Nov for
**Rs 837.52 with no purchase**.

**Dream Bat has its first sale.** The "still no purchase" flag from the last
snapshot is resolved: `Dream Bat - Direct Sales` ran again 1 Oct and 11-14 Oct
and closed at **Rs 2,981.94 for 1 purchase**, 72 adds to cart and 24 site leads.
One sale on nearly three thousand rupees is still the cart problem, not a fix -
and `Dream - No AI Bat Test` repeated it almost exactly: Rs 1,878.81, 45 adds to
cart, 12 leads, 1 sale. **Two separate Dream campaigns have now spent
Rs 4,860.75 for two sales between them.** That is the finding to put in front of
the client.

**The quiz has not moved at all - nine refreshes now.** All three quiz campaigns
read PAUSED and none has spent since 21 September: still exactly **Rs 6,692.70
and 1,019 leads**, to the paisa, with no late-attributed lead. The page works out
on its own that the quiz has been dark for 47 days and says so. Because the quiz
totals are frozen, `DAILY`, `REELS`, `PLACE` and `AGE` are unchanged except for
zero rows extending `DAILY` to the new `SNAP_DAY`; their checks still pass.

**Non-quiz spend is now Rs 29,865.28 against the quiz's Rs 6,692.70** - the quiz
is 18% of the account. This report's quiz sections describe a smaller and smaller
share of what the account does, which is exactly why the account view at the top
exists.

**The part-day guard is doing its job, and we watched it work.** Reading 7 Nov
three times within minutes gave Rs 168.82, then Rs 174.29, then Rs 178.81 at
account level - the day moved by **Rs 9.99 while we looked at it**. It is the
last `DAILY` row, so the page shows it separately as "so far" and keeps it out of
every headline.

**The store pixel is busy and the shop is selling.** Over the 28 days the API
still reaches (10 Oct-6 Nov, in the dataset's own -07:00/-08:00 day buckets):
29,854 page views, 17,278 view-contents, 1,491 adds to cart, 511 checkouts and
**104 purchases**, plus 423 quiz starts and 183 leads with no quiz ad spend
behind them at all. See the `PIXEL` note below for why none of this is on the
page yet.

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

**`PIXEL` still stops at 29 Sep, deliberately — read this before "fixing" it.**
The 28-day lookback is hard: on the 7 Nov 2026 refresh the earliest reachable
bucket was 10 Oct, so **30 Sep – 9 Oct can never be recovered**. Appending the
reachable days would put a permanent ten-day hole in the middle of the series
*and* blend two different regimes into the funnel's Period row — the quiz ran ads
in September and has run none since 21 Sep, so "page views → quiz starts → leads"
would stop describing anything. `PIXEL` therefore holds the September quiz
window, which is the period the quiz funnel section is about, and the recent
store numbers are quoted in the status notes above instead.

If the client wants the shop's current traffic on the page, that is a **second,
separately-labelled pixel window**, not an extension of this array — and it needs
pulling at `granularity: "hour"` to rebuild IST days under the convention above.
**Pull it monthly from now on**, or the gap grows every time.
