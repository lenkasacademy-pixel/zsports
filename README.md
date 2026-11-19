# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 19 Nov 2026: delivery has resumed and the account is busier than it
has ever been.** The account spending limit that stopped everything on 26 Sep is no
longer biting — spend restarted on 1 Oct and has run every day since 2 Nov, taking
**₹1,856.69 on 18 Nov**, its biggest day on record. `STOP_REASON` is therefore set
back to `None`, and the page's delivery-stop note clears itself.

Lifetime account spend is now **₹53,419.38 across 73 days** (8 Sep – 19 Nov) over
**nine campaigns, all of which have now spent**. 19 Nov is the live part-day
(₹354.00 when pulled) and is excluded from every headline.

**Air Light is now the account.** Two Air Light purchase campaigns have taken
**₹30,660.97 for 59 purchases — ₹519.68 each**, which is more than everything else on
the account put together and by far the best cost per sale it has produced. One of
them (`Air Light · Purchase (Copy)`, ₹1,500/day) is the only ACTIVE campaign and is
spending now; the original is paused at ₹21,613.12 for 31 sales. `Air Light - Hook1`
is the creative carrying it in both: ₹17,412.18 for 25 sales in the first campaign,
₹8,068.56 for 27 in the second.

**The Dream Bat cart problem is still there, and now it has a price.** Across both
Dream campaigns — the original Direct Sales and the new `No AI Bat Test` — the group
has spent **₹5,929.98 for 2 purchases, ₹2,964.99 each**, against 170 adds to cart.
It finally has sales where it previously had none, but the gap between carts and
checkouts is the same gap the quiz funnel had, on a campaign that never touches the
quiz. This is the thing on the account most worth fixing.

**Z-Bat Clinic closed at ₹10,135.73 for 11 sessions (₹921.43 each)** and is paused.
Nine new `Reel n Hook x v2` creatives ran on it in October; none beat the original
`Video 4 · Copy 3 Local offer`, which still carries 9 of the 11 sales. Its copy
campaign spent **₹837.52 and produced no session at all** — note that the page
previously said that campaign "was built and never switched on", which is no longer
true; that sentence is now derived from the data rather than hand-written.

**All three quiz campaigns remain PAUSED and have not spent since 21 September** —
the ninth refresh running at exactly ₹6,692.70 and 1,019 leads, with not even a
late-attributed lead moving. They are dark 59 days. At ₹6.57 a lead the quiz is still
the cheapest thing the account has ever bought, and it is switched off. The page works
this out from `CAMPS` status and the `DAILY` rows and says it in its own words — do not
hand-write a note about it.

**Non-quiz spend is now ₹46,726.68**, seven times what the quiz has ever spent. This
report is overwhelmingly a report about selling bats, not about the quiz.

**One historical figure moved.** Meta late-attributed a purchase into Z-Bat Clinic's
**25 Sep** (0 → 1) some time after publication, which is why the clinic's lifetime
purchases read 11 rather than 10. It was caught by the day-rows-versus-ad-rows
assertion, not by eye — keep that check.

## Every campaign is tracked (changed 25 Sep 2026; 9 campaigns as of 19 Nov)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** All six
campaigns on account `1811409506889048` are now first-class: `CAMPS_ALL`,
`DAILY_ALL` (a row per campaign per day) and `ADS_ALL` (a row per ad) drive a new
account section at the top, and every campaign gets its own creative table.

**Leads and purchases are never blended.** The account now sells **four** different
things — quiz leads, ₹199 clinic sessions, Dream bats and Air Light bats — so each
group is priced in its own currency and there is deliberately no single account-wide
"cost per result". `CAMPS_ALL[i][5]` says which result kind a campaign reports, and
`CAMPS_ALL[i][2]` the product group. **Adding a product line means adding one entry to
`GROUPS`** (key, display name, what it sells, and the singular/plural result noun);
`airlight` was added on 19 Nov for exactly this reason. Campaign indexes are
**append-only** — `DAILY_ALL` and `ADS_ALL` reference them by position.

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
| `CAMPS_ALL` | `[id, name, group, status, daily budget, result kind, lifetime reach]` |
| `DAILY_ALL` | `[campIdx, date, spend, impressions, reach, link clicks, lpv, cart adds, purchases, leads]` |
| `ADS_ALL` | `[name, campIdx, status, spend, impressions, reach, clicks, lpv, cart, purchases, leads]` |

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

**The 28-day lookback is a hard wall, and it has now been hit.** `PIXEL` still ends
**29 Sep 2026**, because this refresh ran on 19 Nov and the API will not return
anything before 22 Oct. The days from 30 Sep to 21 Oct are **gone and cannot be
recovered**. Appending only the last 28 days would leave a three-week hole that the
page would draw as zero traffic, which is worse than a short series, so `PIXEL` was
left at its September window. The `pixel-note` now **states the span it covers and
says the rest cannot be retrieved**, so September totals can no longer read as if
they were the whole period.

If the store-pixel series matters going forward, this report needs either a refresh
cadence inside 28 days or a second array for a recent window — that is a structural
decision, not a refresh, and it has deliberately not been made here.
