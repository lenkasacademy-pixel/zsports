# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 10 Oct 2026: the account spending limit is resolved and the
account is delivering again — but the quiz is still dark.** The Z-Bat Clinic
campaign is ACTIVE with delivery `active` and has served on 1, 9 and 10 Oct; its
daily budget has been raised from ₹600 to **₹1,000**. `STOP_REASON` is therefore
set back to `null` and the page's stop note clears itself. Lifetime account
spend is now **₹15,732.77**.

**The quiz has not moved at all — nineteen days and counting.** All three quiz
campaigns are still PAUSED and still read *exactly* ₹6,692.70 and 1,019 leads,
unchanged across ten refreshes now, with not even a late-attributed lead. The
page works this out for itself from `CAMPS` status and the `DAILY` rows and says
it in its own words ("dark for 19 days since… a deliberate stop rather than a
delivery problem") — do not hand-write a note about it. Keep re-pulling the days
either side of the snapshot anyway: they still occasionally move.

**Dream Bat is now PAUSED, and it never made a sale.** It closes at ₹1,605.39
for 222 landing page views, **37 adds to cart, 15 site leads and no purchase at
all**. That is the quiz funnel's old cart problem appearing again on a campaign
that does not touch the quiz, and it is now a finished result rather than a
warning — worth putting to the client before any similar campaign is rebuilt.

**The clinic is the only thing selling, and nine new creatives just joined it.**
It stands at ₹7,434.68 for **10 purchases, ₹743.47 each**. `Video 4 · Copy 3
Local offer` still carries almost all of it (₹6,730.65, 9 purchases, ₹747.85);
nine new `Reel … v2` creatives were added and have taken ₹704.03 between them
for **one** purchase — `Reel 1 Hook C v2` at ₹272.89. Far too early to judge,
but the test is live.

**Meta attributed a purchase to 25 September after that day was published.** The
clinic's 25 Sep row went from 0 purchases to 1, which is what lifted the campaign
from 9 to 10. Re-pull the previous snapshot's last days: this is the second
refresh in a row where a settled-looking day moved.

**Worth knowing: the store pixel recorded 239 adds to cart on 29 September with
no ad spend behind them.** The quiz was dark and the clinic spent ₹0 that day,
yet `AddToCart` fired 96 times in one hour and 135 in the next. The shop is
doing something on its own — a promotion, a bot, or a tracking fault — and it is
the single biggest `ATC` day in the whole series. The page reports the counts as
data; nobody has explained this one.

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
  Meta's own breakdown rounding and is reported on the page (`PLACE_DRIFT`),
  signed as `DAILY − PLACE`. Still ₹0.01 this refresh.
- **Count drift of one between levels is Meta's, not yours.** This refresh the
  clinic campaign reports 571 link clicks at campaign and day level but 570
  across its ad rows. Every spend figure reconciles to the paisa, so there is no
  missing row — carry the difference and say so rather than editing a number to
  make the assertion pass.

### Store pixel

Pull with `ads_get_dataset_stats`, `aggregation: "event"`, unix
`start_time`/`end_time`; max lookback 28 days. Events are stamped **−07:00**, and
an IST day D is the 24 buckets from (D−1) 12:00 through D 11:00. These counts
cover all site traffic, not only visitors from ads, so they run ahead of the Meta
lead count — the page says so.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.

**The 28-day lookback is now cutting into the series, so `PIXEL` history is
write-once.** As of this refresh the earliest day still re-pullable is 12 Sep;
the 8–11 Sep rows can no longer be verified against Meta and must simply be
carried forward. Never rebuild `PIXEL` from scratch — splice new days onto the
existing rows.

**The convention is verified again this refresh.** Summing the hourly buckets
from 27 Sep 12:00 to 28 Sep 11:59 (−07:00) reproduces the published 28 Sep row
exactly on all eleven events (352 / 80 / 8 / 6 / 15 / 8 / 5 / 0 / 12 / 10 / 10).
Use `aggregation: "event"` and sum the hourly buckets yourself — `event_total_counts`
comes back truncated and split, and does not add up.
