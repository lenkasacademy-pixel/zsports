# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 24 Nov 2026: delivery resumed, and the account looks nothing like
it did in September.** The account spending limit that held it dark from 26 Sep no
longer binds — spend restarted on 1 Oct and has run almost every day since. Lifetime
spend is now **₹61,119.97** across 78 days (8 Sep – 24 Nov), up from ₹14,406.77 at
the last refresh. `STOP_REASON` is back to `null`; the page works the stop out for
itself from `CAMPS_ALL` status and `DAILY_ALL`, so it cleared its own note.

**The account has pivoted to selling the Air Light bat.** Five campaigns that did
not exist in September now carry most of the spend:

| Campaign | Spent | Purchases | Each | Status |
|---|---|---|---|---|
| Air Light · Purchase (Oct) | ₹21,613.12 | 31 | ₹697.20 | PAUSED |
| Air Light · Purchase (Copy) | ₹10,476.87 | 29 | ₹361.27 | PAUSED |
| Air Light · Purchase (Copy 2) | ₹6,061.92 | 13 | ₹466.30 | **ACTIVE** |
| Dream · No AI Bat Test | ₹2,948.04 | 1 | ₹2,948.04 | PAUSED |
| Air Light · Bid Cap Test ₹340/₹320 | ₹209.65 | 2 | ₹104.83 | PAUSED |

Air Light is now **₹38,361.56 for 75 purchases, ₹511.49 each** — 63% of everything
the account has ever spent, and the only line with purchase volume behind it. The
copies are getting cheaper: ₹697 → ₹361 → ₹466 per purchase. `Air Light - Hook1`
is the workhorse in every one of them; `Hook2`, `Hook4` and `Hook5` were tried in
the first campaign and have never been un-paused since.

A fourth product group, **`air`**, was added to `GROUPS` for these. The account now
sells **four** different things, so there are four cost-per-result lines and still
no blended one.

**The quiz — the thing this report is named after — is finished.** All three quiz
campaigns are PAUSED and none has spent since **21 September**: nine refreshes now
at exactly ₹6,692.70 and 1,019 leads, with not even a late-attributed lead moving.
The page says so in its own words ("dark for 64 days"), computed from the data —
do not hand-write that note. `DAILY` is padded with explicit zero rows out to
`SNAP_DAY` so the dark run is visible as data rather than as a missing tail, and
so the last row is still `SNAP_DAY`.

**The clinic and Dream Bat both restarted briefly and stopped again.** The clinic
ran 1 Oct and 9–12 Oct (₹2,921.15 more) and closes at ₹9,298.21 for 11 purchases,
₹845.29 each; its copy finally delivered on 2–3 Nov (₹837.52, no purchases).
Dream Bat added 1 Oct and 11–14 Oct and closes at ₹2,981.94 for its single
purchase. Nine new clinic creatives (`Reel 1/2/3 Hook A/B/C v2`) appeared and
between them took ₹2,559.07 for 2 purchases, against ₹6,739.14 and 9 for the
original `Video 4 · Copy 3 Local offer`.

**A late-attributed purchase landed on a settled September day.** The clinic's
25 Sep row moved from 0 purchases to 1 *after* the 29 Sep snapshot was published.
Nothing else in September moved — every other day and campaign reconciles to the
paisa against the figures already in the file. This is why the refresh re-pulls
old days instead of appending: a result can arrive on a day that looks closed.

**Worth knowing:** the store pixel recorded **212 Purchase events in the last 28
days** (27 Oct – 23 Nov) against 75 purchases Meta attributes to ads over the
account's whole life. The shop sells a good deal more than the ads are credited
with, which is the same point the funnel note already makes — these counts cover
all site traffic, not only visitors from ads.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** Every
campaign on account `1811409506889048` is now first-class (11 of them as of
24 Nov 2026): `CAMPS_ALL`,
`DAILY_ALL` (a row per campaign per day) and `ADS_ALL` (a row per ad) drive a new
account section at the top, and every campaign gets its own creative table.

**Leads and purchases are never blended.** The account sells four different
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

**The 28-day cap means `PIXEL` cannot be a continuous series any more.** It still
holds the quiz window (8–29 Sep), which is what the quiz funnel is about, and that
history can never be re-fetched — 30 Sep to 26 Oct is permanently out of reach.
Appending the current 28-day window to the same array would put a 27-day hole in a
table whose rows read as consecutive days, and would make its `Period` row the sum
of two disjoint windows. So `PIXEL` is deliberately left on the quiz window; every
row carries its own date, so the table shows what it covers. If the client wants
current store-funnel numbers, that is a second table with its own period label, not
more rows on this one.
