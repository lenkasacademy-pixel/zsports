# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 29 Oct 2026: delivery has resumed and the account looks nothing like
it did a month ago.** The account spending limit that had everything dark since
26 September is no longer blocking: Meta billed again from **1 October**, and after a
week-long gap the account has run almost every day from 9 October onward. Lifetime
spend is now **₹29,199.22 across 52 days** (8 Sep – 29 Oct), against ₹14,406.77 at
the last snapshot. `STOP_REASON` is back to `null` and the page's stop note has
cleared itself — nothing was hand-removed.

**Two new campaigns arrived in October, and one of them is now the biggest spender on
the account.**

| New campaign | Ran | Spend | Result | Each |
|---|---|---|---|---|
| `Air Light · Purchase · India · ₹1000/day · Oct 2026` | 14–27 Oct, now PAUSED | ₹9,776.23 | 14 purchases | **₹698.30** |
| `Dream · No AI Bat Test · Purchase · India · ₹700/day · Oct 2026` | from 28 Oct, **ACTIVE** | ₹450.14 | 1 purchase | ₹450.14 |

`Air Light` is a different product from Dream Bat, so it gets **its own group** in
`GROUPS` (`key:"air"`) rather than being blended into Dream's cost per sale. The
account now sells **four** different things and the page says so on its own. Note the
campaign name says ₹1000/day but Meta reports `daily_budget` of ₹700 — the name is a
label, the budget field is the truth, and `CAMPS_ALL[6][4]` carries 700.

`Dream · No AI Bat Test` is the **only ACTIVE campaign on the account** and is two days
old. Both of its days are too thin to judge.

**The clinic came back and then stopped again.** `Clinic · Parel ₹199 sessions` ran on
1 Oct and again 9–12 Oct, taking it from ₹6,377.06 / 8 purchases to **₹9,298.21 /
11 purchases** (₹845.29 each) across 15 spending days, and is now PAUSED. It also
gained **nine new creatives** — `Clinic ₹199 · Reel 1/2/3 Hook A/B/C v2` — which
between them took ₹2,559.07 and booked 2 of the 11 purchases. The two zero-spend
`Video 4 · Copy 1/2` ads that used to sit under this campaign are no longer returned
at ad level and have dropped out of `ADS_ALL`; they never spent, so nothing is lost.

**Dream Bat finally sold something.** The old flag — "222 landing page views, 36 adds
to cart, 14 site leads, **still no purchase**" — is resolved. `Dream Bat · Direct sales`
ran on 1 Oct and 11–14 Oct, reaching **₹2,981.94, 72 adds to cart, 24 site leads and
1 purchase** (13 Oct) over 8 spending days, and is now PAUSED. One sale on 72 carts is
still a poor conversion and the cart problem is not fixed — but it is no longer zero,
so do not keep repeating that it is. The page works this out from the data; the
`acct-note` sentence that used to name Dream Bat as a permanent blank is now computed
and drops campaigns off that list as soon as they book a result.

**The quiz has not moved one rupee.** All three quiz campaigns read PAUSED and still
total exactly **₹6,692.70 and 1,019 leads** over 11 spending days — unchanged across
nine refreshes now, with not even a late-attributed lead moving. The last quiz spend
was 21 September; it has been dark **38 days**. `DAILY`, `REELS`, `PLACE` and `AGE` are
therefore byte-identical to the last cut apart from `DAILY` gaining explicit zero rows
out to `SNAP_DAY`. They still reconcile.

That does mean the quiz day table now carries 38 empty rows. It is honest — the
invariant is that the last `DAILY` row is `SNAP_DAY` — but if it reads badly to the
client, collapsing runs of dead days is a page change worth making deliberately
rather than by trimming the data.

**Non-quiz spend is now ₹22,506.52**, more than three times what the quiz has ever
spent. When the quiz is dark this report describes a smaller and smaller share of what
the account is doing — worth saying to the client rather than letting the page look
idle.

