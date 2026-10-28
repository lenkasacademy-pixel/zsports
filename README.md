# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | 8 Sep – 28 Oct 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

## Status as of 28 Oct 2026: the account spent again, then stopped on purpose

**The spending-limit stop cleared itself.** Meta's activity log records
**₹10,000 added to the account balance on 1 Oct at 2:16 pm** (and another
₹10,000 on 19 Oct at 1:55 pm). Delivery was possible again from that moment, so
the `account_spend_limit_reached` reason no longer applies and `STOP_REASON` is
back to `null`.

What happened after is a sequence of hand pauses, not a billing fault — every
one of them in the activity log under actor Abhijeet Lenka:

| when | what |
|---|---|
| 1 Oct, 8:18 pm | Clinic **and** Dream Bat paused, so 1 Oct took ₹614.83 and 2–8 Oct were dark |
| 9 Oct, 10:35 am | **Nine new clinic creatives** (`Clinic ₹199 · Reel N Hook X v2`), clinic daily budget ₹600 → ₹500, campaign switched back on; the two old `Video 4` copies deleted at 10:40 am |
| 11 Oct, 12:37 pm | Dream Bat switched back on |
| 12 Oct, 7:28 pm | Clinic paused again |
| 14 Oct, 9:48 am | **A new campaign: `Air Light · Purchase · India · ₹1000/day · Oct 2026`**, with five ads `Air Light - Hook1` … `Hook5` |
| 14 Oct, 10:04 am | Dream Bat paused |
| 27 Oct, 12:41 pm | **Air Light paused** — and with it the whole account |

So **every campaign on the account now reads PAUSED** and 28 Oct has taken
nothing. The page works this out for itself: with no ACTIVE campaign the
delivery-stop note stays silent, because an account where nothing is switched on
is not a stop that needs explaining.

### Air Light is the first thing on this account that sells at volume

`Air Light` ran **14–27 Oct for ₹9,776.23 and 14 purchases — ₹698.30 each**, the
best cost per sale the account has recorded. `Air Light - Hook1` carries most of
it: ₹7,243.08 for 9 purchases at ₹804.79, with `Hook3` second at ₹289.79 on four.
Note the budget stepped ₹1,000 → ₹2,000 → ₹1,500 on 14 Oct and now reads
**₹700/day**, so the “₹1000/day” in the campaign's own name is already stale —
`CAMPS_ALL[i][4]` carries the live API value, never the name.

**The clinic is at ₹9,298.21 for 11 sessions (₹845.29 each)** after four more
days and nine new creatives; `Video 4 · Copy 3 Local offer` is still the best of
them at ₹748.79.

**Dream Bat has finally recorded one sale** — ₹2,981.94 for a single purchase,
72 adds to cart and 24 site leads. The cart problem the last snapshot flagged is
better but not fixed, and ₹2,982 a sale is not a rate to scale.

### The quiz is frozen and that is now the story

The three quiz campaigns have not spent since **21 September**. Nine refreshes on,
they stand at exactly **₹6,692.70 and 1,019 leads** — and this time the
placement and age breakdowns came back **byte-identical** to the published file
as well, not merely equal in total. 37 full days of the quiz day table are zero
rows. That is deliberate: the dead run is visible as data, and the page says in
its own words that every quiz campaign is paused at Meta and this is a decision,
not a delivery problem.

Non-quiz spend is now **₹22,056.38** against the quiz's ₹6,692.70 — the quiz is
less than a quarter of what this account does. Worth saying to the client rather
than letting the quiz sections look like the whole report.

**Worth knowing while the ads are off:** the store pixel still records sales with
no ad spend behind them. The shop is still selling; it is the advertising that is
switched off.

### A zero-spend day can still carry a result

Dream Bat's day rows came out **2 page views short** of both its lifetime total
and its own ad rows. The cause: **2 Oct carries 2 late-attributed page views on
zero spend and zero impressions**, and the first build dropped it as an empty
day. A day row is now kept whenever *any* column on it is non-zero, not only when
it spent or served. This is the same lesson `halcyon-reports` records for a
late-attributed call — and again it was the reconciliation that found it, not
inspection.

