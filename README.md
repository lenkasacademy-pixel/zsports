# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 1 Nov 2026: the account spent again all through October, and has
hit its account spending limit a second time.** Lifetime spend is now
**₹31,356.15**, up from ₹14,406.77 at the last snapshot. The one still-ACTIVE
campaign (Air Light, ₹700/day) reports delivery `off` with substatus
**`account_spend_limit_reached`**, and nothing has served on 1 November. The exact
limit value cannot be read through the API tools used here; check it in Ads
Manager. `STOP_REASON` is still set to that substatus — clear it to `None` once
delivery resumes.

**The activity log is no longer silent, and the page no longer claims it is.** The
hard-coded sentence "Nobody paused anything — the activity log shows no status or
budget change" was true in September and is false now. It has been replaced by a
`STOP_LOG` constant that carries what the log actually says:

- **27 Oct** — Air Light paused by hand (Abhijeet Lenka, Ads Manager iOS app)
- **28 Oct** — `Dream · No AI Bat Test · Purchase` created (ads MCP server) and run
- **31 Oct** — Air Light switched back on, that Dream campaign switched off
- **31 Oct** — three of Air Light's five ads (Hook2, Hook4, Hook5) set Inactive

Set `STOP_LOG = null` when the log genuinely has nothing in it; the page falls back
to the old sentence.

### Two new campaigns, and the first real purchases

**`Air Light · Purchase` is the biggest thing that has ever run on this account** —
₹10,504.49 over 14–27 and 31 October for **14 purchases**, more than every other
campaign on the account has produced put together. One ad does most of it:
`Air Light - Hook1` took ₹7,464.70 for 9 of the 14.

**`Dream · No AI Bat Test · Purchase`** ran 28–31 October for ₹1,878.81, 1 purchase
and 12 site leads.

Both are appended to `CAMPS_ALL` at indexes 6 and 7. **Air Light is a fourth
product group**, so `GROUPS` gains an `airlight` entry — the account now sells four
different things and the page says so on its own.

**Dream Bat finally converted.** The old `Dream Bat · Direct Sales` campaign closed
at ₹2,981.94 with **1 purchase** — in September it was ₹1,337.01 with none, and this
README flagged the cart problem. One sale on ₹2,982 is still a cart problem, but it
is no longer a zero.

**The clinic closed at ₹9,298.21 and 11 purchases** and is now PAUSED. One of those
eleven is new: **Meta late-attributed a purchase to 25 September**, a day published
with zero. That single row is the only September figure that moved this refresh.

**All three quiz campaigns are still dark and have not moved at all** — nine
refreshes now at exactly ₹6,692.70 and 1,019 leads, and **41 days** since the quiz
last spent (21 September). Every quiz campaign is paused at Meta. The quiz-only
arrays (`DAILY`, `REELS`, `PLACE`, `AGE`) are byte-identical to the last snapshot
apart from `DAILY` gaining trailing zero rows out to `SNAP_DAY`; they were re-pulled
and confirmed, not assumed. The page works out the 41-day gap and the "deliberate
stop rather than a delivery problem" reading from `CAMPS` status and the `DAILY`
rows on its own.

**The quiz now describes a small and shrinking share of the account**: ₹6,692.70 of
₹31,356.15, under a quarter. Non-quiz spend is ₹24,663.45.

### The store pixel has a five-day hole that cannot be filled

`ads_get_dataset_stats` only looks **28 days** back. By the time this snapshot ran,
**30 September to 4 October had fallen out of reach permanently**. `PIXEL` therefore
runs 8–29 September, skips five days, and resumes 5 October–1 November.

A new `PIXEL_GAP` constant carries those two dates and **the page reports the hole
itself**, in the pixel note, so the period row is not read as a quiet spell in the
shop. This is the same idiom as `PLACE_DRIFT` and `STOP_REASON`: put the caveat in
the data and let the page say it.

**The lesson for scheduling:** this report cannot be left for a month. Store-pixel
days are lost 28 days after they happen and no later refresh can recover them. If
the quiz or the store matters, this needs refreshing at least every three weeks.

**The shop is selling well beyond the ads.** Over 5 Oct–1 Nov the pixel recorded
**86 purchases** while Meta attributes **18** to advertising across the whole month.
Most of what the shop sells is not coming from these campaigns.

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

**Assert against Meta's own campaign totals too, not just day-rows against
ad-rows.** On the 1 Nov refresh the clinic's day rows summed to 10 purchases while
its ad rows and Meta's campaign total both said 11 — the day-vs-ad check alone
would have passed once the stale day row was carried forward. The missing purchase
turned out to be a late attribution Meta had added to 25 September after
publication. Three-way agreement is what caught it.

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
| `PIXEL_GAP` | `[from, to]` store-pixel days lost to the 28-day lookback, or `null` |
| `STOP_LOG` | what the activity log says about the current stop, or `null` if silent |

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

**The 28-day lookback is a hard deadline, not a nuisance.** Days older than that
cannot be retrieved by any later refresh. Set `PIXEL_GAP` whenever a hole opens so
the page declares it; never let `PIXEL` present a gap as continuous.

**Events are stamped −07:00, but check the offset each time.** The window for this
refresh crossed the end of US daylight saving, so a response header can read
`-0800` even though every bucket in it is still stamped `-0700`. Bucket on the
**printed wall-clock hour** of each bucket, which is what the documented rule
("(D−1) 12:00 through D 11:00") actually describes, rather than converting through
a fixed offset.
