# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling (8 Sep – 16 Nov 2026 at this snapshot) | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 16 Nov 2026: delivery resumed, and the account is now a bat-sales
account.** The account spending limit that stopped everything in late September is no
longer binding - `STOP_REASON` is back to `null` and the page's stop note has cleared
itself. Spend restarted on 1 Oct, stopped again 2-8 Oct, and has run almost every day
since 9 Oct. Lifetime spend is **₹48,046.42** across 70 days, up from ₹14,406.77
at the last snapshot.

**What the money is doing now is selling bats, not running the quiz.**

| Campaign | Spend | Result | Status |
|---|---|---|---|
| Air Light - Purchase | ₹21,613.12 | 31 purchases | PAUSED |
| Clinic - Parel ₹199 sessions | ₹9,298.21 | 11 purchases | PAUSED |
| Quiz - Website Leads (Copy) | ₹3,744.61 | 759 leads | PAUSED |
| Air Light - Purchase (Copy) | ₹3,674.89 | 9 purchases | **ACTIVE** |
| Dream Bat - Direct sales | ₹2,981.94 | 1 purchase | PAUSED |
| Dream - No AI Bat Test | ₹2,948.04 | 1 purchase | PAUSED |
| Quiz - Website Leads | ₹1,635.16 | 126 leads | PAUSED |
| Quiz - Add to Cart optimisation | ₹1,312.93 | 134 leads | PAUSED |
| Clinic - Parel ₹199 (Copy) | ₹837.52 | none | PAUSED |

Three campaigns are new since the last snapshot, all of them selling bats on a product
catalog: `Air Light - Purchase` (14 Oct), `Dream - No AI Bat Test` (28 Oct) and
`Air Light - Purchase (Copy)` (13 Nov, the only ACTIVE campaign, on ₹1,500/day).
**Air Light is the account's best line by a distance** - ₹25,288.01 for 40 purchases,
**₹632.20 a sale**, against Dream Bat's ₹2,964.99 and the clinic's ₹921.43. Within
it, `Air Light - Hook1` has taken ₹20,649.47 of the ₹25,288.01 and 33 of the 40
sales; Hook2 and Hook4 between them sold nothing on ₹636.25.

**A fourth product line means a fourth `GROUPS` entry.** Air Light is priced on its own,
`air`, because an Air Light sale, a Dream Bat sale, a ₹199 clinic session and a quiz
lead are four different things. `ACCT_RES_KINDS` is now 4 and the page still refuses to
produce one account-wide cost per result.

**The quiz has not moved at all, for the ninth refresh running.** Still exactly
₹6,692.70 and 1,019 leads, still no late-attributed lead, and it has now been dark
**56 days** since 21 September. Every quiz-only array in the file - `DAILY`, `REELS`,
`PLACE`, `AGE` - came back from this pull byte-for-byte identical to what was published.
`DAILY` is padded with zero rows to `SNAP_DAY` so the invariant holds and the page works
the stop out for itself.

**Instagram still returns no rows at all.** Re-checked on this pull: the placement
breakdown has `facebook`, `audience_network` and `whatsapp` and no `instagram` row of any
kind. The page's eligibility explanation stands unchanged.

**The clinic came back and then stopped again.** `Clinic - Parel ₹199 sessions` went
from ₹6,377.06 / 8 purchases to **₹9,298.21 / 11 purchases**, running 8 more days to
12 Oct. Its copy, which the last snapshot described as "built and never switched on", has
since spent **₹837.52** on 2-3 Nov for no sale at all - which is why the result-column
note in `index.html` no longer names campaigns by index (see *Page fixes* below).

**Dream Bat finally sold one.** It was flagged last time at ₹1,337.01 with no purchase;
it closed at ₹2,981.94 with **1**, after 62 adds to cart. `Dream - No AI Bat Test` then
spent ₹2,948.04 for **1** more on 98 adds to cart. The cart-to-purchase gap that the
quiz funnel showed is still there on the Dream campaigns: 170 adds to cart across the two
of them, 2 sales. Air Light does not have the problem - 374 adds to cart, 40 sales.

### The pixel table now has a hole in it

`ads_get_dataset_stats` will not look back more than **28 days**. This snapshot is 48
days after the last one, so **30 Sep - 19 Oct can no longer be fetched at all** and never
will be. `PIXEL` therefore runs 8-29 Sep, jumps, and resumes 20 Oct - 16 Nov. The page
detects the discontinuity from the dates and says so above the funnel table, including
that the period row adds two separate stretches of time.

**This will happen again on every refresh more than 28 days apart.** If the pixel series
matters, the report needs refreshing at least every four weeks, or the dataset counts
need storing as they are pulled.

A `Search` event (3 events in 28 days) now fires on the dataset and is not in `EV`. It is
left out rather than changing the width of every `PIXEL` row for three events.

### Page fixes in this refresh

The page is meant to render every sentence from the arrays. Three places did not, and all
three had gone false:

- `"6 campaigns"` in the period line and `"Six campaigns have run on this account"` were
  written out as words. Both now read `CAMPS_ALL.length`.
- The result-column note named campaigns by **fixed index** - index 4 "was built and never
  switched on" and index 5 had "not one sale yet". Index 4 has since spent ₹837.52 and
  index 5 has sold one, so the sentence was both wrong and, because of a ternary that
  collapsed to an empty string, left a dangling " has ." in the page. It now reads the
  blank-result campaigns off the data and splits them into never-switched-on and
  spent-but-no-sale by itself.

**Do not write a count or a campaign index into the prose.** Everything the page says
should come out of `CAMPS_ALL`, `DAILY_ALL` and `ADS_ALL`.

### One thing left for a human

The account section's lifetime figures **include the live part-day**, because `cSpend`
sums every `DAILY_ALL` row. Today that is ₹163.18 of ₹48,046.42 (0.3%), so nothing
material moves - but a part-day is inside `Air Light - Purchase (Copy)`'s ₹632.20-a-sale
figure. The quiz sections exclude `SNAP_DAY` properly; the account section never has.
Worth deciding whether the same guard should apply there.

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

**Max lookback is 28 days and there is no way round it.** Anything older than that is
gone for good, so `PIXEL` keeps the rows already published and appends only the window
the API will still serve. See *The pixel table now has a hole in it* above.
