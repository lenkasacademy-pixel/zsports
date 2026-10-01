# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 1 Oct 2026: the account has now been dark for five full days and
nothing has changed.** Both ACTIVE campaigns (the Clinic and Dream Bat,
₹1,100/day between them) still report delivery `off` with substatus
**`account_spend_limit_reached`**. Lifetime spend is still **exactly
₹14,406.77** — not one paisa since 25 Sep, when the account took ₹16.26 before
stopping. The ad account has hit its **account spending limit**. It is a limit on
the account, not a fault in the campaigns. **Raise or remove the account spending
limit on `1811409506889048` (Billing → Payment settings) and delivery resumes.**
The exact limit value cannot be read through the API tools used here; check it in
Ads Manager. `STOP_REASON` stays set; clear it once delivery resumes.

The activity log shows **no status, budget or spending-limit change** since the
26 Sep billing event (₹16.30 for the 25th). The only new entries are three
`asa_auto_custom_audience` records Meta created by itself on 30 Sep — housekeeping,
not a fix. **Nobody has acted on this in five days.**

**⚠️ The store pixel logged 239 AddToCart events on 29 September with no ad spend
behind them — and 231 of them landed in two hours.** The −07:00 buckets at 05:00
and 06:00 (about 17:30 and 18:30 IST) carry 96 and 135 adds to cart against 47 and
13 page views in the same hours. More carts than page views is not real shopper
behaviour, so read this as duplicate firing, a bot, or a bulk action rather than
demand. It is carried in `PIXEL` as measured — the page reports what the pixel
says — but **do not quote it to the client as 239 adds to cart** without checking
the shop. Every other day this month is in single or low double digits.

**Otherwise the shop is still ticking over without advertising:** 30 Sep recorded
408 page views, 14 BatFitStarted and 12 Leads with zero ad spend. The quiz funnel
still gets organic traffic; it is the advertising that has stopped.

**All three quiz campaigns read PAUSED and none has spent since 21 September** —
nine refreshes now at exactly ₹6,692.70 and 1,019 leads, with not even a
late-attributed lead moving. They also read PAUSED on 20 and 21 September while
spending, so do not assume a PAUSED status means a campaign is finished — always
re-pull the days, and re-pull the days either side of the last snapshot too
(Meta revised both of those days up by ₹0.28 after they were published). The page
works all of this out from `CAMPS` status and the `DAILY` rows and says it in its
own words — do not hand-write a note about it.

**Dream Bat** is still at ₹1,337.01 over three days: 222 landing page views, 36
adds to cart, 14 site leads, **still no purchase**. That is the quiz funnel's old
cart problem appearing again on a campaign that does not touch the quiz — worth
flagging before it spends more. The clinic is still at ₹6,377.06 and **8
purchases**. Neither has moved since the 25th because neither has served anything.

Non-quiz spend on this account is ₹7,714.07, more than the quiz has ever spent
(₹6,692.70). When the quiz is dark, this report describes a smaller and smaller
share of what the account is doing — worth saying to the client rather than
letting the page look idle.

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
