# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | 8 Sep – 8 Oct 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 8 Oct 2026: the spending-limit block is over, but the account is
now deliberately paused.** The limit was lifted and delivery resumed on **1 October**
for one day only &mdash; the clinic took ₹346.45 and Dream Bat ₹268.38, ₹614.83 between
them &mdash; and then stopped again. **All six campaigns now read PAUSED**, including the
clinic and Dream Bat, which were ACTIVE at the last snapshot. Lifetime account spend
is ₹15,021.60.

`STOP_REASON` is therefore back to `None`: the account spending limit is no longer the
explanation, and a pause is a decision rather than a stop. The page works this out for
itself &mdash; with no ACTIVE campaign the delivery-stop note suppresses itself, exactly
as designed. **Do not hand-write a note saying the account is paused**; the campaign
table already says `paused` on every row.

**What a human needs to decide:** nothing is scheduled to run. The 1 October day was
the first delivery in six days and it was not followed up. If that was intentional, the
report is simply describing a finished flight; if not, somebody paused the account and
has not restarted it.

**All three quiz campaigns are still frozen at exactly ₹6,692.70 and 1,019 leads** &mdash;
unchanged across nine refreshes now, and seventeen days since the quiz last spent
(21 September). Not even a late-attributed lead has moved. They also read PAUSED on 20
and 21 September *while spending*, so do not assume PAUSED means finished &mdash; always
re-pull the days, and the days either side of the last snapshot too.

**The clinic is at ₹6,723.51 and 9 purchases.** The ninth was **late-attributed back to
25 September**, a day that had been published with none; its lifetime count went 8 to 9
without any new delivery behind it. Re-pull the whole window, not just the new days, or
the ad rows and the day rows stop agreeing &mdash; that assertion is what caught it.

**Dream Bat is at ₹1,605.39 over four days: 252 landing page views, 37 adds to cart,
15 site leads, and still no purchase.** It took ₹268.38 more on 1 October and all of it
went to the `Dream &middot; Carousel &middot; 3 angles` ad (₹36.35 to ₹304.73). That is the quiz
funnel's old cart problem appearing again on a campaign that does not touch the quiz,
and it is now four days of spend with nothing sold.

**Meta renamed the Dream Bat campaign** to `Dream Bat &middot; Direct Sales &middot; India &middot; ₹600/day
&middot; Sep 2026`. `CAMPS_ALL` carries a short editorial label rather than Meta's name, so
nothing needed changing &mdash; but note the new name claims ₹600/day while the campaign's
actual `daily_budget` is **₹500**. The name is wrong, not the report.

**Non-quiz spend is now ₹8,328.90 against the quiz's ₹6,692.70** &mdash; the quiz is down to
45% of what the account has ever spent. When it is dark this report describes a smaller
and smaller share of the account, which is worth saying to the client rather than letting
the page look idle.

**Worth knowing while the ads are off:** the store pixel still records activity with no ad
spend behind it. On **29 September it logged 239 AddToCart events**, 231 of them in two
hours (05:00 and 06:00 in the dataset's own &minus;07:00 clock), against a normal day of
12–15. No spend, no campaign, and no purchases followed. **That looks like bot or
duplicate-fire traffic rather than demand** and is worth checking before anyone reads the
cart numbers as interest; 6 October shows a smaller version of the same thing (66 adds to
cart, 27 of them in one hour, again with the account dark). Real purchases did continue
without any advertising behind them: **18 since 30 September**, on 2 October (9),
4 October (3) and 6 October (6). The shop is still selling; it is the advertising that
has stopped.

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

### Things this refresh hit

- **`STOP_REASON` lives in `index.html`**, next to `SNAP_DAY` — there is no `zs_data.py`
  in this repo, whatever earlier notes said. Set it to `None` when the named reason no
  longer applies; with no ACTIVE campaign the stop note suppresses itself anyway.
- **The `clicks` column in `DAILY_ALL` and `ADS_ALL` is `link_click`, not `clicks`.**
  They differ a lot (clinic lifetime: 525 link clicks against 733 clicks). Pull
  `link_click` or the ad-vs-day assertion fails for the wrong reason.
- **Results get attributed backwards.** A clinic purchase landed on 25 September this
  refresh, a day already published with none. Re-pull the whole window.
- **The quiz-only arrays need no work while the quiz is dark.** `REELS`, `PLACE`, `AGE`
  and `CAMPS` were byte-identical this refresh because the quiz has not spent since
  21 September; `PLACE_DRIFT` keeps its published value for the same reason. Assert it
  rather than assuming it.
- Zero rows go into `DAILY_ALL` for **live** campaigns on dead days, to make a stop
  visible as data. With every campaign paused there is no stop to show, so no new zero
  rows were added. `DAILY` (quiz-only) still needs one row per day, including `SNAP_DAY`.

### Store pixel

Pull with `ads_get_dataset_stats`, `aggregation: "event"`, unix
`start_time`/`end_time`; max lookback 28 days. Events are stamped **−07:00**, and
an IST day D is the 24 buckets from (D−1) 12:00 through D 11:00. These counts
cover all site traffic, not only visitors from ads, so they run ahead of the Meta
lead count — the page says so.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.
