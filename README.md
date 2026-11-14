# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 14 Nov 2026: the account is delivering again, and the quiz is not.**
The account spending limit that stopped everything on 26 Sep has been lifted -
`STOP_REASON` is back to `null` and the page's stop note clears itself. Lifetime
account spend is now **Rs 45,116.69 across 68 days** (8 Sep - 14 Nov) over **nine
campaigns**, up from Rs 14,406.77 and six at the last snapshot.

**The account has become an Air Light account.** `Air Light - Purchase`
(`120253356448770067`) ran 14 Oct - 13 Nov and took **Rs 21,613.12 for 31
purchases, Rs 697.20 each** - on its own more than the whole account had ever
spent before this refresh. A copy of it (`120253876814030067`, Rs 1,200/day) is
the **only ACTIVE campaign** on the account and is the one serving the part-day:
Rs 745.16 and 2 purchases over 13-14 Nov.

| Product line | spend | result | each |
|---|---|---|---|
| Air Light (2 campaigns) | Rs 22,358.28 | 33 sales | **Rs 677.52** |
| Z-Bat Clinic (2) | Rs 10,135.73 | 11 sessions | Rs 921.43 |
| AI Bat Fit Quiz (3) | Rs 6,692.70 | 1,019 leads | Rs 6.57 |
| Dream Bat (2) | Rs 5,929.98 | 2 sales | Rs 2,964.99 |

**The quiz has not moved by one paisa.** Still exactly Rs 6,692.70 and 1,019
leads, as at every refresh since 21 September - **54 days dark**, and all three
quiz campaigns now read PAUSED, so the page calls it a deliberate stop rather
than a delivery problem. Because the quiz is frozen, `REELS`, `PLACE` and `AGE`
were re-pulled and came back **byte-identical**; only `DAILY` grew, with zero
rows out to `SNAP_DAY`.

**The cart problem is now the account's main problem.** `Dream Bat - Direct sales`
and `Dream - No AI Bat Test` have between them taken Rs 5,929.98 for **two sales**,
against 160 adds to cart and 40 site leads. Air Light converts (Rs 677.52 a sale);
the Dream lines do not. `Clinic - Parel Rs 199 (Copy)` spent Rs 837.52 on 2-3 Nov
for **no session at all and nothing behind it** - that is the one blank in the
account table, and the page now works out which campaigns are blank and why
instead of naming two of them by index.

**Non-quiz spend is now Rs 38,423.99 against the quiz's Rs 6,692.70** - the quiz
is under 15% of what this account does. The report is still named for the quiz;
worth raising with the client.

**Two campaign names disagree with their own budgets.** `Air Light - ... Rs 1000/day`
reports a `daily_budget` of Rs 700, and `Dream - No AI Bat Test - ... Rs 700/day`
reports Rs 1,000. `CAMPS_ALL[i][4]` carries Meta's `daily_budget`, not the number in
the name, so the budget the page shows is the real one and the names are stale labels.

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

**The 28-day lookback will punch a hole in `PIXEL` if a refresh is late.** This one
was: the last snapshot ended 29 Sep and the window now opens on 18 Oct, so **no pixel
rows exist for the 18 days between**, and they can never be recovered. The September
rows were kept rather than dropped - they are the only record of the quiz funnel while
the quiz was running. The page detects the hole from the row dates itself and says so
under the funnel table, including that the period row totals the days listed rather
than a continuous run. Refresh inside 28 days and this never happens.

A new event, **`Search`, has started firing** (3 events in the 28-day window). It is
deliberately **not** in `EV`: adding a twelfth column would force a zero into every
September row for an event that was never pulled for those days, which would read as
a measured zero rather than an unknown. Add it when it has volume worth a column, and
backfill nothing.

### `SNAP_DRIFT`

Ad-level and campaign-level reads minutes apart do not agree on the live part-day.
On this refresh the one ACTIVE campaign's creative rows came back **Rs 2.23 and 5
impressions ahead** of its day row. `SNAP_DRIFT` carries that number and the page
reports it in its own reconciliation line, because the day-rows-vs-ad-rows assertion
genuinely does not hold for `SNAP_DAY`. Set it to `null` when a refresh finds them
equal. Every other campaign agrees exactly on spend, impressions, link clicks, page
views, adds to cart, purchases and leads; campaign sums match the `ad_account` daily
totals on all 67 settled days.

### Counts are derived, not written

The masthead campaign count, the account intro and the account table's total row used
to say "6", "Six" and "All six" literally. Going from six campaigns to nine made all
three wrong, so they now read `CAMPS_ALL.length`. The "which campaigns are blank"
sentence was hardcoded to indexes 4 and 5 and broke outright when campaign 5 took its
first sale; it is derived now. If you add a campaign, add nothing else - but do append
it to the **end** of `CAMPS_ALL`, because `DAILY_ALL` and `ADS_ALL` index into it by
position.

A campaign whose group key is missing from `GROUPS` is **silently dropped from the
account view**. The two Air Light campaigns needed a fourth group (`airlight`); without
it Rs 22,358.28 of spend would not have appeared in the product-line tiles at all.
Check that every `CAMPS_ALL[i][2]` has a matching `GROUPS` entry.
