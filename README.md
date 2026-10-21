# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling (8 Sep – 21 Oct 2026) | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 21 Oct 2026: delivery has resumed, and the account spending limit
is no longer biting.** The stop reported at the last refresh
(`account_spend_limit_reached`, dark since 26 Sep) is over: `STOP_REASON` is now
`null` and the page's own stop-detector clears itself, because the one ACTIVE
campaign is delivering on the snapshot day.

**A new campaign is carrying the account: `Air Light · Purchase · India`**
(`120253356448770067`), ₹700/day, ACTIVE, created in October. It has taken
**₹5,616.88 over 8 days for 10 purchases — ₹561.69 each**, on five creatives
(`Air Light - Hook1` … `Hook5`). Hook1 alone is ₹4,071.75 of that and 7 of the
10 purchases. This is now the best-performing purchase line the account has had.

**The clinic restarted, then paused.** `Clinic · Parel ₹199 sessions` is at
**₹9,298.21 for 11 sessions (₹845.29 each)**, up from ₹6,377.06 and 8 sessions,
and it ran nine new creatives in October (`Clinic ₹199 · Reel 1/2/3 Hook A/B/C v2`)
alongside the original `Video 4 · Copy 3 Local offer`. It is PAUSED as of 12 Oct.

**Dream Bat finally converted — once.** It closed at **₹2,981.94 for 1 purchase**,
72 adds to cart and 24 site leads. The cart problem flagged last time was real but
not total; at ₹2,982 a sale it is still the worst line on the account, and it is
now PAUSED.

**The quiz has not moved at all.** Nine refreshes now at exactly **₹6,692.70 and
1,019 leads**, with not even a late-attributed lead. All three quiz campaigns read
PAUSED and none has spent since **21 September — 30 days dark**. The page works
that out from `CAMPS` status and the `DAILY` rows and says it in its own words; do
not hand-write a note about it.

Account lifetime spend is now **₹24,589.73** (was ₹14,406.77). Non-quiz spend is
₹17,897.03 of that — **the quiz is now well under a third of what this account
does**, which is worth saying to the client rather than letting the page look like
a quiz report.

**The store pixel kept recording throughout**, including during the dark stretch,
and it is still ahead of the Meta lead count because it counts all site traffic.

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

**Two hand-written counts were removed on the 21 Oct 2026 refresh.** The masthead
said `· 6 campaigns ·` and the account intro began `"Six campaigns have run on this
account"` — both frozen literals, and both wrong the moment a seventh campaign
appeared. They now read off `CAMPS_ALL.length` (with a `WORDS` table so the prose
still spells the number out). If you add a campaign, check nothing else counts them
by hand.

**A new product line needs a `GROUPS` entry.** `CAMPS_ALL[i][2]` is the group key,
and a campaign whose key is missing from `GROUPS` still appears in the account
table but silently drops out of the KPI tiles and the "N of them with any spend"
count. `Air Light` was added as a fourth group (`airlight`) rather than being
folded into `dream`: both sell bats direct, but blending them would have hidden
₹561.69 a sale behind ₹2,981.94 a sale. **`GROUPS[0]` must stay the quiz** — the
quiz-only sections below the account view read it by index.

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
