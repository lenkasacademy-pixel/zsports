# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling (to 4 Oct) | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 4 Oct 2026: the spending-limit stop is over, and the account is
now paused on purpose.** ₹10,000 was added to the balance on **1 Oct at 2:16 pm**
and delivery resumed the same day — the Clinic took ₹346.45 and Dream Bat
₹268.38, ₹614.83 between them, the first spend on the account since 25 Sep. Then
at **8:18 pm that same evening Abhijeet Lenka paused both campaigns** in Power
Editor (activity log, `Campaign status updated`, Active → Inactive). **All six
campaigns now read PAUSED.**

That matters for the page: the delivery-stop note is computed from the ACTIVE
campaigns, and with none left it clears itself — exactly as intended. `STOP_REASON`
is now `None`: `account_spend_limit_reached` was the right reason on 29 Sep and is
the wrong one now, because the limit was cleared and the ads served. The page
instead says, in its own words, that every quiz campaign is paused at Meta and
this is a deliberate stop rather than a delivery problem. Do not hand-write
either note.

Lifetime account spend is now **₹15,021.60** (was ₹14,406.77).

**The quiz has not moved for the ninth refresh running** — exactly ₹6,692.70 and
1,019 leads, with not even a late-attributed lead shifting. All three quiz
campaigns read PAUSED and none has spent since 21 September, thirteen days ago.
They also read PAUSED on 20 and 21 September *while spending*, so do not assume a
PAUSED status means a campaign is finished — always re-pull the days, and re-pull
the days either side of the last snapshot too. The page works all of this out
from `CAMPS` status and the `DAILY` rows and says it in its own words — do not
hand-write a note about it.

**The Clinic picked up a ninth purchase, back-dated to 25 September.** It now
reads ₹6,723.51 for **9 purchases at ₹747.06** (was ₹6,377.06 and 8). The extra
sale was not on the new October day: Meta late-attributed it to the 25th, a day
that had already been published with zero. **The day-rows-versus-ad-rows check is
what caught it** — ad level said 9, the day rows summed to 8 — which is the whole
reason that assertion exists. Re-pull settled days; do not trust that a published
day is final.

**Dream Bat is at ₹1,605.39 over four days and still has no purchase**: 252
landing page views, 37 adds to cart, 15 site leads, zero sales. That is the quiz
funnel's old cart problem showing up again on a campaign that does not touch the
quiz, and it is now a ₹1,600 question rather than a ₹1,300 one. A third creative
appeared on it — **Dream · Carousel · 3 angles**, ₹304.73 for 3 leads — and it
did not sell either.

Non-quiz spend on this account is now **₹8,328.90**, well past the ₹6,692.70 the
quiz has ever spent. When the quiz is dark, this report describes a shrinking
share of what the account is doing — worth saying to the client rather than
letting the page look idle.

**The shop keeps selling with no advertising behind it.** The store pixel
recorded **9 Purchases and 9 AddPaymentInfo on 2 Oct** and 3 more Purchases on
3 Oct, on days the ad account spent nothing at all. Two things to flag rather
than explain:

- **29 September carries a 239-event AddToCart spike**, 231 of it inside two
  hours (96 in one, 135 in the next), on a day with no ad spend and only 350
  PageViews. That is not shopper behaviour; it looks like a crawler or a feed
  job hitting the cart endpoint. It is left in `PIXEL` as recorded, but do not
  read it as demand.
- 29 Sep was published as a part-day (83 PageViews) and has settled at 350 —
  the usual reason the last `PIXEL` row is never a headline.

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
