# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 22 Nov 2026: the account is running again, and it is now an Air Light
bat account.** The account spending limit that stopped delivery on 26 Sep was lifted:
spend resumed on 1 Oct and has run almost every day since. Lifetime spend is
**₹57,401.61** across 8 Sep - 22 Nov, up from ₹14,406.77 at the last snapshot.
`STOP_REASON` is back to `None` and the page's stop note has cleared itself.

**Five new campaigns have appeared, and they are the account now.** All five sell the
**Air Light** bat or test the Dream Bat without the AI angle:

| Campaign | Ran | Spend | Purchases | Each |
|---|---|---|---|---|
| Air Light - Purchase - Oct | 14 Oct - 13 Nov | ₹21,613.12 | 31 | ₹697.20 |
| Air Light - Purchase (Copy) | 13 - 20 Nov | ₹10,476.40 | 29 | **₹361.26** |
| Air Light - Purchase (Copy 2) | 21 - 22 Nov, LIVE | ₹2,353.96 | 6 | ₹392.33 |
| Air Light - Bid Cap ₹340/₹320 | 20 - 22 Nov, LIVE | ₹199.72 | 1 | ₹199.72 |
| Dream - No AI Bat Test | 28 Oct - 14 Nov | ₹2,948.04 | 1 | ₹2,948.04 |

Air Light has taken **₹34,643.20 for 67 purchases, ₹517.06 each**, and it is getting
cheaper as it is iterated: the first campaign bought a sale for ₹697, its copy for
₹361. That copy is the best bat line the account has ever run. The **No AI Bat Test**
answered its own question the other way: ₹2,948.04 for a single sale, against the
Dream Bat original's ₹2,981.94 for one - neither works, and the AI angle was not the
problem.

**The bid-cap test is throttled, not cheap.** `Air Light - Purchase - Bid Cap ₹340/₹320`
carries **₹3,000/day on each of its two ad sets, ₹6,000/day in all**, and has spent
₹199.72 in three days. One sale at ₹199.72 looks like the account's best number; it is
a bid cap holding delivery down, not an efficiency. Judge it when it has spent.

**The quiz is still frozen** - nine refreshes now at exactly **₹6,692.70 and 1,019
leads**, last delivery 21 Sep. All three quiz campaigns read PAUSED. Its `REELS`, `PLACE`
and `AGE` arrays were re-pulled in full this refresh and came back identical to the
published ones, to the paisa, which is what a genuinely stopped funnel should look like.

**The clinic spent again and then stopped.** `Clinic - Parel ₹199 sessions` ran to
12 Oct and closes at ₹9,298.21 for 11 sessions (₹845.29 each, up from ₹797 at the
last cut). Its **Copy** was switched on for 2-3 Nov, took ₹837.52 and produced **no
sale at all** - the one blank in the result column, and the page now says so on its own.

**The shop is selling far more than the ads claim.** Over the 28 days of pixel history
that can still be fetched, the store recorded **190 Purchase events** while Meta
attributed 67 purchases to ads in the same stretch. Worth saying to the client rather
than letting the ad numbers stand as the whole picture.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** All eleven
campaigns on account `1811409506889048` are now first-class: `CAMPS_ALL`,
`DAILY_ALL` (a row per campaign per day) and `ADS_ALL` (a row per ad) drive a new
account section at the top, and every campaign gets its own creative table.

**Leads and purchases are never blended.** The account sells four different
things — quiz leads, ₹199 clinic sessions, Dream Bats and Air Light bats — so
each group is priced in its own currency and there is deliberately no single
account-wide "cost per result". `CAMPS_ALL[i][5]` says which result kind a campaign
reports, and `CAMPS_ALL[i][2]` which group it belongs to. **A new product line means a
new entry in `GROUPS`** (`air` was added on 22 Nov); without one its campaigns would
carry no tile and no group total.

**New campaigns are appended to `CAMPS_ALL`, never inserted.** `DAILY_ALL` and `ADS_ALL`
index into it by position, so reordering it re-labels every historical row. The five
Air Light / No-AI campaigns went on the end as indexes 6–10. One campaign is
deliberately left out: `OLD · DO NOT USE · Z-Bat Clinic Leads` is ARCHIVED and has
never spent a rupee, so it would only add a dead row. Add it the day it spends, on the
end.

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
`start_time`/`end_time`; **max lookback 28 days**. An IST day D is the 24 hourly
buckets from **UTC (D-1) 19:00 through UTC D 18:00**. The README used to state this as
"(D-1) 12:00 to D 11:00, stamps are -07:00", which is the same window - but the stamps
moved to **-08:00** when US daylight saving ended on 1 Nov 2026, so the rule is now
written in UTC, where it holds all year. Bucketing by the old local-time rule would shift
every November day by an hour.

**There is a permanent hole in `PIXEL`: 29 Sep to 26 Oct.** The 28-day lookback had
already passed those days by the time this refresh ran, so they can never be fetched.
The published September rows are kept and the new 26 Oct - 22 Nov rows appended; the page
detects the discontinuity itself and says in the pixel note how many days are missing and
that the table's totals exclude them. **Do not let the window lapse again** - refresh at
least every 28 days or the series keeps losing months.

A `Search` event (3 events in the window) now fires on the site and is **not** carried in
`PIXEL`: adding it to `EV` would change the row shape, and the historical September rows
could not be back-filled with a value for it.

These counts cover all site traffic, not only visitors from ads, so they run ahead of the
Meta lead count - the page says so.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.

## Prose is derived, and two numbers had been hand-written

The page's promise is that every sentence is rendered from the arrays. Two places were
not, and both had gone stale by this refresh:

- the account intro and the masthead both said **"six campaigns"** as a literal. They now
  count `CAMPS_ALL`.
- the note under the account table named campaign indexes 4 and 5 by hand and assumed
  exactly **two** blanks in the result column. Both assumptions had expired - the clinic
  copy has since spent, and Dream Bat has its first sale - and the sentence rendered
  broken ("Dream Bat - Direct sales has ."). It now derives the blanks, and what to say
  about each, from the data.

If you add a fact to this page, compute it; do not type it.
