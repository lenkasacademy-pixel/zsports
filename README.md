# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 23 Oct 2026: the account is delivering again, and the quiz is still
dark.** The account spending limit that stopped everything on 26 Sep has been
lifted. Delivery resumed with ₹614.83 on **1 Oct**, paused again 2–8 Oct, and has
run every day from **9 Oct to 23 Oct**. Lifetime spend is now **₹25,833.13**, up
from ₹14,406.77 at the last snapshot. `STOP_REASON` is back to `None` and the
page's own stop-detection has cleared itself — nothing about the stop is
hand-written, so it disappeared when the data changed.

**A seventh campaign has appeared and it is the only one running.** `Air Light ·
Purchase · India · ₹1000/day · Oct 2026` (`120253356448770067`, ACTIVE, ₹700/day)
started on **14 Oct** and is the sole live line. It has taken **₹6,860.28 for 12
purchases (₹571.69 each)**, on 1,018 landing page views and 108 adds to cart —
the best purchase economics on the account. Its five hooks are very unequal:
`Hook 1` alone is ₹5,058.67 and 9 of the 12 purchases. It is appended to
`CAMPS_ALL` as index 6 and given its own `airlight` group.

**Dream Bat finally converted, then stopped.** It closed at ₹2,981.94 with
**1 purchase**, 72 adds to cart and 24 site leads, and is now PAUSED. The old
note that it had "not one sale yet" is gone — the page works that sentence out
from the data, so it corrected itself.

**The clinic is paused too.** It finished at ₹9,298.21 for **11 purchases**
(₹845.29 each, against ₹797.13 at eight purchases last time). It ran 1 Oct and
9–12 Oct on a new set of nine `Reel … v2` creatives before stopping.

**All three quiz campaigns remain PAUSED and have not moved one paisa** —
₹6,692.70 and 1,019 leads for the ninth refresh running, with no late-attributed
leads either. `DAILY`, `REELS`, `PLACE` and `AGE` are therefore **byte-identical**
to the last cut; only the trailing zero days were extended to the new `SNAP_DAY`.

**The quiz is now a minority of the account.** Non-quiz spend is **₹19,140.43**
against the quiz's ₹6,692.70 — the quiz is 25.9% of everything this account has
ever spent, down from 46% at the last snapshot. The report describes a shrinking
share of what the account is doing; worth saying to the client rather than
letting the page look idle.

**One pixel anomaly to look at.** The store pixel recorded **239 AddToCart events
on 29 September**, a day with zero ad spend, against 3–66 on every other day in
the series. The 29 Sep row was published as a part-day (the old snapshot was
taken at 11:10 am IST that day, so it held only 11 of the day's 24 buckets);
re-pulling the full day is what surfaced it. It may be a bot or a site fault, and
it is not ad-driven. Flagged, not corrected — the number is what Meta returns.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** All **seven**
campaigns on account `1811409506889048` are now first-class: `CAMPS_ALL`,
`DAILY_ALL` (a row per campaign per day) and `ADS_ALL` (a row per ad) drive a new
account section at the top, and every campaign gets its own creative table.
**Nothing on the page counts campaigns by hand** — the intro sentence reads
`CAMPS_ALL.length`, and which campaigns show a blank result is worked out from
spend and results rather than listed by index, so adding the seventh campaign and
Dream Bat's first sale both corrected themselves.

**Leads and purchases are never blended.** The account sells three different
things — quiz leads, ₹199 clinic sessions, and bats — so each group is priced in
its own currency and there is deliberately no single account-wide "cost per
result". `CAMPS_ALL[i][5]` says which result kind a campaign reports. There are
now **four** `GROUPS` (quiz, clinic, dream, airlight) but still three things sold:
`ACCT_RES_KINDS` counts distinct `sells` strings, not groups, so Dream Bat and Air
Light both selling bats direct does not inflate it to four.

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

**The 28-day lookback is now binding.** Rows before the lookback window can no
longer be re-pulled, so `PIXEL` is append-and-amend: keep the existing rows for
days older than 28 days and replace only the days the API still serves. On the
23 Oct refresh that meant keeping 8–28 Sep untouched and rebuilding 29 Sep
onward. Re-pulling the overlap is worth it — 29 Sep had been published as a
part-day and its real full-day figures are several times larger.

The hourly buckets come back in one response for a multi-day range, so a single
call covers the whole window; bucket them into IST days yourself rather than
making one call per day. An hour stamped at −07:00 belongs to IST day D when its
hour is ≥ 12 on D−1, or < 12 on D.
