# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | 8 Sep 2026 – 20 Nov 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 20 Nov 2026: delivery resumed, and the account is now mostly a
different business.** The account spending limit that stopped everything on 26 Sep
was raised — the first spend after the stop is **1 Oct**, and the account has run
almost continuously since. Lifetime spend on the window this report covers
(8 Sep – 20 Nov) is now **₹54,400.12**, up from ₹14,406.77 at the last snapshot.
`STOP_REASON` is back to `null` and the page no longer shows a stop note; it works
that out from `CAMPS_ALL` status and `DAILY_ALL` on its own.

**A new product line arrived in October and now dominates the account.** Two
**Air Light** purchase campaigns (`120253356448770067` and its Copy
`120253876814030067`) have taken **₹31,641.71 — 58% of everything** — and returned
**60 purchases at ₹527.36**. The Copy is the only ACTIVE campaign on the account and
the better of the two: **₹345.81 a purchase against ₹697.20**. This is by far the
best cost per sale the account has ever recorded; the clinic pays ₹921.43 a session
and the two Dream Bat campaigns have **two sales between them for ₹5,929.98**.

`CAMPS_ALL` has grown from six campaigns to nine, and a fourth product group,
`air`, was added to `GROUPS`. **New campaigns are appended** so no historical
`DAILY_ALL` row re-labels itself.

**The quiz is unchanged and has been dark for 60 days.** All three quiz campaigns
are PAUSED and have not spent since **21 September** — still exactly ₹6,692.70 and
1,019 leads, ten refreshes running, with not even a late-attributed lead moving.
The quiz-only arrays (`REELS`, `PLACE`, `AGE`) are therefore untouched; `DAILY` is
extended with zero rows to `SNAP_DAY` so the page counts the dark run itself and
says so. Do not hand-write that note.

**Dream Bat is the problem line.** Across two campaigns and ₹5,929.98 it has
produced **two purchases** — ₹2,964.99 each — on 505 landing page views and 193
adds to cart. The cart problem the quiz funnel had is still there on the campaigns
that do not touch the quiz, and it is now the clearest waste on the account.

**The store pixel says the shop is doing well.** Over the 28 days to 20 Nov it
recorded **32,162 page views, 2,079 adds to cart, 653 checkouts and 177 Purchase
events** — and the trend is up: 15 purchases in the first seven days of that window
against **91 in the last seven**. It also recorded **404 BatFitStarted events with
the quiz dark**, which is organic traffic still finding the quiz page.

### Why `PIXEL` still ends on 29 September

`ads_get_dataset_stats` reaches back **28 days and no further**. The gap between
30 Sep and 22 Oct can never be retrieved, so welding a fresh 28-day block onto the
September rows would produce a `Period` total summing two windows separated by a
hole it does not show. `PIXEL` is therefore **kept as the quiz's own window,
8–29 Sep**, which is the period the quiz funnel describes; the page now says so and
says why. The recent store-pixel figures above are reported here rather than
grafted onto that table. **If the client wants a rolling store-pixel section, it
needs to be its own array with its own window** — not an extension of this one.

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
- Every campaign's `DAILY_ALL` rows sum to the **ad account's own daily** spend and
  impressions, day by day. On this refresh all 74 days matched to the paisa.

**Day rows and ad rows can disagree on site events, and the page now says where.**
Meta's per-day `omni_*` rows sometimes sum *lower* than its per-ad rollup of the
same campaign. On this refresh two campaigns drift: Dream Bat · Direct sales reads
452 page views by day against 454 by creative, and Dream · No AI Bat Test reads 88
adds to cart against 98 and 16 leads against 17. Spend, impressions, link clicks
and purchases agree everywhere. Both numbers are Meta's own and **neither is
adjusted** — the reconciliation note at the foot of the page computes the
difference from `DAILY_ALL` and `ADS_ALL` and names it, so it can never go stale.
Do not "fix" one side to make the assertion pass.

**A part-day moves while you are building.** On this refresh 20 November read
₹32.76 / 174 impressions at the account level and, minutes later, ₹69.80 / 351 —
it more than doubled between two calls in the same session. The per-campaign
monthly rollup fetched before that settled still read ₹2.08 lower than the sum of
its own day rows. Day rows are the source of truth for the arrays; the monthly
rollup is only a cross-check, and on the live day it will lose.

### Store pixel

Pull with `ads_get_dataset_stats`, `aggregation: "event"`, unix
`start_time`/`end_time`; max lookback 28 days. Events are stamped **−07:00**, and
an IST day D is the 24 buckets from (D−1) 12:00 through D 11:00. These counts
cover all site traffic, not only visitors from ads, so they run ahead of the Meta
lead count — the page says so.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.
