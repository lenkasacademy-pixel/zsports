# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | 8 Sep – 12 Nov 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 12 Nov 2026: delivery resumed and the account looks nothing like
the September report.** The account spending limit that had stopped everything was
raised or removed, and **`Air Light · Purchase · India · Oct 2026` started on
14 October**. It is now the biggest campaign the account has ever run:
**₹21,000.91, 31 purchases at ₹677.45** — 48% of lifetime account spend on its own.
A second new campaign, `Dream · No AI Bat Test · Purchase · Oct 2026`, started on
28 October (₹2,738.20, 1 purchase).

`STOP_REASON` is now **`null`**, as the previous note said it should be once
delivery resumed. The page's stop detection is computed from `CAMPS_ALL` status and
`DAILY_ALL`, so the whole "nothing has been served since…" paragraph cleared itself
— do not hand-write or hand-remove it.

| | Spend | Result | Each |
|---|---|---|---|
| AI Bat Fit Quiz (3 campaigns) | ₹6,692.70 | 1,019 leads | ₹6.57 |
| Z-Bat Clinic (2) | ₹10,135.73 | 11 sessions | ₹921.43 |
| Dream Bat (2) | ₹5,720.14 | 2 sales | ₹2,860.07 |
| **Air Light (1)** | **₹21,000.91** | **31 sales** | **₹677.45** |
| Account | **₹43,549.48** | — | never blended |

Lifetime account spend went **₹14,406.77 → ₹43,549.48**. Of that, ₹318.29 is the
12 November part-day.

**Air Light is the only thing on this account that has ever sold at scale.** It has
run 26 days without a break longer than three days, and it got *better* in November:
₹1,384.95 / 4 purchases on the 8th and ₹1,319.03 / 4 on the 10th against ₹600–₹800
a day in October. Watch it rather than the quiz.

**The clinic is dearer than it looked.** It took ₹9,298.21 on the main campaign for
11 sessions — ₹845.29 each, against the ₹797 the September cut implied — and a
second clinic campaign spent ₹837.52 on 2–3 November for **no sale at all**. Eleven
₹199 sessions cost ₹10,135.73 to sell.

**Dream Bat finally sold one.** The September note said "still no purchase" after
₹1,337.01; it is now ₹2,981.94 with **1 purchase**, and the newer
`No AI Bat Test` campaign has ₹2,738.20 with **1 purchase**. Two sales for ₹5,720.14
is still the worst line on the account, and the old cart problem has not gone away.

**All three quiz campaigns are still PAUSED and have still not spent since
21 September** — nine refreshes now at exactly ₹6,692.70 and 1,019 leads, with not
even a late-attributed lead moving. Non-quiz spend is now **₹36,856.78**, more than
five times what the quiz has ever spent, so this report describes a smaller and
smaller share of the account. Still worth saying to the client rather than letting
the page look idle.

### Air Light needed a fourth group

`GROUPS` had `quiz` / `clinic` / `dream`. Air Light is a different bat and cannot be
priced as a Dream Bat sale, so a fourth group **`airlight`** was added. Anything new
that sells a different thing needs its own group, or its cost per sale gets blended
into someone else's.

### Three hand-written numbers had gone stale

The page is supposed to render every sentence from the arrays, but three places were
written against a six-campaign account and have been made derived:

- the masthead said `6 campaigns` — now `CAMPS_ALL.length`;
- the account table's total row said `All six` — now `All ` + the count;
- `acct-note` named `CAMPS_ALL[4]` and `CAMPS_ALL[5]` by index and asserted one "was
  built and never switched on" (it has since spent ₹837.52) and the other had "not
  one sale yet" (it has one). It now works out which campaigns show a blank result
  from the data.

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

**The 28-day limit has now bitten: 30 Sep – 16 Oct 2026 is permanently missing.**
The gap between the September snapshot and this one was longer than Meta's dataset
history, so those 17 days were already out of reach when this refresh ran and cannot
be recovered. `PIXEL` therefore jumps from 29 Sep to 17 Oct, and the page's
`pixel-note` says so and warns that the Period row adds up only the days listed.
**Refresh this report at least every 28 days** or it will happen again.

Drop a day whose buckets are incomplete rather than publishing an undercount: the
16 Oct window only returned 16 of its 24 buckets because `start_time` cut into it,
so it was left out. A `Search` event now fires too; it is not in `EV` and is not
carried.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.
