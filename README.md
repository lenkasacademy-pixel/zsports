# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 5 Oct 2026: the spending limit is resolved, and the account is now
deliberately switched off.** The sequence, from the activity log:

- **1 Oct, 2:16 pm** — ₹10,000 added to the available balance.
- **1 Oct** — the account served again for the first time since the 25th: **₹614.83**
  (Clinic ₹346.45, Dream Bat ₹268.38).
- **1 Oct, 8:18 pm** — **Abhijeet Lenka paused the Clinic and Dream Bat campaigns**
  (Power Editor, Active → Inactive). They were the only two still ACTIVE.
- Nothing has served since. All six campaigns now read PAUSED.

So `STOP_REASON` is now **`None`**. The `account_spend_limit_reached` substatus no longer
applies — the limit stopped binding once the balance was topped up, the account proved it
by spending, and the stop since is a human decision. With no ACTIVE campaign the page's
stop-detection block clears itself (`if (!live.length) return`), which is the right
behaviour: there is no mystery left to explain. **Do not hand-write a note about the
pause** — the page already says "Every quiz campaign is now paused at Meta, so this is a
deliberate stop rather than a delivery problem" in its own words.

Lifetime account spend is now **₹15,021.60** (was ₹14,406.77 — the whole difference is
1 October).

**The quiz has still not moved: ninth refresh at exactly ₹6,692.70 and 1,019 leads**, not
one late-attributed lead. It last spent on 21 September and has been dark 14 days.
`REELS`, `PLACE` and `AGE` came back **byte-identical** to the published arrays, which is
the cleanest confirmation available. Instagram still returns no rows at all, so the page's
eligibility explanation stands.

**A PAUSED status does not mean a campaign is finished.** The quiz campaigns read PAUSED on
20 and 21 September *while spending*, so always re-pull the days — and re-pull the days
either side of the last snapshot too. Meta has now revised after publication on three
separate refreshes: both of those days up by ₹0.28, and this time a 9th clinic purchase
landing on 25 September.

**The clinic is at ₹6,723.51 and 9 purchases** (₹747.06 each), up from 8 — and that 9th
was **attributed back to 25 September after publication**. The published day row had 0
purchases there while the campaign total went 8 → 9 with no new delivery day. The
day-rows-equal-ad-rows assertion is what caught it.

**Dream Bat is the thing to raise with the client.** It is now at ₹1,605.39 over four days
— 252 landing page views, 37 adds to cart, 15 site leads and **still not one purchase**.
It has spent 20% more than at the last refresh and the cart problem has not budged. That
is the quiz funnel's old cart problem appearing again on a campaign that does not touch
the quiz.

**Worth knowing while the ads are off:** the store pixel recorded **12 Purchase events on
days with no ad spend behind them** — 9 on 2 Oct and 3 on 4 Oct. The shop is still
selling; it is the advertising that has stopped.

Non-quiz spend on this account is now ₹8,328.90 against the quiz's ₹6,692.70. When the
quiz is dark, this report describes a smaller and smaller share of what the account is
doing — worth saying to the client rather than letting the page look idle.

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

**Verify the IST bucketing against a published day before trusting new rows.** Re-pulling
the window for a day already in `PIXEL` must reproduce its row exactly. On this refresh
IST 28 Sep came back as PV 352, VC 80, BFS 8, LD 6, ATC 15, IC 8, API 5, PUR 0, CBO 12,
CSP 10, SCH 10 — identical to the published row, which is what licensed the six new days.
In unix terms an IST day D is `start = (D-1) 19:00 UTC`, `end = D 19:00 UTC`.

Note the part-day has fewer than 24 buckets by definition (5 Oct had 8), and a settled day
can have fewer than 24 too if an hour had no events at all (30 Sep had 23, 1 Oct had 22) —
that is not a gap in the pull.

**One pre-existing quirk, unchanged by this refresh:** the pixel table's *Period* row sums
all days including `SNAP_DAY`, unlike the quiz headline tiles, which correctly exclude it.
The part-day is labelled "SO FAR" in its own row directly above, and the table counts all
site traffic rather than ad results, so it is defensible — but it is inconsistent with the
rest of the page. Left alone deliberately, since this file says to patch the arrays and
nothing else.
