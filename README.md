# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling (now to 27 Oct) | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 27 Oct 2026: delivery has resumed, and the account is spending
again on something new.** The account spending limit that held it dark for the
last three days of September no longer binds — lifetime spend has gone from
₹14,406.77 to **₹28,578.60**, so the limit was raised or removed. `STOP_REASON`
is now `None`/`null` and the page's delivery-stop note has cleared itself.

**The money has moved to a new bat.** `Air Light · Purchase · India · ₹1000/day`
(`120253356448770067`) was created in October, is the **only ACTIVE campaign**,
and has taken **₹9,605.75 over 14 days (14–27 Oct) for 14 purchases — ₹686.13
each**, the best cost per sale any bat campaign on this account has managed. Its
five hooks are very unevenly matched: `Air Light - Hook1` alone is ₹7,124.65 of
the spend and 9 of the 14 sales, `Hook3` has 4 sales on ₹1,123.72, and `Hook2`
has none on ₹170.03.

**Everything else is now PAUSED**, including the clinic and Dream Bat, which were
ACTIVE at the last snapshot:

- **Z-Bat Clinic** ran 13 Sep–12 Oct and closes at **₹9,298.21 for 11 purchases**
  (₹845.29 each), up from ₹6,377.06 and 8. Nine new `Reel * Hook * v2` creatives
  were added in October; between them they took ₹2,559.07 for 2 purchases, while
  the original `Video 4 · Copy 3 Local offer` holds ₹6,739.14 and 9.
- **Dream Bat** ran 23 Sep–14 Oct and **finally converted**: ₹2,981.94 for
  **1 purchase** (and 24 site leads, 72 adds to cart, 454 landing page views).
  The README previously flagged this campaign as "still no purchase" after
  ₹1,337.01 — it took another ₹1,645 to get one, so the cart problem is real but
  not total.

**The quiz is still dark and still frozen — nine refreshes now at exactly
₹6,692.70 and 1,019 leads.** Its last spending day was **21 September**; the page
works out for itself that it has been dark for 36 days and says so. All three
quiz campaigns read PAUSED. Not one late-attributed lead has moved, and the
placement, age and creative breakdowns are **byte-identical** to the last
snapshot — re-pulled and re-checked, not assumed.

Non-quiz spend is now **₹21,885.90** against the quiz's ₹6,692.70 — more than
three times it. The quiz sections below describe a shrinking share of what the
account is doing.

**Worth knowing: the store is selling far more than Meta is claiming.** The store
pixel recorded **92 Purchase events between 30 Sep and 27 Oct** against the 14
purchases Meta attributes to Air Light and the 1 to Dream Bat in the same window.
These counts cover all site traffic, not only visitors from ads, so they always
run ahead — but a gap that wide is worth a look at where the other sales come
from.

## A fourth product group (changed 27 Oct 2026)

`GROUPS` gained a fourth entry, `air`, for the Air Light campaign. Air Light and
Dream Bat are both bats and both report purchases, but they are **different
products at different prices** (₹686.13 against ₹2,981.94 a sale), so blending
them into one "bats" line would hide exactly the comparison that matters. The
account now sells **four** things and the page says so — the count is computed
from `GROUPS`, not written down.

Two hard-coded counts were also removed while doing this: the masthead and the
account intro both said "6 campaigns" in prose. They now read `CAMPS_ALL.length`,
so they cannot go stale the next time a campaign is added. **`STOP_REASON` lives
in `index.html`, not in a `zs_data.py`** — there is no such file in this repo.

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

**Zero rows are only added forward from a campaign's first active day.** The
rule is "explicit zero rows for live campaigns on dead days so the stop is
visible as data rather than a gap" — but applied to *every* day it made Air
Light, created in October, report a range of "8 Sep–27 Oct". Fill zeros from the
campaign's first active day onward only; `cFirst`/`cLast` read the raw first and
last row, so leading zeros fake a start date.

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
