# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | 8 Sep 2026 onward, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 31 Oct 2026: the account is serving again, and it is a different
account.** The spending limit that had it dark since 26 Sep was lifted — delivery
came back on **1 Oct** and has run most days since. `STOP_REASON` is now `null`,
and the page's stop note clears itself off the back of that plus `CAMPS_ALL`
status and `DAILY_ALL`; nothing about it is hand-written.

Lifetime account spend is **₹30,677.12** across 54 days (8 Sep – 31 Oct), more
than double the ₹14,406.77 at the last cut. **Two new campaigns opened in
October** and both are about selling bats, not the quiz:

| Campaign | Status | Ran | Spend | Purchases | Each |
|---|---|---|---|---|---|
| Air Light · Direct sales | **ACTIVE** ₹700/day | 14–31 Oct | ₹9,825.46 | 14 | ₹701.82 |
| Dream Bat · No AI Bat Test | PAUSED ₹700/day | 28–31 Oct | ₹1,878.81 | 1 | ₹1,878.81 |

**Air Light is now the biggest single line on the account** — ₹9,825.46, just
ahead of the clinic's ₹9,298.21 — and it is the only thing still ACTIVE. It runs
five hooks. `Air Light - Hook1` takes 74% of the budget and 9 of the 14
purchases at ₹804.79 each; **`Air Light - Hook3` is the one to watch**, 4
purchases on ₹1,203.08 at **₹300.77** — less than half Hook1's rate on an eighth
of the spend. Hook2 and Hook4 have taken ₹521.52 between them with no sale.

**Dream Bat finally recorded a purchase**, on 13 Oct, after the cart problem
flagged in the last two refreshes. It closed at ₹2,981.94 for that one sale (72
adds to cart, 24 site leads) and is now PAUSED. Its replacement, `Dream Bat · No
AI Bat Test`, has the same shape so far: ₹1,878.81, 41 adds to cart, 11 site
leads, **one** sale. Two campaigns, ₹4,860.75, two bats sold — worth putting in
front of the client before a third variant is built.

**The clinic ran again and then stopped.** It took ₹2,921.15 more across 1 Oct
and 9–12 Oct and closes at ₹9,298.21 for **11 sessions at ₹845.29** (it was 8 at
₹797.13). Ten new `Reel * Hook * v2` creatives went in; only `Reel 1 Hook C v2`
(₹1,131.14, 1 sale) and `Reel 3 Hook C v2` (₹133.49, 1 sale) converted, and the
original `Video 4 · Copy 3 Local offer` still accounts for 9 of the 11. It reads
PAUSED now.

**Meta late-attributed a clinic purchase to 25 September** — months after that
day was published. The campaign total said 11 purchases while the day rows added
to 10, and the gap only closed after re-pulling the September days. That is the
reconciliation assertion earning its keep: **re-pull the old days too, not just
the new ones**, and never append blind.

**The quiz has not moved at all.** Eight refreshes ago it stopped at ₹6,692.70
and 1,019 leads and it is still exactly there — no spend, and not one
late-attributed lead, since **21 September**: forty dark days. All three quiz
campaigns read PAUSED. So `DAILY`, `REELS`, `PLACE` and `AGE` are untouched this
refresh; `DAILY` only gains explicit zero rows out to `SNAP_DAY` so the stop
stays visible as data. The page works the forty days out from `CAMPS` status and
the `DAILY` rows and says it in its own words — do not hand-write it.

**Non-quiz spend is now ₹23,984.42 — 78% of the account.** This report's quiz
sections describe under a quarter of what the account is doing. Say that to the
client rather than letting the quiz sections look like the whole picture.

**A fourth product line.** `GROUPS` gained an `air` entry (Air Light bats, sold
direct, priced per sale). A campaign whose `CAMPS_ALL[i][2]` group key has no
`GROUPS` entry is **silently dropped from the account view** — that would have
hidden ₹9,825.46, a third of lifetime spend. Adding a product line means one
`GROUPS` entry; that is the only code change a new campaign can require.

**Three bits of hand-written prose had gone stale and are now computed:** the
masthead's "6 campaigns", the account intro's "Six campaigns", and the account
table's "All six" footer all read off `CAMPS_ALL.length`. The note explaining the
blank result column also named campaigns 4 and 5 by index and assumed there were
exactly two blanks; once Dream Bat recorded its sale that sentence rendered as a
truncated fragment ("… and Dream Bat · Direct sales has ."). It now works the
blanks out of the data and distinguishes a campaign that never ran from one that
spent without converting.

**The store pixel has a four-day hole: 30 Sep – 3 Oct.** `ads_get_dataset_stats`
serves 28 days of history and no more, and those days had aged out before this
snapshot was taken. They are **not recoverable**. The page computes the gap from
`PIXEL`'s own dates and says which days are missing and that the period row is
short by them — so the hole is disclosed rather than hidden. **This is the cost
of a refresh gap: run this report at least every 28 days or lose pixel days
permanently.**

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
| `CAMPS_ALL` | `[id, label, group, status, daily budget, result kind, lifetime reach]` |
| `DAILY_ALL` | `[campIdx, date, spend, impressions, reach, link clicks, lpv, cart adds, purchases, leads]` |
| `ADS_ALL` | `[name, campIdx, status, spend, impressions, reach, link clicks, lpv, cart adds, purchases, leads]` |

**Append to `CAMPS_ALL`, never reorder it** — `DAILY_ALL` and `ADS_ALL` index
into it. A new `group` key also needs a `GROUPS` entry in the script, or the
campaign vanishes from the account view without a word.

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
`start_time`/`end_time`; **max lookback 28 days, and that is a hard wall** —
`start_time` more than 28 days old is rejected, so any day that ages out between
refreshes is gone for good. The 30 Sep – 3 Oct rows are missing for exactly that
reason. The page derives the gap from `PIXEL`'s own dates and discloses it; do
not paper over it with zeros, and do not let the report go more than 28 days
without a refresh. Events are stamped **−07:00**, and
an IST day D is the 24 buckets from (D−1) 12:00 through D 11:00. These counts
cover all site traffic, not only visitors from ads, so they run ahead of the Meta
lead count — the page says so.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.
