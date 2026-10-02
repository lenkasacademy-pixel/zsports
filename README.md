# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 2 Oct 2026: delivery came back on 1 October, then somebody paused
the whole account that evening.** The spend limit that stopped the account on
26 September is no longer biting — the Clinic and Dream Bat both served on 1 Oct
(₹346.35 and ₹267.76, ₹614.11 between them) after four dark days. At **8:18 pm
IST on 1 October both were set to PAUSED by hand** (`updated_time` on each
campaign). Every campaign on the account now reads PAUSED with delivery
`off` / `off`, and the `account_spend_limit_reached` substatus is gone. Lifetime
account spend is ₹15,020.88.

`STOP_REASON` is therefore back to `None`. **Do not hand-write a stop note**: the
page's delivery-stop detection requires at least one ACTIVE campaign, so with
nothing active it clears itself, which is exactly what it did. If a campaign is
switched back on and then serves nothing, the note returns on its own.

**The quiz is still frozen — nine refreshes now at exactly ₹6,692.70 and 1,019
leads**, with not even a late-attributed lead moving, and all three quiz
campaigns still PAUSED. They last spent on 21 September. They also read PAUSED on
20 and 21 September *while spending*, so do not assume a PAUSED status means a
campaign is finished — always re-pull the days, and re-pull the days either side
of the last snapshot too. The page works all of this out from `CAMPS` status and
the `DAILY` rows and says it in its own words — do not hand-write a note about it.

**Dream Bat still has no sale.** ₹1,604.77 over four spending days: 250 landing
page views, 37 adds to cart, 15 site leads, **still not one purchase**. The 1 Oct
day added ₹267.76, 28 page views, 1 cart add and 1 lead — and all of it went to
`Dream · Carousel · 3 angles`, which jumped from ₹36.35 to ₹304.11. That is the
quiz funnel's old cart problem appearing again on a campaign that does not touch
the quiz, and it is now four days deep. The Clinic is at ₹6,723.41 and **8
purchases** (₹840.43 each); its 1 Oct day bought 24 page views and no purchase.

Non-quiz spend on this account is now ₹8,328.18 against the quiz's ₹6,692.70 —
the quiz is down to 45% of what the account has ever spent. When the quiz is
dark, this report describes a smaller and smaller share of what the account is
doing; worth saying to the client rather than letting the page look idle.

**Worth knowing while the ads are off:** the store pixel still records activity
with no ad spend behind it, and on **29 September it recorded 239 adds to cart** —
96 in one hour and 135 in the next — on a day the account spent nothing at all
and the pixel saw only 314 page views. Against a normal day of 3–30 that is not a
shopping surge; treat it as a tracking or bot artefact until someone checks the
store. It is carried in `PIXEL` as returned, unsmoothed.

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
must reconcile against the quiz-only `DAILY` on both spend and leads; `REELS`,
`AGE` and `PLACE` must agree with `DAILY` as set out above; `ACCOUNT_SPEND` must
equal both the `DAILY_ALL` and the `ADS_ALL` spend totals and Meta's own account
lifetime; and the last `DAILY` row must be `SNAP_DAY`. Reach is excluded from
these checks on purpose.

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
an IST day D is the 24 buckets from (D−1) 12:00 through D 11:00 — i.e. the window
`[(D−1) 19:00 UTC, D 18:59:59 UTC]`. These counts cover all site traffic, not only
visitors from ads, so they run ahead of the Meta lead count — the page says so.

### The window is right, but the web events decay — do not rebuild the column

Re-pulling **27 and 28 September** on 2 October reproduced `AddToCart`,
`InitiateCheckout`, `AddPaymentInfo`, `Purchase`, `ClinicBookOpened`,
`ClinicSlotPicked` and `Schedule` **exactly, to the unit, on both days** — which is
what proves the bucket window above is correct. But the four top-of-funnel browser
events came back **lower** than the figures published on 29 September:

| | published 29 Sep | re-pulled 2 Oct |
|---|---|---|
| 27 Sep `PageView` | 448 | 396 |
| 27 Sep `ViewContent` | 116 | 93 |
| 28 Sep `PageView` | 352 | 326 |
| 28 Sep `ViewContent` | 80 | 79 |

Neither widening nor shifting the window by an hour accounts for it (the adjacent
buckets come back empty), so this is Meta restating `PageView` / `ViewContent` /
`BatFitStarted` / `Lead` downward as the day ages — the older day lost the most.

**So `PIXEL` rows are deliberately left as published and only new days are
appended.** Each row is then a capture taken at roughly the same age, which is the
honest time series. Rebuilding the whole column from one pull would look tidier
but would bake in an age gradient — older days understated against newer ones.
Replace a row only when it was a part-day that has since settled, as 29 September
was here. The earlier claim that this convention "is verified to reproduce the
historical figures" holds for the seven conversion and clinic events, not for the
four web events.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.
