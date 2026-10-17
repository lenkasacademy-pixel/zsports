# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 17 Oct 2026: delivery has resumed, and the account is on a new
campaign.** The account spending limit that held it dark from 26 Sep has been
lifted — ₹614.83 went out on 1 Oct (Clinic + Dream Bat), nothing on 2–8 Oct, and
it has run every day since 9 Oct. `STOP_REASON` is now `null` and the page's own
stop note has cleared itself on its own. Lifetime account spend is
**₹21,466.06** across 40 days, **₹7,059.29 of it in October**.

**A seventh campaign, `Air Light · Purchase` (`120253356448770067`), started on
14 Oct** and is the only ACTIVE line on the account: ₹2,493.21 on a ₹700/day
budget, 13,556 impressions, reach 8,889, **3 purchases at ₹831.07 each**, spread
over five `Air Light - Hook1`…`Hook5` creatives. It sells a different bat from
Dream Bat, so it is its own group (`air`) and is priced on its own — the account
now shows **four** result lines, not three.

**Dream Bat finally sold something.** It is at ₹2,981.94 over seven spending days
with **its first purchase**, so ₹2,981.94 a sale. It also now reads PAUSED. The
cart problem flagged on 29 Sep (adds to cart but no sale) is no longer strictly
true, but one sale on ₹2,982 is still the weakest line on the account.

**The Clinic is the account's biggest spender**, ₹9,298.21 (from ₹6,377.06) for
**11 ₹199 sessions at ₹845.29**, now PAUSED with its daily budget cut ₹600 → ₹500.
Nine new `v2` creatives were added (`Reel 1/2/3` × `Hook A/B/C`), but
`Video 4 · Copy 3 Local offer` still carries ₹6,739.14 of the ₹9,298.21 and nine
of the eleven sessions.

**All three quiz campaigns are still dark and still read exactly ₹6,692.70 and
1,019 leads** — nine refreshes unchanged now, and 26 days since the last spending
day (21 Sep). All three read PAUSED, so this is a deliberate stop rather than a
delivery problem. They also read PAUSED on 20 and 21 September *while* spending,
so do not assume a PAUSED status means a campaign is finished — always re-pull the
days, and re-pull the days either side of the last snapshot too. The page works all
of this out from `CAMPS` status and the `DAILY` rows and says it in its own words —
do not hand-write a note about it.

Non-quiz spend on this account is now **₹14,773.36 against the quiz's ₹6,692.70**,
more than twice it. When the quiz is dark, the quiz-only sections below the account
view describe a shrinking share of what the account is doing; the account view at
the top is the part worth reading to the client.

**The store pixel restated seven days** inside the 28-day lookback (20, 21, 23, 24,
25, 27 and 29 Sep), the largest being 20 Sep's page views 1,504 → 1,748 and 29 Sep's
83 → 350. Rows older than the lookback floor are kept exactly as published, because
they can no longer be re-pulled.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** All seven
campaigns on account `1811409506889048` are now first-class: `CAMPS_ALL`,
`DAILY_ALL` (a row per campaign per day) and `ADS_ALL` (a row per ad) drive a new
account section at the top, and every campaign gets its own creative table.

**Leads and purchases are never blended.** The account sells four different
things — quiz leads, ₹199 clinic sessions, Dream Bats and Air Light bats — so each
group is priced in its own currency and there is deliberately no single
account-wide "cost per result". `CAMPS_ALL[i][5]` says which result kind a
campaign reports, and `CAMPS_ALL[i][2]` which group it is priced in. **A new
product gets a new group**, not a line folded into an existing one: Air Light was
added as `air` on 17 Oct for exactly that reason.

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

**Assert over the whole campaign, not just the days you added.** On 17 Oct the
purchase check failed with the Clinic's day rows at 10 against 11 at ad level.
Nothing was wrong with the pull: Meta had restated **25 September** from 0
purchases to 1, three weeks after that day settled. Re-pulling the campaign's
entire daily history in one call (13 Sep – 17 Oct) showed which day had moved.
Had the check covered only the new October rows, a restatement in the middle of
the history would have been invisible — and the headline would have read 10
sessions against a creative table showing 11.

**No sentence may count anything by hand.** The seventh campaign broke four
hand-written counts at once — "Six campaigns have run", "All six", "6 campaigns"
in the masthead, "the three quiz campaigns" — and Dream Bat's first sale broke the
blanks note into "Dream Bat · Direct sales has ." All five now derive from
`CAMPS_ALL` (there is a `nWord` word-number helper and a `listOf` joiner for it).
If a refresh ever makes you want to reword a sentence, the sentence is the bug.

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
