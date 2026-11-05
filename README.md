# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | 8 Sep 2026 – 5 Nov 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 5 Nov 2026: the account is serving again, and the quiz is not.**
The account spending limit that stopped everything on 26 September has been
raised or removed — delivery resumed on 1 October and the account has taken
**₹34,894.29** across 8 Sep – 5 Nov (lifetime). `STOP_REASON` is back to `None`
and the page's stop note has cleared itself.

| | spend | result | each |
|---|---|---|---|
| **Air Light · Catalogue purchases** (live) | ₹13,205.11 | 18 purchases | **₹733.62** |
| Clinic · Parel ₹199 sessions | ₹9,298.21 | 11 sessions | ₹845.29 |
| Quiz · Website Leads (Copy) | ₹3,744.61 | 759 leads | ₹4.93 |
| Dream Bat · Direct sales | ₹2,981.94 | 1 sale | ₹2,981.94 |
| Dream · No AI Bat Test | ₹1,878.81 | 1 sale | ₹1,878.81 |
| Quiz · Website Leads | ₹1,635.16 | 126 leads | ₹12.98 |
| Quiz · Add to Cart optimisation | ₹1,312.93 | 134 leads | ₹9.80 |
| Clinic · Parel ₹199 (Copy) | ₹837.52 | — | — |

**Two campaigns are new since the last refresh**, and both sell bats:

- **`Air Light · Purchase · India · ₹1000/day · Oct 2026`** (`120253356448770067`),
  started 14 Oct, **ACTIVE**, ₹700/day, catalogue-backed. It is now the account's
  biggest line ever at ₹13,205.11 and the only campaign still serving. **18
  purchases at ₹733.62** — the best cost per sale any bat campaign on this account
  has managed, against Dream Bat's ₹2,981.94. Five hooks; `Air Light - Hook1` has
  ₹9,917.05 of the spend and 13 of the 18 sales (₹762.85), but `Air Light - Hook3`
  is the efficient one at **₹446.66** on ₹1,786.64 — worth more budget.
- **`Dream · No AI Bat Test · Purchase · India · ₹700/day · Oct 2026`**
  (`120253597607430067`), 28–31 Oct, now PAUSED: ₹1,878.81 for 1 sale.

Because Air Light is a different product on its own catalogue campaign, it gets
its **own group** in `GROUPS` (`key:"airlight"`) rather than being folded into
Dream Bat. The account now prices **four** things separately, not three — a quiz
lead, a ₹199 clinic session, a Dream Bat sale and an Air Light sale are not
interchangeable and are never added together.

**Dream Bat finally made a sale.** The old `Dream Bat · Direct sales` campaign
ran on to 14 Oct and closed at ₹2,981.94 with **1 purchase** — the cart problem
flagged last time is no longer "no purchase at all", but one sale on ₹2,982 and
72 adds to cart is still the worst cost per sale on the account.

**`Clinic · Parel ₹199 (Copy)` came off the shelf and spent ₹837.52** on 2–3 Nov
for 42 page views and **no purchase**. It had never served before. It is the one
blank in the result column now, and the page works that out from the data rather
than being told.

**The quiz has not moved at all — ninth refresh at exactly ₹6,692.70 and 1,019
leads.** Nothing has late-attributed, not one lead, not one paisa. All three quiz
campaigns read PAUSED and none has spent since 21 September. `DAILY`, `REELS`,
`PLACE` and `AGE` are byte-for-byte the same figures as the last cut, re-pulled
and re-reconciled rather than assumed. **The quiz is now ₹6,692.70 of a
₹34,894.29 account — 19%** — so this report increasingly describes a minority of
what the account does; say that to the client rather than letting the quiz
sections look like the whole picture.

**The clinic closed at 11 sessions.** `Clinic · Parel ₹199 sessions` ran to 12 Oct
and finished at ₹9,298.21 for 11 purchases (₹845.29). `Clinic ₹199 · Video 4 ·
Copy 3 Local offer` carries ₹6,739.14 and 9 of them at ₹748.79.

## ⚠ Nine days of store-pixel history are gone for good

`ads_get_dataset_stats` serves **at most 28 days** of dataset history. The last
refresh captured pixel days through **29 Sep**; the earliest day retrievable today
is **9 Oct**. So **30 September to 8 October (9 days) can never be recovered** —
those rows are absent from `PIXEL`, not zero.

The page now says this itself: if `PIXEL[0][0]` is later than `DAILY[0][0]` it
prints the gap, the reason and an explicit warning that the missing days are
unavailable rather than empty. Nothing infers them.

**This will happen again every time the report is left for more than four weeks.**
The ad-level arrays can be rebuilt from any date, but `PIXEL` cannot. If the store
funnel matters to the client, this report has to be refreshed at least every 28
days, or the pixel rows have to be archived somewhere outside Meta.

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
- Every campaign's `DAILY_ALL` rows equal its `ADS_ALL` rows on spend,
  impressions, link clicks, page views, adds to cart, purchases and leads.
- Every campaign's `DAILY_ALL` and `ADS_ALL` totals equal Meta's own
  campaign-level period totals.

All of these passed on the 5 Nov 2026 refresh with no mismatch, and `PLACE_DRIFT`
is ₹0.01.

**Do not hand-write a campaign count or a list of which campaigns show a blank
result.** Both used to be literals (`"Six campaigns"`, `"All six"`, and a sentence
naming `CAMPS_ALL[4]` and `CAMPS_ALL[5]` by index) and all three went wrong the
moment two campaigns were added and Dream Bat made its first sale. They are now
derived from `CAMPS_ALL` and the day rows.

### Store pixel

Pull with `ads_get_dataset_stats`, `aggregation: "event"`, unix
`start_time`/`end_time`; max lookback 28 days. Events are stamped **−07:00**, and
an IST day D is the 24 buckets from (D−1) 12:00 through D 11:00. These counts
cover all site traffic, not only visitors from ads, so they run ahead of the Meta
lead count — the page says so.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.

**The 28-day lookback is a hard wall, not a soft one.** Pull the pixel on every
refresh and never let more than four weeks pass, or those days are lost — see the
section above. An IST day D needs `start_time` at (D−1) 19:00 UTC, so the earliest
fully retrievable day is always the day after `now − 28 days`.
