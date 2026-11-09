# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 9 Nov 2026: delivery has resumed, and the account is much bigger
than it was.** The account spending limit that held it dark through late September
has been raised or removed. Lifetime spend is now **₹38,967.12** against
₹14,406.77 at the last snapshot, `STOP_REASON` is back to `null`, and the page's
own stop-detection note has cleared itself.

**A new campaign now dominates the account.** `Air Light · Purchase · India ·
₹1000/day · Oct 2026` (`120253356448770067`) is the only ACTIVE campaign, carries a
₹1,200/day budget, and has taken **₹17,277.94 for 27 purchases — ₹639.92 each**.
That is more than the quiz has ever spent, and it is the first line on this account
to buy purchases at a sensible price. A second new campaign, `Dream · No AI Bat Test`
(`120253597607430067`), took ₹1,878.81 for 1 purchase and is paused. A third,
`Z-Bat Clinic · Parel ₹199 Session Sales – Copy` (`120252832409690067`), spent
₹837.52 with no result. All three are appended to `CAMPS_ALL` after the existing
six, so no historical row re-labels itself. Air Light gets its **own** entry in
`GROUPS` — it is a different bat from Dream Bat, and the account's rule is that
results are never blended.

**The quiz is still frozen.** All three quiz campaigns read PAUSED and none has spent
since 21 September: nine refreshes now at exactly **₹6,692.70 and 1,019 leads**, with
not even a late-attributed lead moving. Because of that `REELS`, `PLACE` and `AGE` are
**unchanged** this refresh — they are quiz-only and the quiz has not moved. `DAILY` was
extended with zero rows so its last row is still `SNAP_DAY`. The quiz campaigns also
read PAUSED on 20 and 21 September *while spending*, so do not assume a PAUSED status
means a campaign is finished — always re-pull the days, and re-pull the days either
side of the last snapshot too.

**The clinic and Dream Bat both moved while the report was stale.** The clinic is now
at ₹9,298.21 and **11 purchases** (was ₹6,377.06 / 8); Dream Bat is at ₹2,981.94 with
24 site leads and **1 purchase** — its first, after the cart problem flagged at the
last snapshot. Both are now PAUSED.

**Non-quiz spend is now ₹32,274.42 of the account's ₹38,967.12 — 83%.** The quiz
sections below describe a shrinking minority of what the account does; the account
view at the top is the part worth reading first.

**One reconciliation note.** Air Light is the one campaign still delivering, and its
day rows and its ad rows both sum to ₹17,277.94 while the campaign-level lifetime
read returns ₹17,275.53 — a **₹2.41 part-day difference**. Everything else matches to
the paisa. `ACCOUNT_SPEND` uses the day-row figure so the page's totals agree with
its own table.

**The campaign count is no longer written out by hand.** The masthead, the account
intro and the table's total row used to say "six"; they now read `CAMPS_ALL.length`,
so adding a campaign cannot leave a stale number behind.

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

**The 28-day cap is destructive, so refresh at least monthly.** At the 9 Nov 2026
refresh the previous `PIXEL` window (8–29 Sep) was already out of reach, and
30 Sep – 12 Oct can no longer be fetched at all. `PIXEL` was therefore **replaced**
with the contiguous 28 days the API still returns (13 Oct – 9 Nov) rather than
appending a block with a 13-day hole in the middle — the table's `Period` row totals
every row it is given, so a gap would have been silently folded into it. The
September rows remain in git history if anyone wants them back. If this report goes
more than 28 days without a refresh again, that month of store-pixel history is
simply lost.

A new `Search` event has appeared on the dataset (3 occurrences in the current
window). It is not in `EV` and is not carried; add it if it gains volume.
