# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | 8 Sep – 18 Oct 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 18 Oct 2026: the account spending limit has been cleared and delivery
has resumed - on a new campaign.** `STOP_REASON` in `index.html` is back to `null`. Nothing is dark any
more: `Air Light · Purchase · India · ₹700/day · Oct 2026` started on
14 October and is the **only ACTIVE campaign on the account**. In five days it has taken
**₹3,196.72 for 6 purchases - ₹532.79 each**, on 482 landing page views and 52 adds
to cart. It is the cheapest purchase the account has bought from any campaign.

**Air Light is a fourth product line, so it needed a fourth `GROUPS` entry** (`key:"air"`).
Without it the campaign would have shown in the account table but been left out of every
group-level figure, and the page would have said the account sells three things when it
sells four. `CAMPS_ALL` is append-only, so Air Light is index 6.

| Group | Spend | Result | Each |
|---|---|---|---|
| AI Bat Fit Quiz | ₹6,692.70 | 1,019 leads | **₹6.57** |
| Z-Bat Clinic | ₹9,298.21 | 11 ₹199 sessions | ₹845.29 |
| Dream Bat | ₹2,981.94 | 1 sale | ₹2,981.94 |
| Air Light | ₹3,196.72 | 6 sales | ₹532.79 |

Account spend over 8 Sep - 18 Oct is **₹22,169.57 across 41 days**, of which the quiz is
only ₹6,692.70. **Non-quiz spend is now ₹15,476.87 - more than twice what the quiz has
ever spent.** The report describes a smaller and smaller share of the account; worth saying
to the client rather than letting the quiz sections look like the whole story.

**Dream Bat finally converted.** It is paused at ₹2,981.94 with **1 purchase** (and 454
landing page views, 72 adds to cart, 24 site leads). The cart problem flagged on 29 Sep is
still real - 72 adds to cart for one sale - but it is no longer true that it has never sold.
The page works this out itself: the "blank in the result column" sentence now drops Dream Bat
and names only the campaign that never switched on.

**The clinic got eight new creatives and they did not work.** It is paused at ₹9,298.21
and 11 sessions. Eight `Reel 1/2/3 × Hook A/B/C v2` ads ran from early October and took
₹2,559.07 between them for **1 purchase**; the original `Video 4 · Copy 3 Local offer`
still carries the campaign at ₹6,739.14 for 9 purchases (₹748.79 each). Worth putting
to the client before the v2 set is tried again.

**All three quiz campaigns read PAUSED and none has spent since 21 September** - eleven
refreshes now at exactly ₹6,692.70 and 1,019 leads, dark for 27 days, with not even a
late-attributed lead moving. They also read PAUSED on 20 and 21 September while spending, so
do not assume a PAUSED status means a campaign is finished - always re-pull the days, and
re-pull the days either side of the last snapshot too. The page works all of this out from
`CAMPS` status and the `DAILY` rows and says it in its own words - do not hand-write a note
about it.

**Worth knowing:** the store pixel still records sales with no ad spend behind them, and on
29 September it logged a **231-event AddToCart spike in two hours** with the account dark.
The shop sells on its own; the pixel counts all site traffic, not only visitors from ads.

### Generated sentences that broke on this data

Three sentences were written assuming the account would always have six campaigns and that
Dream Bat would never sell. They are now computed:

- the masthead campaign count and the account-table total row read `CAMPS_ALL.length`
  rather than the literal "six";
- the "blanks in the result column" sentence finds those campaigns by filtering on spend
  and results instead of naming `CAMPS_ALL[4]` and `CAMPS_ALL[5]` by index. With Dream Bat
  now selling, the old version rendered the broken fragment *"Dream Bat · Direct sales
  has ."*

This is still "no prose is written by hand" - the generator was fixed, not the output.

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

### Store pixel: the day convention is verified, and the last row is a part-day

`ads_get_dataset_stats` returns **hourly** buckets, so the IST day has to be assembled from
them: an IST day D is the 24 buckets from (D-1) 12:00 to D 11:00 at the −07:00 stamp. On the
18 Oct refresh this was checked by re-deriving the already-published, fully settled
**28 September** row and getting `[352, 80, 8, 6, 15, 8, 5, 0, 12, 10, 10]` back exactly.
Keep the convention.

**The 29 September row published on 29 September was a part-day** and was wrong once the day
closed: PageView 83 → 350 and AddToCart 3 → **239**, because the 231-event cart spike landed
after the 11:10 am snapshot. `PIXEL`'s last row is always a part-day for the same reason
`DAILY`'s is — re-pull it, never append past it.

One call covering the whole window exceeds the tool's output limit and spills to a file;
parse that file with python rather than transcribing it.
