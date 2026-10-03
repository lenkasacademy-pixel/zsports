# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 3 Oct 2026: the spending limit was lifted, the account served
one day, and then everything was paused.** The `account_spend_limit_reached`
stop that held the account dark from 26 Sep is over — delivery resumed on
**1 Oct** (₹614.83: Clinic ₹346.45, Dream Bat ₹268.38) after six dark days
(26-30 Sep). Nothing served on 2 Oct and nothing has served on the 3rd. **All
six campaigns on the account now read PAUSED**, so this is a decision, not a
delivery fault, and `STOP_REASON` is back to `None`. Lifetime account spend is
₹15,021.60.

Because no campaign is ACTIVE any more, the page's delivery-stop note clears
itself (the detector returns early when nothing is live) — that is the intended
behaviour, so do not hand-write a replacement note. If a campaign is switched
back on and then fails to serve, the note returns on its own.

**Worth knowing while the ads are off:** the store pixel keeps recording sales
with no ad spend behind them — **2 Oct: 9 raw Purchase events, 9 AddPaymentInfo,
₹0 spent**. The shop is still selling; it is the advertising that has stopped.

**One pixel day needs a human eye.** 29 Sep (IST) carries **239 AddToCart
events**, 231 of them inside two hours (17:30-18:30 IST), against single digits
on every day either side and with no ad spend at all behind them. Nothing else
on that day moved - 350 PageViews, 4 quiz starts, 2 leads, zero purchases - so
this looks like bot or scripted traffic rather than demand. It inflates the
pixel cart column on the page, which already carries the caveat that these
counts cover all site traffic, not only ad visitors. Note the previously
published 29 Sep pixel row (`PV 83, ATC 3`) was a part-day captured at the
11:10 am snapshot; this refresh is the complete 24-bucket day.

**All three quiz campaigns read PAUSED and none has spent since 21 September** —
nine refreshes now at exactly ₹6,692.70 and 1,019 leads, with not even a
late-attributed lead moving. They also read PAUSED on 20 and 21 September while
spending, so do not assume a PAUSED status means a campaign is finished — always
re-pull the days, and re-pull the days either side of the last snapshot too
(Meta revised both of those days up by ₹0.28 after they were published). The page
works all of this out from `CAMPS` status and the `DAILY` rows and says it in its
own words — do not hand-write a note about it.

**Dream Bat** is at ₹1,605.39 over four serving days: 252 landing page views, 37
adds to cart, 15 site leads, **still no purchase**. That is the quiz funnel's old
cart problem appearing again on a campaign that does not touch the quiz — 37
carts and nothing through checkout. The clinic is at ₹6,723.51 and **9
purchases** (Meta late-attributed a ninth to 25 Sep after the last refresh); its
1 Oct day took ₹346.45 for 24 landing page views and no purchase.

Non-quiz spend on this account is now ₹8,328.90, more than the quiz has ever
spent (₹6,692.70). When the quiz is dark, this report describes a smaller and
smaller share of what the account is doing — worth saying to the client rather
than letting the page look idle.

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
