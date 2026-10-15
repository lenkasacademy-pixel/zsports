# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 15 Oct 2026: the account is delivering again, and there is a seventh
campaign.** The spending-limit block is gone — Meta has billed the account on every day
from 10 to 15 October (₹601.10, ₹958.44, ₹1,046.86, ₹982.96, ₹306.94, ₹1,063.04) — so
`STOP_REASON` is now `null` and the page's stop note has cleared itself. Delivery actually
restarted earlier than the relaunch: **1 October** took ₹614.83 (clinic ₹346.45, Dream Bat
₹268.38) before another six-day gap.

Lifetime account spend is now **₹19,983.61** across 38 days.

**What happened, from the activity log:**

| when | what |
|---|---|
| 9 Oct, 10:35–10:40 am | Nine new clinic creatives created (`Clinic ₹199 · Reel 1–3 Hook A–C v2`), the clinic campaign switched back on, and the two old `Video 4` copies deleted |
| 11 Oct, 12:37 pm | Clinic daily budget cut ₹1,000 → ₹500; Dream Bat switched back on |
| 12 Oct, 7:28 pm | Clinic paused |
| 14 Oct, 9:48–9:49 am | **New campaign `Air Light · Purchase · India · ₹1000/day · Oct 2026`** created with five ads (`Air Light · Hook 1–5`) |
| 14 Oct, 10:04 am | Dream Bat paused |
| 14 Oct, 5:10 pm → 10:23 pm | Air Light daily budget ₹1,000 → ₹2,000 → ₹1,500 → **₹700** |

**Air Light is the only ACTIVE campaign**, and on two days it is the best purchase line on
the account: ₹1,010.76 for **2 purchases at ₹505.38**, against the clinic's ₹845.29 and Dream
Bat's ₹2,981.94. Two days is two days — but it is also carrying the whole account now, on
₹700/day.

**The clinic's relaunch is not paying yet.** Its original creative, `Video 4 · Copy 3 Local
offer`, has ₹6,739.14 and **9 purchases at ₹748.79**. The nine new `v2` reels that replaced it
on the 9th have ₹2,559.07 and **2 purchases — ₹1,279.54 each**, and seven of the nine have
booked nothing at all. The campaign was paused again on the 12th. Worth asking whether the
swap was an improvement before it is switched on again.

**Dream Bat has its first sale.** It closed its second run at ₹2,981.94 for 454 landing page
views, 72 adds to cart, 24 site leads and **1 purchase** — the cart problem flagged in the
last refresh is not fixed, just no longer absolute. ₹2,981.94 a sale is not a rate to scale.

**All three quiz campaigns are still PAUSED and have not spent since 21 September** — now
24 days dark, still exactly ₹6,692.70 and 1,019 leads, with not even a late-attributed lead
moving. They also read PAUSED on 20 and 21 September *while spending*, so do not assume a
PAUSED status means a campaign is finished — always re-pull the days, and the days either
side of the last snapshot too. Meta revised **25 September up by one clinic purchase** after
it was published (8 → 9 purchases in the September rows), which is exactly why.

**Non-quiz spend is now ₹13,290.91 — twice what the quiz has ever spent** (₹6,692.70), or 67%
of the account. When the quiz is dark, the quiz-only half of this report describes a third
of what the account is doing; say that to the client rather than letting the page look idle.
The page works all of this out from `CAMPS_ALL` status and the `DAILY_ALL` rows and says it
in its own words — do not hand-write a note about it.

**The store is selling far beyond what the ads are credited with.** Over 1–14 October the
pixel recorded **34 Purchase events** while Meta attributed **5** to ads. The page already
says these counts cover all site traffic, not only ad visitors — but the gap is now the
loudest thing in the funnel table. 14 October is the biggest day in the whole pixel record:
2,082 page views and 1,337 ViewContent.

**One pixel row changed rather than being appended.** 29 September was published as a
part-day (83 page views); the full day reads **350 page views and 239 adds to cart** — an
add-to-cart spike on a day with no ad spend behind it at all. Worth a look if it matters.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** Every
campaign on account `1811409506889048` is first-class — seven of them now:
`CAMPS_ALL`,
`DAILY_ALL` (a row per campaign per day) and `ADS_ALL` (a row per ad) drive a new
account section at the top, and every campaign gets its own creative table.

**Leads and purchases are never blended.** The account sells four different
things — quiz leads, ₹199 clinic sessions, the Dream Bat and the Air Light bat —
so each group is priced in its own currency and there is deliberately no single
account-wide "cost per result". `CAMPS_ALL[i][5]` says which result kind a campaign
reports, and `CAMPS_ALL[i][2]` which product group it belongs to.

**A new product needs a new group.** `GROUPS` in the script is keyed on
`CAMPS_ALL[i][2]`, and a campaign whose group key is not in `GROUPS` falls out of
the tiles and the account totals silently. Air Light was added as a fourth group
(`airlight`) when its campaign appeared on 14 Oct. **Append to `CAMPS_ALL`, never
reorder it** — `DAILY_ALL[0]` and `ADS_ALL[1]` are indexes into it.

**Reach is carried, not derived.** `CAMPS_ALL[i][6]` is Meta's own de-duplicated
lifetime reach per campaign. It can never be obtained by adding the day rows up,
so the account table shows it per campaign and leaves the total blank.

The quiz keeps its own sections below the account view, because it is the only
funnel on the account with site events behind it — the placement, age and pixel
breakdowns exist for the quiz and nothing else. Those arrays (`DAILY`, `ADS`,
`PLACE`, `AGE`) stay quiz-only. `REELS`, `PLACE` and `AGE` are unchanged this
refresh because the quiz has not spent since 21 September; `DAILY` still gains a
zero row for every day up to `SNAP_DAY`, because the last `DAILY` row must be
`SNAP_DAY`.

**The page detects a delivery stop on its own.** If every ACTIVE campaign serves
nothing on the trailing days, the account section says so, names the budget those
campaigns are still carrying, and gives Meta's own reason when `STOP_REASON` is
set (otherwise it points at the payment method). It is computed
from `CAMPS_ALL` status and `DAILY_ALL`, so it clears itself when delivery
resumes — do not hand-write or hand-remove that note. It cleared itself on this
refresh. Explicit zero rows are added to `DAILY_ALL` for live campaigns on dead
days so the stop is visible as data rather than as a gap; the 26–29 September zero
rows from the spending-limit stop are kept as a record of it, and no new ones were
needed, because the only ACTIVE campaign is delivering.

**Nothing on the page is allowed to hardcode a count.** Two places did and were
fixed on this refresh: the masthead said “6 campaigns” and the account table footer
said “All six”, both now read `CAMPS_ALL.length`; and the footnote under the account
table named campaigns 4 and 5 by index to explain the blanks in the result column —
it now finds them (a campaign that never spent, or one that spent and has booked
nothing) so it stays true as the data moves. Dream Bat's first sale had already
made that sentence read “Dream Bat · Direct sales has .”

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
