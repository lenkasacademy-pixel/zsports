# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 26 Oct 2026, 8:20 am IST: delivery has resumed and the account
spending limit is no longer binding.** The account was dark from 26 September
with Meta reporting `account_spend_limit_reached`; it spent again on 1 October,
was quiet 2–8 October, and has delivered **every day from 9 October to the 26th**.
`STOP_REASON` is now `None` — the page works the stop notice out from `CAMPS_ALL`
and `DAILY_ALL` on its own, so with the trailing days no longer dead the notice
clears itself and nothing was hand-removed.

**Lifetime account spend has nearly doubled, from ₹14,406.77 to ₹27,902.59**, and
almost all of the growth is outside the quiz.

| Product line | Spend | Result | Each | Status |
|---|---|---|---|---|
| AI Bat Fit Quiz | ₹6,692.70 | 1,019 leads | **₹6.57** | all three PAUSED |
| Z-Bat Clinic | ₹9,298.21 | 11 × ₹199 sessions | ₹845.29 | PAUSED |
| **Air Light** (new) | ₹8,929.74 | **14 sales** | **₹637.84** | ACTIVE |
| Dream Bat | ₹2,981.94 | **1 sale** | ₹2,981.94 | PAUSED |

**A fourth product line appeared: `Air Light · Purchase · India · ₹1000/day ·
Oct 2026`** (`120253356448770067`), running ₹700/day since 14 October and the only
ACTIVE campaign on the account. It is the best-performing sales line Z-Bat has
run: **14 purchases on ₹8,929.74**, against the clinic's 11 on ₹9,298.21 and Dream
Bat's 1 on ₹2,981.94. Five hooks; `Air Light - Hook1` carries ₹6,581.49 of the
spend and 9 of the 14 sales. It needed a new `GROUPS` entry (`key:"air"`) and is
**appended** to `CAMPS_ALL` at index 6 so no historical `DAILY_ALL` or `ADS_ALL`
row re-labels itself.

**Dream Bat has its first sale.** The previous refresh flagged it as "₹1,337.01
over three days: 222 landing page views, 36 adds to cart, 14 site leads, **still
no purchase** — the quiz funnel's old cart problem appearing again". It went on to
₹2,981.94 and **1 purchase**, which is one more than zero but still ₹2,981.94 a
sale on 72 adds to cart. The cart problem is real and is not fixed; Air Light
converts adds to cart to sales roughly nine times better.

**The clinic is paused at 11 sessions**, up from 8, on ₹9,298.21 — ₹845.29 each
against the ₹199 ticket. It also gained **nine new "v2" reel creatives** (Reel 1/2/3
× Hook A/B/C) alongside `Video 4 · Copy 3 Local offer`, which still carries ₹6,739.14
of the campaign's spend and 9 of its 11 sales.

**All three quiz campaigns remain PAUSED and have not spent since 21 September** —
still exactly ₹6,692.70 and 1,019 leads, now across nine refreshes, with not even
a late-attributed lead moving. Non-quiz spend is now ₹21,209.89 against the quiz's
₹6,692.70: **this report describes less than a quarter of what the account does.**
Worth saying to the client rather than letting the quiz sections look like the
whole story.

**Worth knowing:** the store pixel kept recording sales throughout the dark period.

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
resumes — do not hand-write or hand-remove that note. **This worked**: setting
`STOP_REASON = None` and adding the October rows was enough for the notice to
disappear on the 26 Oct refresh.

## Counts and blanks must be derived too (fixed 26 Oct 2026)

The "no prose is written by hand" rule had three leaks, and all three went stale
the moment a seventh campaign appeared and Dream Bat took a sale:

- the masthead said `" · 6 campaigns · "` as a literal;
- the account intro opened `"Six campaigns have run on this account"`;
- `acct-note` explained "**The two blanks** in the result column" using
  **hard-coded indices** `CAMPS_ALL[4]` and `CAMPS_ALL[5]`. With Dream Bat
  (index 5) now holding a purchase, the conditional collapsed to
  `"Dream Bat · Direct sales has ."` — a broken sentence asserting a blank that
  was no longer blank.

All three now derive from the arrays: the counts from `CAMPS_ALL.length` and
`GROUPS[0].idx.length`, and `acct-note` from `cSpend(i)` and `cRes(i)` — it finds
the campaigns that never ran and the ones that ran without a result, counts them,
and pluralises itself. **If you add a line of prose to this page, derive every
number and every index in it, or it will lie at the next refresh.** Explicit zero rows are
added to `DAILY_ALL` for live campaigns on dead days so the stop is visible as
data rather than as a gap.

**What the builder asserts before writing the file:** for every campaign, its day
rows and its ad rows must agree on spend, impressions, link clicks, page views,
adds to cart, purchases and leads — **and both must agree with Meta's own
campaign-level lifetime total for that campaign**; and the three quiz campaigns in
`DAILY_ALL` must reconcile against the quiz-only `DAILY` on both spend and leads.
`DAILY_ALL` spend must also equal `ACCOUNT_SPEND`. Reach is excluded from these
checks on purpose.

**Re-pull the published days, not just the new ones.** On the 26 Oct refresh the
ad-vs-day assert caught a purchase the clinic did not have when the page was
published: Meta added one to **25 September** after the fact, taking that campaign
from 8 purchases to 9. Every other published September row re-pulled identically
to the paisa, so one cell was corrected rather than the whole month rebuilt — but
the only reason it was found is that the assert compares day rows, ad rows and
Meta's campaign total all three ways.

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

**A part-day `PIXEL` row can be settled later, inside the 28-day window.** The
29 September row was captured as a part-day and read 83 page views; re-pulled on
26 October it is **350**. Always re-derive the previous snapshot's last `PIXEL`
row, not just the new days — but note the 28-day lookback means a part-day row
older than that is frozen wrong for good, so do not let one go unsettled for a
month.
