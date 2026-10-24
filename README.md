# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 24 Oct 2026: delivery has resumed and the account is spending again.**
The account spending limit that stopped everything on 26 Sep is no longer blocking.
Spend came back on **1 Oct** (₹614.83), stopped again for seven days, and has run
**every day since 9 Oct**. `STOP_REASON` is now `None`/`null` and the page's own
stop note has cleared itself, as it is designed to.

**A new campaign is now the account's best purchase line.**
`Air Light · Purchase · India · ₹1000/day · Oct 2026` (`120253356448770067`,
daily budget ₹700) started on **14 Oct** and is the only ACTIVE campaign. In eleven
days it has taken **₹7,600.77 for 12 purchases — ₹633.40 a sale**, against the
clinic's ₹845.29 and Dream Bat's ₹2,981.94. One creative carries it: **Air Light -
Hook1**, ₹5,547.46 and 9 of the 12 sales, **₹616.38 each**. Hook2 and Hook4 have
taken under ₹350 between them and no sales — untested rather than beaten.

**The clinic closed out, and Dream Bat finally sold one.** The clinic campaign ran
to 12 Oct and is now PAUSED at **₹9,298.21 for 11 ₹199 sessions (₹845.29)**, up from
₹6,377.06 and 8 at the last snapshot. Nine new creatives were added to it
(`Clinic ₹199 · Reel 1/2/3 Hook A/B/C v2`); together they took ₹2,559.07 for 2 of the
11 sales, so `Video 4 · Copy 3 Local offer` is still doing the work at ₹748.79 a
session. **Dream Bat took its first sale on 13 Oct** and closed on the 14th at
₹2,981.94 — one sale, 72 adds to cart and 24 site leads. That is still the cart
problem the last snapshot flagged, now with one order behind it rather than none.

**All three quiz campaigns are still PAUSED and still have not spent since
21 September** — nine refreshes now at exactly ₹6,692.70 and 1,019 leads, with not
even a late-attributed lead moving. They also read PAUSED on 20 and 21 September
while spending, so do not assume a PAUSED status means a campaign is finished —
always re-pull the days, and re-pull the days either side of the last snapshot too.
The page works all of this out from `CAMPS` status and the `DAILY` rows and says it
in its own words (it currently reads "dark for 33 days") — do not hand-write a note
about it.

Non-quiz spend on this account is now **₹19,880.92 against the quiz's ₹6,692.70** —
three times what the quiz has ever spent. When the quiz is dark, this report
describes a smaller and smaller share of what the account is doing — worth saying
to the client rather than letting the page look idle.

**Worth knowing: the store pixel recorded 239 add-to-carts on 29 September with no
ad spend behind them at all** (the account was dark from the 26th). The shop sells
on its own; it is only the advertising that had stopped.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** Every
campaign on account `1811409506889048` is first-class: `CAMPS_ALL`,
`DAILY_ALL` (a row per campaign per day) and `ADS_ALL` (a row per ad) drive the
account section at the top, and every campaign gets its own creative table.
There are **seven** of them as of 24 Oct 2026, Air Light being the newest.
**Nothing on the page hard-codes that count** — the masthead, the intro sentence
and the account table's total row all read `CAMPS_ALL.length`, which they did not
before this refresh (they said "six"). Keep it that way.

One campaign is deliberately **not** carried:
`OLD · DO NOT USE · Z-Bat Clinic Leads (replaced by ₹199 Session Sales)`
(`120252806446910067`) is ARCHIVED and has never delivered — Meta returns no
spend or impressions for it at all. Add it the day it spends, **appended to the
end of `CAMPS_ALL`** so no historical `DAILY_ALL` or `ADS_ALL` row re-labels
itself. Three zero-spend duplicate ads in the Add-to-Cart campaign are also left
out, because their names are identical to the three ads that did run and two
rows with the same name read as a data bug rather than as completeness.

**Leads and purchases are never blended.** The account sells four different
things — quiz leads, ₹199 clinic sessions, Dream Bats and Air Light bats — so each
group is priced in its own currency and there is deliberately no single
account-wide "cost per result". `CAMPS_ALL[i][5]` says which result kind a
campaign reports, and `CAMPS_ALL[i][2]` which group it belongs to. Air Light got
its own `air` group rather than joining `dream`: both sell a bat direct, but they
are different products at very different prices per sale, and merging them would
hide exactly the comparison worth making.

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

**The 28-day lookback now bites.** On 24 Oct 2026 the earliest day the API would
return was 28 September, so `PIXEL` rows for 8–27 September can no longer be
re-pulled — they are carried forward from the previous build and must not be
dropped. The bucketing was re-verified where the windows overlap: 28 September
re-derived to `[352, 80, 8, 6, 15, 8, 5, 0, 12, 10, 10]`, byte-identical to the
published row, which is the check that the −7:00 → IST convention is still right.
Pull the whole window in one call and bucket locally: a bucket stamped at hour
≥12 belongs to the next IST day, a bucket at hour ≤11 to its own.
