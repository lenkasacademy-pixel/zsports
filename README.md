# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 28 Nov 2026: the account is running again, it is four times the
size it was, and it is selling a different product.** The spending limit that
stopped everything in September is gone — ₹10,000 was added to the balance on
21 November and Meta has billed every day since. Lifetime spend is now
**₹65,253.79 across 82 days**, against ₹14,406.77 at the last snapshot. Five
campaigns have been built since, all optimising
`actions:offsite_conversion.fb_pixel_purchase` against product catalogue
`1069890358754783`; four of them carry the **Air Light** bat and have taken
**₹42,495.38 for 79 website purchases, ₹537.92 each**. `STOP_REASON` is now
`None` and the page's stop notice clears itself.

**Air Light is the account now, and one creative is Air Light.** `Air Light -
Hook1` runs in three of the four Air Light campaigns and has taken ₹34,451.02 —
81% of that group's spend — for **67 of its 79 purchases, ₹514.19 each**. Hook3
is a distant second (₹6,333.29, 9 purchases, ₹703.70). Hooks 2, 4 and 5 are
effectively untested: Meta set them Inactive at review on 20 and 23 November and
they have spent ₹636.25 between them. The four campaigns are the same two
creatives rebought at different bids, and they price very differently —
₹406.00 (Copy), ₹523.44 (Copy 2), ₹697.20 (the original), ₹104.83 on 2 purchases
from the bid-cap test. **The cheap one is the one that is paused.**

> **Check this before anything else.** On 26 November the owner paused
> `Air Light · Purchase (Copy 2)` at 7:13 am and switched
> `Air Light · Purchase (Copy)` back on at 9:49 am, at the same time changing its
> ad set from "Automatically bid for actions" to a **₹400 cost-per-result goal**.
> That campaign's own cost per purchase is ₹406. Spend went ₹1,121.34 on the
> 26th → **₹175.42 on the 27th → ₹0.48 by 8:58 am on the 28th**. A cost goal set
> below what the campaign actually achieves throttles delivery, and that is what
> this looks like. The page does not call it a stop, correctly — the campaign is
> delivering, just barely.

**The quiz is finished unless someone restarts it.** All three quiz campaigns
read PAUSED, none has spent since 21 September, and the figures have not moved
across ten refreshes: exactly ₹6,692.70 and 1,019 leads at ₹6.57. It has now
been dark for **68 days**. The quiz-only sections below the account view
(`DAILY`, `REELS`, `PLACE`, `AGE`, `PIXEL`) are unchanged for that reason — only
zero rows were appended to `DAILY` so the series still ends on `SNAP_DAY`. The
page works this out from `CAMPS` status and the `DAILY` rows and says it in its
own words; do not hand-write a note about it.

**The clinic sold three more sessions, two of them after it stopped.**
`Clinic · Parel ₹199 sessions` is at ₹9,298.21 and **11 sessions (₹845.29)**, up
from ₹6,377.06 and 8. It ran again from 1 to 12 October with nine new `Reel … v2`
creatives, then stopped. One of the three extra sessions is not new spend at all:
Meta has **late-attributed a purchase to 25 September**, a day that was published
with none. Its copy, `Clinic · Parel ₹199 (Copy)`, finally delivered on 2–3
November and took ₹837.52 for nothing — no purchase, no lead, no add to cart.

**Dream Bat still cannot sell a bat.** The old campaign added ₹1,644.93 (now
₹2,981.94 lifetime) for its **first ever purchase**. A new one,
`Dream · No AI Bat Test · Purchase`, ran 28 October – 14 November for ₹2,948.04
and **one purchase**. Between them that is ₹5,929.98 for 2 sales, ₹2,964.99 each,
against Air Light's ₹537.92. Same account, same catalogue, same checkout — the
difference is the product.

**The quiz is now 10% of this account.** Non-quiz spend is ₹58,561.09 against the
quiz's ₹6,692.70. The report is named after the quiz and the quiz is the smallest
thing on it; the account view at the top is what the client should be reading.

