# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | 8 Sep – 16 Oct 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 16 Oct 2026: the account is serving again, and there is a new
campaign on it.** The account spending limit that held delivery off from 26 Sep
has been lifted: the account spent on 1 Oct, went quiet again 2–8 Oct, and has
delivered every day since **9 Oct**. `STOP_REASON` is now `None` and the page's
own stop note has cleared itself, exactly as designed — nothing was hand-removed.

**A seventh campaign has appeared: `Air Light · Purchase · India · ₹1000/day ·
Oct 2026`** (`120253356448770067`), ACTIVE, ₹700/day, delivery `active`. It is
the only ACTIVE campaign on the account. It started on **14 Oct** and has taken
**₹1,745.27 for 3 purchases** across five creatives (Hook1–Hook5) in two and a
bit days. It sells bats direct, like Dream Bat, so it is filed in the `dream`
group — and because that group now carries two different bat campaigns, the
group's display name changed from "Dream Bat" to **"Direct bat sales"**. The
group's `sells` value ("bats, direct") was already right and is unchanged, and
the account still sells **3** different things, so `ACCT_RES_KINDS` is unmoved.

**Both of the campaigns that were ACTIVE at the last refresh are now PAUSED.**
Z-Bat Clinic closes at ₹9,298.21 for **11 sessions** (₹845.29 each) and Dream Bat
at ₹2,981.94. The clinic also picked up ten new `Reel … v2` creatives that had
not run at the last snapshot; its single old ad (`Video 4 · Copy 3 Local offer`)
still accounts for ₹6,739.14 of its spend and 9 of its 11 purchases.

**Dream Bat finally sold something.** It is at ₹2,981.94 over nine days: 454
landing page views, 72 adds to cart, 24 site leads and **1 purchase** — its
first, on 13 Oct, after eight refreshes at zero. That one sale broke a
hand-written sentence in the page (see *Refreshing it* below).

**All three quiz campaigns read PAUSED and none has spent since 21 September** —
**nine** refreshes now at exactly ₹6,692.70 and 1,019 leads, with not even a
late-attributed lead moving, and the quiz has now been dark for 25 days. They
also read PAUSED on 20 and 21 September while spending, so do not assume a
PAUSED status means a campaign is finished — always re-pull the days, and
re-pull the days either side of the last snapshot too. The page works all of
this out from `CAMPS` status and the `DAILY` rows and says it in its own words —
do not hand-write a note about it.

Non-quiz spend on this account is now **₹14,025.42** against the quiz's
₹6,692.70 — more than twice it. When the quiz is dark, this report describes a
smaller and smaller share of what the account is doing; worth saying to the
client rather than letting the page look idle.

**One thing for a human to look at: a cart-add spike with no advertising behind
it.** The store pixel recorded **239 AddToCart events on 29 September**, a day
with zero ad spend — and 231 of them land in just two hours (the −07:00 buckets
at 05:00 and 06:00, i.e. roughly 17:30–19:30 IST) on only **60 page views**. 135
adds to cart on 13 page views in one hour is not real demand; it looks like a
bot or a double-firing event. It is carried in `PIXEL` as Meta reports it, but
the funnel note will read oddly until someone checks the site. Nothing else in
the pixel series looks like this.

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
| `CAMPS_ALL` | `[id, name, group, status, dailyBudget, resultKind, reach]` — **append** new campaigns, never reorder |
| `DAILY_ALL` | `[campIdx, date, spend, impressions, reach, linkClicks, lpv, cart, purchases, leads]` |
| `ADS_ALL` | `[adName, campIdx, status, spend, impressions, reach, linkClicks, lpv, cart, purchases, leads]` |
| `ACCOUNT_SPEND` / `ALL_DAYS` | account total and the day spine, both covering the whole window |
| `STOP_REASON` | Meta's delivery-stop reason, or `null` once delivery resumes |

**The last `DAILY` row must be `SNAP_DAY`.** The page treats that day as still
accruing: it is shown separately, labelled "so far", and excluded from every
headline total and from the blended cost per lead. That is the guard against
Meta's ~48h revisions — a part-day must never set a headline number.

### Checks that must pass before publishing

- `REELS` totals equal `DAILY` totals on spend, impressions **and** leads.
- `AGE` totals equal `DAILY` totals on spend and leads.
- `PLACE` matches `DAILY` exactly on leads; a few rupees of spend drift is
  Meta's own breakdown rounding and is reported on the page (`PLACE_DRIFT`).

### Three hand-written sentences had to be derived

The README has always claimed that **every sentence is rendered from the
arrays**. Three places did not honour that, and the October data broke all
three. They are now computed, so the next refresh will not trip over them:

- **`"Six campaigns have run on this account"`** and **`"All six"`** in the
  account table's total row were literal words. There are now seven campaigns.
  Both read `n(CAMPS_ALL.length)`.
- **`acct-note`** named `CAMPS_ALL[4]` and `CAMPS_ALL[5]` by index and asserted
  "the two blanks in the result column". Dream Bat's first sale removed one of
  those blanks, which left the rendered sentence as *"… and Dream Bat · Direct
  sales has ."* — a dangling clause with no object. It now derives the list of
  campaigns with no result (never-delivered, and delivered-but-zero) and counts
  them, so a campaign that converts drops out of the sentence on its own.
- **`recon`** flatly asserted that every campaign's day rows and creative rows
  agree. They do not while a day is settling (see below). It now computes the
  mismatches and names them with the size of the gap.

### A campaign can disagree with itself on the part-day

`ADS_ALL` is lifetime and so includes the live part-day, and on that day Meta
can return different totals at ad level and campaign level. At this refresh
**Air Light's creative rows run ₹6.97 and 11 impressions ahead of its day
rows**. The gap is entirely in the part-day: for 14–15 Oct alone the ad rows sum
to ₹1,674.19 / 9,658 impressions, matching the campaign exactly. The page
reports the gap itself in the footer rather than hiding it, and every headline
already excludes the part-day. Do not "fix" it by adjusting a number — re-check
after the day settles.

### Store pixel

Pull with `ads_get_dataset_stats`, `aggregation: "event"`, unix
`start_time`/`end_time`; max lookback 28 days.

**The 28-day lookback means older `PIXEL` rows cannot be re-pulled** — at this
refresh anything before 19 Sep is out of range. Rows for 8–18 Sep are therefore
kept exactly as previously published and only 19 Sep onward was rebuilt. The
bucketing convention was re-verified on the overlap: **19, 22, 26 and 28
September reproduce the published numbers digit for digit**, which is the proof
the −07:00 convention is still right. The other overlap days moved upward as
late events landed (20 Sep gained 244 page views; 29 Sep was a part-day when
published and is now a settled day), so expect re-pulled days to grow a little
and never shrink. Events are stamped **−07:00**, and
an IST day D is the 24 buckets from (D−1) 12:00 through D 11:00. These counts
cover all site traffic, not only visitors from ads, so they run ahead of the Meta
lead count — the page says so.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.
