# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 12 Oct 2026: delivery has resumed.** Both ACTIVE campaigns (the Clinic
and Dream Bat) now report delivery `active` with substatus `active` — the
`account_spend_limit_reached` block that kept the account dark from 26 Sep is gone, so
whoever raised or removed the account spending limit on `1811409506889048` did the thing
this README asked for. **`STOP_REASON` is now `None`**, and the page's own stop note has
cleared itself from the data. Nothing was hand-written or hand-removed.

**Delivery came back in two bursts, not one.** The account served ₹614.83 on 1 Oct, then
went dark again for 2–8 Oct, then came back properly on 9 Oct and has run every day
since: ₹602.29, ₹957.79, ₹1,047.16, and ₹138.09 so far today. Twelve of the 35 days in
the window took nothing at all. If that 2–8 Oct gap repeats, check the spending limit
again before looking at the campaigns.

Account spend over 8 Sep – 12 Oct is now **₹17,766.93**, up from ₹14,406.77.

**The quiz has not moved, for the twelfth refresh running.** All three quiz campaigns read
PAUSED and none has spent since 21 September — still exactly **₹6,692.70 and 1,019
leads**, now 21 days dark, with not even a late-attributed lead appearing. `REELS`,
`PLACE` and `AGE` came back byte-identical to the previous snapshot. They also read PAUSED
on 20 and 21 September *while spending*, so do not assume a PAUSED status means a campaign
is finished — always re-pull the days, and the days either side of the last snapshot
too. The page works all of this out from `CAMPS` status and the `DAILY` rows and says it in
its own words — do not hand-write a note about it.

**The Clinic is now the account's main line and it has nine new creatives.** ₹8,932.45 and
**11 purchases** at ₹812.04 each. In October nine `v2` reel ads were added alongside the
original `Video 4 · Copy 3 Local offer`, which still carries 75% of the campaign's spend
and 9 of its 11 purchases at ₹748.45 each. Of the new nine, only `Reel 1 Hook C v2`
(₹956.42, 1 purchase) and `Reel 3 Hook C v2` (₹121.94, 1 purchase) have produced anything;
the other seven have ₹1,118.35 between them and no sale. Too early to judge, but
`Reel 1 Hook C v2` at ₹956.42 for one session is already dearer than the control.
The campaign's daily budget also moved **₹600 → ₹500**; `CAMPS_ALL[3][4]` follows it.

**Dream Bat still has not sold a single bat, and it is no longer a small bet.**
₹2,141.78 across 6 days, 331 landing page views, **39 adds to cart, 16 site leads, zero
purchases**. That is the quiz funnel's old cart problem showing up a third time on a
campaign that never touches the quiz: people reach the cart and do not buy. At ₹1,337 it
was worth flagging; at ₹2,142 with 39 abandoned carts it is the single clearest thing on
this account to fix before it spends more. `Dream · Value · front` alone has 29 of the 39
carts on ₹1,089.30.

**A new ARCHIVED campaign appeared and is deliberately left out of `CAMPS_ALL`:**
`OLD · DO NOT USE · Z-Bat Clinic Leads (replaced by ₹199 Session Sales)`
(`120252806446910067`). Meta returns no spend, impression or result fields for it at all,
so it has never delivered. Archived campaigns are **not returned by an unfiltered campaign
listing** — this refresh filtered
`effective_status IN ["ACTIVE","PAUSED","ARCHIVED","DELETED",...]` to find it, and the
account reconciliation confirms no spend is hiding in it. Add it to `CAMPS_ALL` the day it
spends, **appended to the end** so no historical row re-labels itself — and note that
`acct-note` hardcodes indexes 4 and 5, and says "two blanks", so adding a third
never-delivered campaign needs that sentence looked at. Its name is also not something to
put in front of a client as-is.

**Worth knowing:** the store pixel kept recording sales through the dark stretch, with no
ad spend behind them. The shop sells on its own; the advertising is only part of it.

Non-quiz spend on this account is now ₹11,074.23 against the quiz's ₹6,692.70 — the quiz
is down to 38% of what the account does. When the quiz is dark, this report describes a
smaller and smaller share of the account; worth saying to the client rather than letting
the page look idle.

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

**The clinic now fails the ad-vs-day spend check by 30 paise** (ads ₹8,932.75,
days ₹8,932.45) **and by one impression** (58,905 vs 58,904). Link clicks, page views,
adds to cart, purchases and leads all still agree exactly, and every other campaign
agrees on everything. This appeared with the nine new `v2` ads: Meta rounds each ad's
spend independently, and the clinic now has ten ads to round instead of one. It is
0.003% of the campaign and it is left in the data as Meta reports it rather than
plugged — but it does mean the clinic's creative table column sums 30 paise above the
campaign total in its own header. If that drift grows past a rupee or two, stop and find
out why instead of assuming rounding.

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

Event names map to `EV` as: `PageView`=PV, `ViewContent`=VC, `BatFitStarted`=BFS,
`Lead`=LD, `AddToCart`=ATC, `InitiateCheckout`=IC, `AddPaymentInfo`=API,
`Purchase`=PUR, `ClinicBookOpened`=CBO, `ClinicSlotPicked`=CSP, `Schedule`=SCH. The
dataset also emits `Search`, which `EV` deliberately ignores. An hour with no events
returns **no bucket at all**, so a complete day often has 22 or 23 buckets rather than 24
— that is not missing data.

- **The 28-day lookback freezes the early rows.** From 12 Oct the earliest reachable IST
  day is 15 Sep, so the `PIXEL` rows for **8–14 Sep can no longer be re-pulled** and are
  carried forward from the previous snapshot unchanged. They may since have been revised
  at Meta and there is no way to find out. Everything from 15 Sep on is re-pulled every
  refresh.
- **These counts get revised, sometimes a lot.** Re-pulling 15–29 Sep reproduced
  5 of 15 days byte-for-byte and moved the rest. Purchases rose on 17, 18, 21, 25 and
  27 Sep (+2, +1, +1, +1, +2); 20 Sep rose across the board (page views 1,504 → 1,748,
  quiz starts 86 → 126, leads 74 → 92); 15 Sep gained 12 quiz starts; 23 and 24 Sep rose
  slightly. The bucketing convention is not the cause — the days that match, match
  exactly, which is the check that it is right. Re-pull, never append.
- **29 Sep carries a 239 add-to-cart spike that nothing explains.** 231 of those 239
  landed in two consecutive hours (`2026-09-29T05:00` and `06:00` at −07:00, i.e. IST
  17:30–19:30) on a day the account spent **nothing at all**, and no purchase followed.
  It is either duplicate firing on the site or a genuine organic burst; it is in the data
  as Meta reports it, and it is worth someone looking at the site before it is quoted to
  the client as demand.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.
