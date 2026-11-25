# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 25 Nov 2026: the account is running again, and Air Light is carrying
it.** The account spending limit that had it dark on 29 Sep was lifted — delivery
resumed **1 October** and has barely stopped since. Lifetime spend is now
**₹62,809.94** across 79 days, up from ₹14,406.77 at the last snapshot. `STOP_REASON`
is back to `null`; the page works the rest out from `CAMPS_ALL` status and `DAILY_ALL`.

**Eleven campaigns have now run on the account, not six.** Five are new since the last
refresh:

| Campaign | Ran | Spend | Purchases | Each |
|---|---|---|---|---|
| Air Light · Purchase · Oct | 14 Oct–13 Nov | ₹21,613.12 | 31 | ₹697.20 |
| Air Light · Purchase (Copy) | 13–23 Nov | ₹10,476.87 | 29 | **₹361.27** |
| Air Light · Purchase (Copy 2) | 21–25 Nov, **LIVE** | ₹7,751.89 | 16 | ₹484.49 |
| Dream · No AI Bat Test | 28 Oct–14 Nov | ₹2,948.04 | 1 | ₹2,948.04 |
| Air Light · Bid cap test ₹340/₹320 | 20–22 Nov | ₹209.65 | 2 | ₹104.83 |

**Air Light is the only thing on this account that has ever sold at volume**:
₹40,051.53 across its four campaigns for **78 purchases at ₹513.48 each**. Against
that, the clinic has taken ₹10,135.73 for 11 ₹199 sessions (₹921.43 each) and the two
Dream Bat campaigns ₹5,929.98 for **2 sales** (₹2,964.99 each). The Dream cart problem
flagged on 29 Sep never resolved — a second Dream campaign was built to test the
proposition without the AI-bat angle and it bought exactly one sale on ₹2,948.04.

A fourth product group, `airlight`, was added to `GROUPS` for this. Dream bats and Air
Light bats are both bats sold direct, but they are different products at different
prices and blending them would hide the whole finding.

**The quiz is still frozen, for the ninth refresh running.** ₹6,692.70 and 1,019 leads,
unchanged to the paisa; its last spending day remains 21 September. Every quiz array
(`DAILY` values, `REELS`, `PLACE`, `AGE`, `H`) is byte-identical to the last snapshot
and still passes its checks. The quiz now describes ₹6,692.70 of a ₹62,809.94 account
— about one rupee in ten.

**The part-day moved three times while this refresh was being built.** Air Light ·
Purchase (Copy 2) read ₹7,746.03, then ₹7,746.72, then ₹7,751.89 on three calls minutes
apart. 25 Nov is `SNAP_DAY` and is out of every headline, which is the point.

### The quiz chart is now 65 empty rows — a human should decide

Holding the invariant (`DAILY` must run to `SNAP_DAY`) means the quiz day chart now
renders 11 days of delivery followed by **65 rows of ₹0.00**. Nothing is wrong with it;
it is the literal truth. But it reads badly, and it is a judgement call, not a data
question. Two options, neither taken here:

- leave it, and let the dead tail say plainly that the quiz has been off for two months;
- end the quiz `DAILY` at 21 Sep and let `PART` be null (the tiles already handle a null
  part-day), freezing the quiz section to its own window while the account section keeps
  rolling.

Do not "fix" it by dropping the invariant on a live funnel — it is what stops a part-day
setting a headline number.

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

**Hard-coded counts are a recurring bug here.** Four places said "six" or "All six" or
named `CAMPS_ALL[4]`/`[5]` by index when they meant "the campaigns with no result yet".
They were all correct when there were six campaigns and all wrong at eleven. They are
now computed from `CAMPS_ALL`. If you add a campaign and the page still reads right
without you touching prose, that is the design working — check it anyway.

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

**`PIXEL` still stops at 29 Sep, deliberately.** Two things block extending it, and
both need a decision rather than a guess:

1. **The 28-day lookback cannot bridge the gap.** The dataset API reaches back only 28
   days, so 30 Sep – 28 Oct can never be rebuilt. Appending the recent window would
   leave a 29-day hole in the middle of the table and a `Period` total summed across
   it, which would be worse than stopping. The page now says this itself: `pixel-note`
   compares the last `PIXEL` date with the last day on the page and explains the gap
   when there is one.
2. **The stamp offset changed.** Events were stamped −7:00 when the IST bucket rule was
   verified. From **1 Nov 2026 the dataset stamps −8:00** (US Pacific left daylight
   time), so "(D−1) 12:00 through D 11:00" no longer describes one IST day across the
   whole window. Re-verify the rule on both halves before adding a single new row.

For what it is worth, the store pixel is busy: over the 28 days to 24 Nov it recorded
**34,966 page views and 218 Purchase events**, against 91 purchases attributed to ads by
Meta over the whole 8 Sep – 25 Nov window. The shop sells well beyond what the ads claim.