### The store pixel cannot reach 30 September any more

`ads_get_dataset_stats` has a hard **28-day lookback**, and an IST day D needs
the buckets from (D−1) 12:00 in −7:00 stamps. On 28 Oct that puts the earliest
reachable day at **1 Oct**, so **`PIXEL` has no row for 30 September** and never
will. The published rows to 29 Sep are carried forward untouched; the gap is one
day and it shows as a jump in the funnel table. The lesson for the schedule: this
report has to be refreshed at least every 28 days or it loses pixel days
permanently.

### Campaigns and ads not in the report

`OLD · DO NOT USE · Z-Bat Clinic Leads (replaced by ₹199 Session Sales)`
(`120252806446910067`) is ARCHIVED on the account and has **never delivered**, so
it is left out of `CAMPS_ALL` — the same rule `elixir-reports` uses. Add it the
day it spends, **appended to the end** so no historical row re-labels itself.

Pulling ads with an explicit `effective_status` filter (which this report must
do, or archived ads vanish) also returns **re-created duplicates**: a zero-spend
ad carrying the same name as a spending ad in the same campaign. Five of those
were dropped — two Dream Bat ads and all three quiz ATC ads — because two rows
with one name in one table is a reporting error, not completeness. Never-delivered
ads with a *unique* name are still listed, which is what the page's own note
promises.

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

- Every campaign's day rows equal its `date_preset: "maximum"` lifetime totals on
  spend, impressions, link clicks, page views, cart adds, purchases and leads.
- Every campaign's day rows equal its ad rows on the same seven columns.
- **Per day, the campaign rows sum to the `level: "ad_account"` daily row on
  spend *and* impressions**, and the 51 day labels match `ALL_DAYS` exactly.
- `REELS` totals equal `DAILY` totals on spend, impressions **and** leads.
- `AGE` totals equal `DAILY` totals on spend and leads.
- `PLACE` matches `DAILY` exactly on leads; a few rupees of spend drift is
  Meta's own breakdown rounding and is reported on the page (`PLACE_DRIFT`,
  written as `DAILY` minus `PLACE`, so it reads −0.01 while the breakdown is one
  paisa over).
- The last `DAILY` row is `SNAP_DAY`, and no `(name, campaign)` pair appears
  twice in `ADS_ALL`.
- Every pixel event name maps to an `EV` code — an unmapped one is a build
  failure, not a silent drop.

Compare spend in **integer paise**, not floats: summing rounded rupee values two
different ways flipped the sign of `PLACE_DRIFT` on this refresh.

### Nothing in the page should name a campaign by index

`index.html` had **“six campaigns” written into four places** and the result-column
note named `CAMPS_ALL[4]` and `CAMPS_ALL[5]` directly. Adding Air Light made all
five wrong, and the note would have rendered “… and Dream Bat has .” the moment
Dream Bat recorded its first sale. All five now derive from the arrays:
`CAMPS_ALL.length` for the counts, and the blanks note works out for itself which
campaigns never delivered and which spent without a result. A new campaign is
**appended** to `CAMPS_ALL` so existing indexes keep their meaning, and it gets
its own `GROUPS` entry so its results are never blended with another product's.

### Diff the ad names every refresh

`ads_account_get_activity_logs` with `event_category: "ad"` over 25 Sep – 28 Oct
returns **44 events and not one `Ad name updated`**, so the 14 new names
(9 clinic + 5 Air Light) are all `Ad created` and nothing was renamed in place.
Check this every time: a rename at Meta can hide a creative swap, and no total
would move if you carried the history forward under the wrong name.

### Store pixel

Pull with `ads_get_dataset_stats`, `aggregation: "event"`, unix
`start_time`/`end_time`; max lookback 28 days. Events are stamped **−07:00**, and
an IST day D is the 24 buckets from (D−1) 12:00 through D 11:00. These counts
cover all site traffic, not only visitors from ads, so they run ahead of the Meta
lead count — the page says so.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.
