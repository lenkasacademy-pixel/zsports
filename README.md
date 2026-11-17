# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

Snapshotted **17 November 2026, 8:15 am IST** (`SNAP_TS`), window **8 Sep – 17 Nov**,
with 17 Nov held out of every headline as the still-accruing day.

**Delivery has resumed and `STOP_REASON` is now `null`.** The September stop was real —
both live campaigns reported substatus `account_spend_limit_reached` — but the account
has spent on most days since 9 October and took ₹276.56 on the morning of the 17th. The
limit is no longer binding; nothing was hand-removed from the page, the stop note
cleared itself once `DAILY_ALL` showed delivery on the trailing days.

**Lifetime account spend is now ₹49,758.65**, against ₹14,406.77 at the previous
snapshot. Nearly all of the increase is one new product line.

| Campaign | Status | Spend | Result | Each |
|---|---|---|---|---|
| Air Light · Purchase | PAUSED | ₹21,613.12 | 31 purchases | ₹697.20 |
| Clinic · Parel ₹199 sessions | PAUSED | ₹9,298.21 | 11 purchases | ₹845.29 |
| Air Light · Purchase (Copy) | **ACTIVE** | ₹5,387.12 | 14 purchases | ₹384.79 |
| Quiz · Website Leads (Copy) | PAUSED | ₹3,744.61 | 759 leads | ₹4.93 |
| Dream Bat · Direct sales | PAUSED | ₹2,981.94 | 1 purchase | ₹2,981.94 |
| Dream · No AI Bat Test | PAUSED | ₹2,948.04 | 1 purchase | ₹2,948.04 |
| Quiz · Website Leads | PAUSED | ₹1,635.16 | 126 leads | ₹12.98 |
| Quiz · Add to Cart optimisation | PAUSED | ₹1,312.93 | 134 leads | ₹9.80 |
| Clinic · Parel ₹199 (Copy) | PAUSED | ₹837.52 | none | — |

**Air Light is the account now.** ₹27,000.24 across the two Air Light campaigns for
45 purchases, ₹600.01 each — more than half of everything the account has ever spent,
on a product the previous snapshot had never seen. The original ran 14 Oct – 13 Nov and
was paused the day its copy started; the copy is the only ACTIVE campaign and is
buying at ₹384.79 a purchase against the original's ₹697.20. That gap is the one
thing on this page worth acting on.

**The quiz has not moved at all.** Still exactly ₹6,692.70 and 1,019 leads, unchanged
across nine refreshes now — not even a late-attributed lead. `PLACE` and `AGE` were
re-pulled in full and came back identical to the figures already in the file, so the
quiz-only arrays are untouched except for the zero-day padding below. All three quiz
campaigns read PAUSED and the page says so in its own words.

**`DAILY` is now padded with zero rows out to `SNAP_DAY`.** The quiz last spent on
21 September, so 59 of its 71 day rows are empty. This is what keeps the invariant
(last row = `SNAP_DAY`) true, and it is what lets the page say "the quiz campaigns last
spent on 21 Sep … and have been dark for 57 days since" without anyone writing that
sentence. The cost is that the quiz spend tile is labelled "70 full days" next to a
correct "11 spending days" in the section intro. **If that reads badly to the client,
the alternative is to close the quiz sections off at 21 Sep rather than pad — that is a
judgement call, not a data fix.**

**Dream Bat finally booked a sale**, one, at ₹2,981.94. The old `acct-note` named
Dream Bat by index as a campaign with no sale; it is now worked out from the data, so a
campaign drops out of that sentence the day it books its first result.

**Worth knowing:** `Dream · No AI Bat Test` booked 10 adds to cart and 1 site lead on
**14 November with zero spend and zero impressions** — late attribution from earlier
clicks. It is kept as a real zero-spend row, not dropped as a gap.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** All campaigns
on account `1811409506889048` are now first-class: `CAMPS_ALL`,
`DAILY_ALL` (a row per campaign per day) and `ADS_ALL` (a row per ad) drive an
account section at the top, and every campaign gets its own creative table.

