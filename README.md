# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 7 Oct 2026: the spending-limit stop is over, the account served
one day, and then somebody paused everything.** The sequence, from the activity log:

- **1 Oct, 2:16 pm** — **Rs 10,000 added to the account balance**. That cleared the
  `account_spend_limit_reached` condition that had held the account dark since 26 Sep.
- **1 Oct** — delivery resumed: **Rs 614.83**, the Clinic on Rs 346.45 and Dream Bat
  on Rs 268.38. First spend in six days.
- **1 Oct, 8:18 pm** — **Abhijeet Lenka set both live campaigns to Inactive** (Power
  Editor): `Z-Bat Clinic - Parel - Rs 199 Session Sales` and
  `Dream Bat - Direct Sales`. Nothing has served since.
- Billing followed on 2 Oct (Rs 611.92) and 3 Oct (Rs 2.91).

**All six campaigns now read PAUSED**, so this is a deliberate pause, not a delivery
or billing fault. **`STOP_REASON` is now `None`** and the page's delivery-stop note
has cleared itself, exactly as designed — there are no ACTIVE campaigns for it to
describe. Do not hand-write a replacement; if a campaign is switched back on and
fails to serve, the note will reappear on its own.

**Lifetime account spend is now Rs 15,021.60** (was Rs 14,406.77).

**The clinic found a ninth sale.** It closes at Rs 6,723.51 for **9 purchases,
Rs 747.06 each**. The ninth was attributed back to **25 September**, a day already
published with zero — late attribution, not new delivery, so that `DAILY_ALL` row
was corrected rather than appended to.

**Dream Bat still has not sold anything.** Rs 1,605.39 over four days: 252 landing
page views, 37 adds to cart, 15 site leads, **no purchase**. Its 1 Oct money went
entirely to `Dream - Carousel - 3 angles`, which went from Rs 36.35 to Rs 304.73.
That is the quiz funnel's old cart problem showing up a third time on a campaign
that never touches the quiz. **Worth deciding before it spends again.**

**Watch out: Dream Bat's campaign name lies about its budget.** Meta reports the
name as `Dream Bat - Direct Sales - India - Rs 600/day - Sep 2026` while its actual
`daily_budget` is **Rs 500**. `CAMPS_ALL[5][4]` carries the real 500. Someone
renamed it without changing the budget, or changed the budget without renaming it.

**All three quiz campaigns are still PAUSED and have not spent since 21 September**
— **nine refreshes now** at exactly Rs 6,692.70 and 1,019 leads, with not even a
late-attributed lead moving. They also read PAUSED on 20 and 21 September while
spending, so do not assume a PAUSED status means a campaign is finished - always
re-pull the days. The page works all of this out from `CAMPS` status and the
`DAILY` rows and says it in its own words - do not hand-write a note about it.

Non-quiz spend on this account is now Rs 8,328.90 against the quiz's Rs 6,692.70.
When the quiz is dark, this report describes a smaller and smaller share of what the
account is doing - worth saying to the client rather than letting the page look idle.

**The shop keeps selling with no advertising behind it.** The store pixel recorded
**18 raw Purchase events across 1-6 October** (9 on the 2nd, 3 on the 4th, 6 on the
6th) while ad spend after 1 October was zero. The shop is fine; it is the advertising
that has stopped.

**Caveat on the store pixel's history.** Re-pulling IST 27 September now returns
PageView 396, ViewContent 93, AddToCart 18, InitiateCheckout 21, Purchase 9 against
the **448 / 116 / 15 / 18 / 7** published on 29 Sep. The low-volume clinic events
reproduce exactly (`ClinicBookOpened` 10, `ClinicSlotPicked` 10, `Schedule` 6), so
the -07:00 bucketing convention is still right - but Meta keeps re-deduplicating the
high-volume browser events, so **older `PIXEL` rows are not reproducible**. This
refresh therefore **left every published row before 29 Sep untouched** and rewrote
only the 29 Sep part-day and the days after it. Do the same next time rather than
silently rewriting figures the client has already seen.

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