**One campaign is deliberately left out.** `OLD · DO NOT USE · Z-Bat Clinic Leads
(replaced by ₹199 Session Sales)` (`120252806446910067`) is ARCHIVED on the account and
has **never delivered** — ₹0, no impressions, ever. It is kept out of `CAMPS_ALL` so
the client-facing table does not carry a row labelled "DO NOT USE". Add it only if it
ever spends, **appended to the end** so no historical row re-labels itself. Archived
campaigns are **not returned by an unfiltered campaign listing** — the refresh filters
`effective_status IN [..., "ARCHIVED", "DELETED"]`, which is how this one was seen at
all.

**Worth knowing:** the store pixel records far more than the ads explain — 64,535 page
views and 120 orders across the period against 98 raw `Purchase` events in October
alone. The shop sells with and without ad spend behind it.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** Every
campaign on account `1811409506889048` that has ever delivered is first-class
(eight as of 29 Oct 2026): `CAMPS_ALL`,
`DAILY_ALL` (a row per campaign per day) and `ADS_ALL` (a row per ad) drive a new
account section at the top, and every campaign gets its own creative table.

**Leads and purchases are never blended.** The account sells four different
things — quiz leads, ₹199 clinic sessions, Dream Bats and Air Light bats — so each
group is priced in its own currency and there is deliberately no single
account-wide "cost per result". `CAMPS_ALL[i][5]` says which result kind a campaign
reports, and `CAMPS_ALL[i][2]` which `GROUPS` entry it belongs to.

**A new product gets a new group, not a share of an existing one.** `Air Light`
was added as its own `GROUPS` entry in October rather than folded into `dream`,
because two bat models at the same funnel stage still have their own economics —
blending them would have hidden a ₹698 sale behind a ₹2,982 one. Adding a group is
one entry in `GROUPS` plus the matching `key` on the campaign row; everything
downstream, including "sells N different things", counts itself.

**Nothing says how many campaigns there are.** The campaign count, the "All eight"
total row and the "Eight campaigns have run" sentence are all derived from
`CAMPS_ALL.length` through `word()`. They used to be the literal word "six" in
three places, which silently went wrong the moment October added two campaigns.

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
| `PIXEL_GAP` | IST days the 28-day pixel lookback can no longer reach |
| `SNAP_DAY` | the still-running day; `SNAP_TS` the snapshot stamp |
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
- Campaign sums equal the `level: "ad_account"` daily totals on spend **and**
  impressions, every day. On the 29 Oct refresh this was exact on all 51 settled
  days; only `SNAP_DAY` differed (+₹0.64, +13 impressions), which is Meta still
  settling the live day and is why that day sets no headline.
- **For a campaign that is still ACTIVE, build its day rows and its ad rows from
  one ad-level daily response.** Reading campaign-level and ad-level separately
  lets the part-day move between the two calls and the day-vs-ad assertion fails
  on a difference that is not an error — it cost three re-pulls on 29 Oct before
  the live campaign's figures agreed. Reach is the exception: it is non-additive,
  carried from the campaign read, and excluded from every check on purpose.

### Store pixel

Pull with `ads_get_dataset_stats`, `aggregation: "event"`, unix
`start_time`/`end_time`; **max lookback 28 days — this is a hard wall, not a
preference**. Events are stamped **−07:00**, and
an IST day D is the 24 buckets from (D−1) 12:00 through D 11:00. These counts
cover all site traffic, not only visitors from ads, so they run ahead of the Meta
lead count — the page says so.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.

**The 28-day wall has already cost two days, permanently.** An IST day needs
buckets starting at (D−1) 12:00 −07:00, so on the 29 Oct refresh the earliest
fully readable IST day was **2 October**: 30 Sep and 1 Oct fell outside the window
and can never be recovered. They are listed in `PIXEL_GAP`, and the page names them
in the funnel note itself rather than leaving an unexplained hole in the table. Add
to that list, never silently skip a date.

**The lesson: `PIXEL` must be topped up at least every four weeks.** The Meta ad
figures can be re-pulled from any point in history, but the dataset cannot. If this
report goes a month without a refresh again, more pixel days are lost for good.