**Leads and purchases are never blended.** The account sells four different things —
quiz leads, ₹199 clinic sessions, Dream bats and Air Light bats — so each group is
priced in its own currency and there is deliberately no single account-wide "cost per
result". `CAMPS_ALL[i][5]` says which result kind a campaign reports, and
`CAMPS_ALL[i][2]` which product group it belongs to. `GROUPS` gained a fourth entry,
`airlight`, on 17 Nov; `Dream · No AI Bat Test` is routed into `dream`.

**Reach is carried, not derived.** `CAMPS_ALL[i][6]` is Meta's own de-duplicated
lifetime reach per campaign. It can never be obtained by adding the day rows up, so the
account table shows it per campaign and leaves the total blank. Ad-level reach in
`ADS_ALL` comes from a lifetime pull for the same reason — it is not the sum of the
settled window and today.

The quiz keeps its own sections below the account view, because it is the only
funnel on the account with site events behind it — the placement, age and pixel
breakdowns exist for the quiz and nothing else. Those arrays (`DAILY`, `REELS`,
`PLACE`, `AGE`, `PIXEL`) stay quiz-only.

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
adds to cart, purchases and leads; the campaign day rows must equal the
`level: "ad_account"` daily totals on both spend and impressions, per day; and the
three quiz campaigns in `DAILY_ALL` must reconcile against the quiz-only `DAILY` on
spend, impressions, page views, adds to cart and leads. Reach is excluded from these
checks on purpose.

## Shape

`index.html` is a **single self-contained file**. Figures live in flat arrays in
the `<script>` block at the bottom; everything above them — every table, every
bar, **and every sentence** — is rendered from those arrays at load time. Nothing
calls the network. There is no build step and no separate internal version.

Counts are computed, never typed. The masthead campaign count, the account table's
total row, the "N campaigns have run on this account" line and the explanation of the
blank result cells were all hand-written at some point and all went stale the moment a
campaign was added; they are now derived from `CAMPS_ALL` and `DAILY_ALL`. **If you
find yourself typing a number into the markup or into an `el()` string, that is the
bug.**

This is the same shape as the Elixir Creative Ledger and the O2 / Halcyon calls
reports, and it is what makes the page safe to refresh automatically: no prose
goes stale when a day is added, because no prose is written by hand.

## Refreshing it

Patch the arrays, nothing else:

| Array | Row shape |
|---|---|
| `CAMPS_ALL` | `[id, short name, group key, status, daily budget, result kind, lifetime reach]` |
| `DAILY_ALL` | `[campIdx, date, spend, impressions, reach, link clicks, lpv, cart adds, purchases, leads]` |
| `ADS_ALL` | `[name, campIdx, status, spend, impressions, reach, link clicks, lpv, cart adds, purchases, leads]` |
| `DAILY` | `[date, spend, impressions, lpv, cart adds, leads]`, quiz only, one per day |
| `REELS` | `[creative, spend, impressions, lpv, cart adds, leads, [campaign indexes]]`, sorted by spend desc |
| `PLACE` | `[platform, position, spend, impressions, lpv, leads]` |
| `AGE` | `[band, gender, spend, impressions, lpv, leads]` |
| `PIXEL` | `[date, ...counts in EV order]` — `EV` names the events |
| `SNAP_DAY` | the still-running day; `SNAP_TS` the snapshot stamp; `ALL_DAYS` the day axis |

**The last `DAILY` row must be `SNAP_DAY`.** The page treats that day as still
accruing: it is shown separately, labelled "so far", and excluded from every
headline total and from the blended cost per lead. That is the guard against
Meta's ~48h revisions — a part-day must never set a headline number. Pad `DAILY`
with zero rows if the quiz is dark, rather than letting the last row be an old day.

**`CAMPS_ALL` order must not be reshuffled** — `DAILY_ALL[0]` and `ADS_ALL[1]` index
into it. **Append new campaigns to the end.** Air Light was appended as index 6,
`Dream · No AI Bat Test` as 7 and the Air Light copy as 8 on 17 Nov for exactly this
reason.

### Traps this account has actually sprung

