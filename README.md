# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 10 Nov 2026: the account is delivering again, and it is a different
account from the one this report last described.** The spending limit that stopped
everything on 26 Sep has been lifted — delivery resumed on 1 Oct and has run more or
less continuously since 9 Oct. Lifetime spend is now **₹40,288.67**, up from
₹14,406.77 at the last snapshot: the account has spent nearly twice as much in the
last six weeks as it had in its whole life before. `STOP_REASON` is back to `None`
and the page's stop note has cleared itself.

**The headline is a campaign that did not exist at the last cut.**
`Air Light · Purchase · India · ₹1000/day · Oct 2026` started on 14 Oct, is the only
ACTIVE campaign, and has taken **₹18,599.49 for 29 purchases — ₹641.36 a sale**. It
is now the largest spender the account has ever had, larger than the entire quiz
(₹6,692.70) and larger than the clinic (₹9,298.21 + ₹837.52). Five creatives;
`Air Light - Hook1` alone carries ₹14,829.85 and 24 of the 29 sales.

A second new campaign, `Dream · No AI Bat Test · Purchase · ₹700/day · Oct 2026`,
ran 28–31 Oct for ₹1,878.81 and **one** purchase, then paused.

**The Dream Bat cart problem resolved itself, barely.** The old Dream Bat campaign
was flagged at the last cut as "₹1,337.01, 14 site leads, still no purchase". It ran
on to ₹2,981.94 and finally took **one** sale, then paused on 14 Oct. Across both
Dream campaigns that is ₹4,860.75 for 2 sales — ₹2,430.38 each, against Air Light's
₹641.36. **Air Light is roughly four times more efficient than Dream on the same
account selling the same kind of thing**, and that is the finding worth putting in
front of the client.

**The clinic ran on and then stopped.** `Z-Bat Clinic · Parel · ₹199 Session Sales`
went from ₹6,377.06 / 8 purchases to **₹9,298.21 / 11 purchases** (₹845.29 each) and
paused on 12 Oct. Ten new `v2` reel creatives were added to it during that run;
`Clinic ₹199 · Video 4 · Copy 3 Local offer` still carries most of it (₹6,739.14,
9 of the 11 sales).

**The clinic Copy campaign is no longer "never switched on".** It ran for two days,
2–3 Nov, took ₹837.52 and produced no sale. It is the one blank left in the result
column, and the page now works that out from the data rather than naming it by hand.

**All three quiz campaigns are still frozen** at exactly ₹6,692.70 and 1,019 leads —
unchanged across every refresh since 21 September, now eleven weeks. Their creative,
placement, age and pixel arrays are unchanged in this cut because none of the
underlying numbers moved a paisa. When the quiz is dark this report's quiz half is a
historical record; the account section at the top is the live part.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** Every campaign
on account `1811409506889048` that has ever spent is first-class — eight as of
10 Nov 2026, up from six: `CAMPS_ALL`,
`DAILY_ALL` (a row per campaign per day) and `ADS_ALL` (a row per ad) drive a new
account section at the top, and every campaign gets its own creative table.

**Leads and purchases are never blended.** The account sells four different
things — quiz leads, ₹199 clinic sessions, Dream bats and Air Light bats — so each
group is priced in its own currency and there is deliberately no single
account-wide "cost per result". `CAMPS_ALL[i][5]` says which result kind a campaign
reports, and `CAMPS_ALL[i][2]` which group it belongs to.

**Air Light is its own group, not part of Dream Bat.** Both sell a bat direct, but
they are different products at different prices and their costs per sale differ by
about 4x. Blending them would hide exactly the comparison that matters. Adding a
group is one entry in `GROUPS` plus the right key in `CAMPS_ALL[i][2]`.

**Never reshuffle `CAMPS_ALL`** — `DAILY_ALL[0]` and `ADS_ALL[1]` index into it by
position. Append new campaigns to the end, which is where Air Light (6) and
Dream · No AI Bat Test (7) went.

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

**Meta's own attribution can break that tie on a campaign that is still
delivering.** On the 10 Nov cut, Air Light's ad rows and day rows agreed exactly on
spend, impressions, adds to cart, purchases and leads, but sat **one** link click
and **one** page view apart (2,445 vs 2,446 and 2,324 vs 2,323). That is Meta
disagreeing with itself across two reads of a live campaign, not a build error.
Rather than force the numbers to match, the page now measures the gap itself and
names it in the reconciliation note, so it can never silently claim an agreement it
does not have. Both sides still reconcile against the campaign's own period total.

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

**The 28-day lookback is a hard wall, and it has already cost us a window.**
`PIXEL` currently holds 8-29 Sep, the period the quiz actually ran. On the
10 Nov 2026 refresh the earliest fetchable IST day was 14 Oct, so **30 Sep - 13 Oct
can no longer be retrieved from Meta at all**. `PIXEL` was deliberately left alone
in that refresh: the array feeds the section headed "The quiz funnel", the quiz has
not spent since 21 Sep, and splicing a disjoint October-November window under that
heading would describe Air Light's site traffic as if it were the quiz's.

If the client ever wants a site funnel for the current Air Light period, **it has to
be captured within 28 days of the days it covers** — and it belongs in a new array
under its own heading, not appended to `PIXEL`. For reference, the store pixel over
1-9 Nov 2026 was running roughly 660-1,500 page views and 6-12 purchases a day, so
the data is there for the taking while it lasts.

## Prose that was hand-written and went stale (fixed 10 Nov 2026)

The shape note above promises that "every sentence is rendered from those arrays at
load time" and "no prose goes stale". Four sentences did not honour that, and three
of them had become false by 10 Nov:

- `"Six campaigns have run on this account"` - a hard-coded word, wrong once the
  account had eight. Now `CAMPS_ALL.length`, with the follow-on clause switching
  between "all of them" and "N of them" on its own.
- `"· 6 campaigns ·"` in the masthead period line - same fix.
- `<td>All six</td>` in the campaign table's total row - same fix.
- The result-column note named `CAMPS_ALL[4]` as "built and never switched on" and
  `CAMPS_ALL[5]` as having "not one sale yet". Both became false in October. It now
  derives the list of blank-result campaigns from the data and says nothing when
  there are none.
- The creative intro claimed "ads that never spent are listed" whether or not any
  were; it is now conditional.

If you add prose to this file, make it read from the arrays or it will lie within a
month.