**The store sells far more than Meta is credited with.** The pixel recorded
**227 Purchase events in the 28 days to 27 November** (33,904 page views, 2,388
adds to cart, 881 checkouts) while Meta attributed 83 purchases to ads across the
whole of 30 September – 28 November. That gap is not in `index.html` and should
not be read as ad performance — it is the shop's own traffic — but it is worth
saying to the client before they judge the campaigns on Meta's number alone.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** Every
campaign on account `1811409506889048` is first-class — **twelve** of them as of
28 Nov 2026: `CAMPS_ALL`, `DAILY_ALL` (a row per campaign per day) and `ADS_ALL`
(a row per ad) drive a new account section at the top, and every campaign that
has an ad gets its own creative table. Nothing in the page hard-codes how many
campaigns there are; the masthead, the intro and the table footer all count
`CAMPS_ALL`.

**Leads and purchases are never blended.** The account sells four different
things — quiz leads, ₹199 clinic sessions, Dream bats and Air Light bats — so
each group is priced in its own currency and there is deliberately no single
account-wide "cost per result". `CAMPS_ALL[i][5]` says which result kind a
campaign reports and `CAMPS_ALL[i][2]` which group it belongs to; **adding a
group means one entry in the `GROUPS` array and nothing else.** Dream and Air
Light are kept apart even though both report `purchase`, because they are
different bats at very different prices.

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

**Which campaigns show a blank result is read off the data, not written down.**
A campaign can start spending between refreshes — `Clinic · Parel ₹199 (Copy)`
did — so the note under the account table derives "never switched on" and "spent
with no result" from `CAMPS_ALL`/`DAILY_ALL` each time the page loads. Do not put
campaign indexes back into that sentence.

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
- Campaign-level day rows summed per day equal the `level: "ad_account"` daily
  totals on spend **and** impressions, for every day in `ALL_DAYS`. On the
  28 Nov refresh that held on all 82 days with no drift at all, including the
  two most recent — re-read them anyway, because the ~48h revision window is
  real even when a given pull happens to be stable.

### Traps that cost time on the 28 Nov refresh

- **A campaign-daily pull of 8 campaigns × 20 days overflows the response cap.**
  Let it spill to a file and aggregate the files with a script; three calls of
  ~160 campaign-days each covered 30 Sep – 28 Nov.
- **Do not drop day rows just because spend is zero.** Meta attributes views,
  adds to cart and leads to days a campaign did not spend on, and four of the
  per-campaign ad-vs-day checks failed until those rows were kept. Keep a row if
  *any* field is non-zero, not only spend or impressions.
- **Late-attributed conversions reach back weeks.** `Clinic · Parel ₹199
  sessions` gained a purchase on **25 September** between the two snapshots.
  Re-pull a campaign's whole history, not just the new days, and let the
  ad-vs-day assertion find the day for you.
- **ARCHIVED campaigns and ads are invisible to an unfiltered listing.** Filter
  `effective_status IN [… "ARCHIVED", "DELETED"]` or you will miss
  `OLD · DO NOT USE · Z-Bat Clinic Leads` and the archived clinic creatives.
- **Duplicate ad names inside one campaign.** `Quiz · Add to Cart optimisation`
  carries three zero-spend ads with exactly the names of its three spending
  ones. `ADS_ALL` keeps every ad that spent, plus zero-spend ads that are not
  ARCHIVED and whose name is not already in that campaign — otherwise the
  creative table shows the same line twice.
- **`PIXEL` cannot be back-filled.** The event API reaches back 28 days from
  whenever it is called, so 30 Sep – 31 Oct is permanently unavailable. `PIXEL`
  therefore still covers 8–29 Sep, which is when the quiz ran, and the page now
  states that window and the reason itself.

### Store pixel

Pull with `ads_get_dataset_stats`, `aggregation: "event"`, unix
`start_time`/`end_time`; max lookback 28 days. Events are stamped **−07:00**, and
an IST day D is the 24 buckets from (D−1) 12:00 through D 11:00. These counts
cover all site traffic, not only visitors from ads, so they run ahead of the Meta
lead count — the page says so.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.