- **Include ARCHIVED and DELETED in every campaign and ad listing.**
  `effective_status IN ["ACTIVE","PAUSED","ARCHIVED","DELETED","CAMPAIGN_PAUSED","ADSET_PAUSED",...]`.
  `OLD · DO NOT USE · Z-Bat Clinic Leads` (`120252806446910067`) is ARCHIVED and an
  unfiltered listing omits it; it has never delivered (₹0, no impressions) so it stays
  out of `CAMPS_ALL`, but the only way to know that is to ask for it. Five *ads* also
  carry archived duplicates. Same trap as `halcyon-reports` and `elixir-reports`.
- **Several ads share a name inside one campaign.** A retired ad set kept its ad names
  when a campaign was rebuilt, so `Dream · Value · front`, `Dream · Carousel · 3 angles`
  and the three `(ATC)` reels each appear twice under one campaign. Collapse them onto
  one row per name — but assert that at most one of the duplicates ever delivered,
  because merging two live ads would silently invent a creative.
- **Ad-level reads need `campaign_id` requested by name**; it is not returned by default.
- **A PAUSED status does not mean a campaign is finished.** All three quiz campaigns
  read PAUSED on 20 and 21 September *while spending*. Always re-pull the days, and
  re-pull the days either side of the last snapshot too — Meta revised both of those
  days up by ₹0.28 after they were published.
- **Build the part-day's campaign rows by summing that day's ad rows.** A campaign-level
  read of today and an ad-level read of today, taken seconds apart, do not have to
  agree: on 17 Nov they differed by 3 link clicks on the live campaign while spend and
  impressions matched exactly. Summing the ad rows makes the creative table and the day
  table agree on the part-day by construction. The same drift is why the live
  campaign's lifetime spend read ₹5,378.48 while its day rows summed to ₹5,387.12
  minutes later — the builder allows a difference no larger than today's own spend on
  an ACTIVE campaign and nothing at all on a paused one.
- Field names: `amount_spent`, `impressions`, `omni_landing_page_view`,
  `omni_add_to_cart`, `omni_purchase`, `lead`, `link_click`. **`link_click`, not
  `clicks`** — the account table's column is link clicks, and `clicks` runs ~35% higher.

### Checks that must pass before publishing

- `REELS` totals equal `DAILY` totals on spend, impressions **and** leads.
- `AGE` totals equal `DAILY` totals on spend and leads.
- `PLACE` matches `DAILY` exactly on leads; a few rupees of spend drift is
  Meta's own breakdown rounding and is reported on the page (`PLACE_DRIFT`).
- Campaign day rows equal the `ad_account` daily totals on spend and impressions for
  every **settled** day. Only `SNAP_DAY` may differ, and only because the two pulls
  were minutes apart.

### Store pixel

Pull with `ads_get_dataset_stats`, `aggregation: "event"`, unix
`start_time`/`end_time`; **max lookback 28 days**. An IST day is the 24 hourly buckets
whose instants fall inside it; under the old −07:00 stamps that is exactly the
documented "(D−1) 12:00 through D 11:00" window, and it keeps working now that US
daylight saving has ended and the stamps read **−08:00**. Convert each bucket's
timestamp with its own offset rather than assuming −07:00. These counts cover all site
traffic, not only visitors from ads, so they run ahead of the Meta lead count — the
page says so.

**`PIXEL` is frozen at 8–29 September and cannot be refreshed.** Those days are long
past the 28-day lookback, so the API will not serve them again. The rows are the quiz's
own delivery window, which is what the quiz funnel section needs, and the page now
states the range rather than leaving it to be guessed.

The last 28 days *were* pulled, as a check, and they are worth a human's attention even
though they are not on the page. Over the 26 complete IST days **22 Oct – 16 Nov** the
dataset recorded **29,278 page views, 1,790 adds to cart, 570 checkouts and 129
purchases** — against 45 purchases Meta attributes to the ads over roughly the same
stretch, so most of what the shop sells is not being credited to a campaign. It also
recorded **397 `BatFitStarted` and 157 `Lead` events with no quiz ad spend at all**:
the bat-fit quiz is still pulling organic traffic three weeks after its ads were
switched off. Neither figure belongs in the quiz funnel table, which is the quiz's own
*paid* window, but both are real.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.
