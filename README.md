# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 2 November 2026: delivery resumed in October, and the account is now
twice the size it was.** The account spending limit is no longer binding - October took
**Rs 16,949.38** - so `STOP_REASON` is back to `null` and the page's stop note has cleared
itself, as it is designed to. Lifetime account spend is now **Rs 31,356.27** across 8 Sep
to 2 Nov.

**Two new campaigns opened in October and they are the account's biggest lines.**

| Campaign | Spend | Result | Each |
|---|---|---|---|
| `Air Light - Purchase - India - Rs 1000/day - Oct 2026` | Rs 10,504.49 | 14 purchases | **Rs 750.32** |
| `Dream - No AI Bat Test - Purchase - India - Rs 700/day - Oct 2026` | Rs 1,878.81 | 1 purchase | Rs 1,878.81 |

`Air Light` ran 14-27 Oct and again on the 31st, is still ACTIVE, and is the **best
purchase line on the account** at Rs 750.32 against the clinic's Rs 845.29. Its five
`Hook` creatives split hard: `Hook1` took Rs 7,464.70 of the Rs 10,504.49 and returned 9
of the 14 purchases; `Hook3` 4 purchases on Rs 1,538.37 (Rs 384.59 each, the best on the
account); `Hook2` and `Hook4` spent Rs 636.25 between them for nothing and are paused.
`Air Light` is carried as a **fourth product group** (`key:"air"`) rather than folded into
Dream Bat, because it is a different bat line and the page's whole premise is that each
thing is priced in its own currency. **Check that grouping is what the client wants.**

**The clinic and Dream Bat both grew and both then stopped.** The clinic is at
Rs 9,298.21 for **11 purchases** (Rs 845.29) over 15 days to 12 Oct, with nine new `Reel`
creatives added in October on top of `Video 4 Copy 3`; it is now PAUSED. Dream Bat closed
at Rs 2,981.94 and **one** purchase. The old cart problem is unchanged: Rs 2,981.94 and
72 adds to cart for a single sale.

**`Clinic - Parel Rs 199 (Copy)` is ACTIVE and spent Rs 0.12 on 2 November.** That single
paisa-level day is the only reason the account reads as delivering rather than stopped. It
is almost certainly not meant to be live - worth a look.

**All three quiz campaigns remain PAUSED and have not spent since 21 September** - nine
refreshes now at exactly **Rs 6,692.70 and 1,019 leads**, not even a late-attributed lead
moving. The quiz-only arrays (`DAILY`, `REELS`, `PLACE`, `AGE`) are therefore numerically
**unchanged**; `DAILY` only gains zero rows so that its last row is still `SNAP_DAY`. They
also read PAUSED on 20 and 21 September *while spending*, so do not assume PAUSED means
finished - always re-pull the days.

**Non-quiz spend is now Rs 24,663.57 against the quiz's Rs 6,692.70** - nearly four times
it. This report describes a steadily smaller share of what the account does; say that to
the client rather than letting the page look like the whole picture.

**`STOP_REASON` lives in `index.html`, not in a `zs_data.py`** - there is no such file in
this repo. It is `null` while the account delivers and a Meta substatus string
(e.g. `account_spend_limit_reached`) while it does not; the page's stop note is computed
from `CAMPS_ALL` status plus `DAILY_ALL` and clears itself, so never hand-write or
hand-remove it.

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

**Two sentences were hand-written and are now derived.** The masthead said
"6 campaigns" and the account intro said "Six campaigns have run on this account"; both
read off `CAMPS_ALL.length` now, so adding a campaign no longer leaves a wrong number on a
client-facing page. If you add prose, derive it.

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

Pull with `ads_get_dataset_stats`, `aggregation: "event"`, unix `start_time`/`end_time`.
The response is **hourly buckets**, each carrying its own UTC offset, and an IST day is the
24 buckets that fall inside it. **Convert each bucket's timestamp to IST and take the
date** rather than hard-coding an offset: that reproduces the documented -07:00 rule exactly
during PDT *and* survives the change below. These counts cover all site traffic, not only
visitors from ads, so they run ahead of the Meta lead count - the page says so.

**Two things bit this refresh.**

1. **The 28-day lookback is a hard wall, and missing a refresh loses history for good.**
   `start_time` cannot be more than 28 days ago, so on 2 November the earliest fully
   retrievable IST day was **6 October**. `PIXEL` ended at 29 September, which means
   **30 Sep - 5 Oct (6 days) can never be recovered**. The array now has a permanent hole
   there. The page detects any break in the series itself and warns that the `Period` row
   adds only the days listed - derived from `PIXEL`, so it disappears once the series is
   continuous again. **Refresh this report inside 28 days or the store-pixel history is
   lost one day at a time.**
2. **US daylight saving ended on 1 November 2026**, so bucket stamps shift from `-0700`
   to `-0800` partway through the window (both offsets appear in this pull). Code that
   assumes `-07:00` will mis-bucket every day from 1 November onward by an hour. Using each
   bucket's own offset, as above, makes this a non-issue.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions them
automatically once they have volume. Event names map to `EV` in order as PageView,
ViewContent, BatFitStarted, Lead, AddToCart, InitiateCheckout, AddPaymentInfo, Purchase,
ClinicBookOpened, ClinicSlotPicked, Schedule.
