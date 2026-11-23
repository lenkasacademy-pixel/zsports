# zsports

Client-facing Meta ads reports for **Shah Sports Tech Private Limited** (Z-Bat).

| Report | Period | Live |
|---|---|---|
| Z-Bat — every Meta campaign | from 8 Sep 2026, rolling | [index.html](https://lenkasacademy-pixel.github.io/zsports/) |

Source: Meta Ads API, ad account `1811409506889048` (zbats), plus store pixel
dataset `1380826504029254`. Figures are in the advertiser's time zone, currency INR.

**Status as of 23 Nov 2026: delivery resumed and the account is spending more than
ever.** The account spending limit that stopped everything on 25 Sep is no longer
binding — `STOP_REASON` is back to `null`, and the page's own stop detector is quiet
because the one ACTIVE campaign is delivering. Spend by month: **₹14,406.77 (Sep) →
₹16,949.38 (Oct) → ₹28,249.68 (Nov to the 23rd)**, ₹59,605.83 lifetime across 77 days.

**The account has moved on from the quiz, and the report now reflects that.** Five new
campaigns opened in October and November, all selling **Air Light** bats out of the
product catalogue (`product_catalog_id 1069890358754783`). They are now **62% of
lifetime account spend** and have produced **73 purchases at ₹504.76** — more results
than everything else on the account put together, excluding quiz leads.

| Product line | Spend | Results | Each |
|---|---|---|---|
| Air Light (4 campaigns) | ₹36,847.42 | 73 sales | **₹504.76** |
| Z-Bat Clinic (2) | ₹10,135.73 | 11 sessions | ₹921.43 |
| AI Bat Fit Quiz (3) | ₹6,692.70 | 1,019 leads | ₹6.57 |
| Dream Bat (2) | ₹5,929.98 | 2 sales | ₹2,964.99 |

**The quiz has not moved a rupee or a lead since 21 September** — ₹6,692.70 and 1,019
leads, identical across every refresh since. `REELS`, `PLACE` and `AGE` came back
byte-for-byte identical to the published arrays on the 23 Nov pull. Every quiz campaign
is PAUSED, and the page works that out for itself from `CAMPS_ALL` status and the
`DAILY` rows ("the quiz campaigns last spent on 21 Sep … and have been dark for 63 days
since"). **Do not hand-write a note about it.**

**Dream Bat is still the problem line.** Two campaigns, ₹5,929.98, **2 sales** —
₹2,964.99 each against Air Light's ₹504.76 on the same catalogue and the same site.
`Dream · No AI Bat Test` was built to test exactly that and bought one sale on
₹2,948.04. Worth raising before it spends more.

**`Clinic · Parel ₹199 (Copy)` delivered for the first time** on 2–3 Nov: ₹837.52,
3,481 impressions, 42 page views, **no purchase**. It used to be the "built and never
switched on" row in the account note; that sentence is now computed, not typed.

## Every campaign is tracked (changed 25 Sep 2026)

The report used to cover the three quiz campaigns only, with the clinic and Dream
Bat reduced to a footnote in an `OTHER` list. **That list is gone.** Every campaign on
account `1811409506889048` is first-class: `CAMPS_ALL`, `DAILY_ALL` (a row per campaign
per day) and `ADS_ALL` (a row per ad) drive the account section at the top, and every
campaign gets its own creative table.

**Leads and purchases are never blended.** The account sells four different things —
quiz leads, ₹199 clinic sessions, Dream bats and Air Light bats — so each group is
priced in its own currency and there is deliberately no single account-wide "cost per
result". `CAMPS_ALL[i][5]` says which result kind a campaign reports, and `[2]` says
which product group it belongs to.

**`GROUPS` is the one place a new product line has to be declared.** It is a small
lookup in the script (`key`, `name`, `sells`, `unit`, `units`) and the tiles, the group
totals and `ACCT_RES_KINDS` all read off it. The `air` group was added on 23 Nov 2026
for the Air Light catalogue campaigns; without it they would have been grouped wrongly
and the tile row would have under-reported the account. **If a campaign appears with a
group key that is not in `GROUPS`, it silently vanishes from the tiles — check that the
group spends in the tile row add up to `ACCOUNT_SPEND` before publishing.**

**Reach is carried, not derived.** `CAMPS_ALL[i][6]` is Meta's own de-duplicated
lifetime reach per campaign. It can never be obtained by adding the day rows up, so the
account table shows it per campaign and leaves the total blank.

The quiz keeps its own sections below the account view, because it is the only funnel on
the account with site events behind it — the placement, age and pixel breakdowns exist
for the quiz and nothing else. Those arrays (`DAILY`, `REELS`, `PLACE`, `AGE`, `PIXEL`)
stay quiz-only.

**The page detects a delivery stop on its own.** If every ACTIVE campaign serves nothing
on the trailing days, the account section says so, names the budget those campaigns are
still carrying, and gives Meta's own reason when `STOP_REASON` is set (otherwise it
points at the payment method). It is computed from `CAMPS_ALL` status and `DAILY_ALL`,
so it clears itself when delivery resumes — **do not hand-write or hand-remove that
note.** It cleared itself on this refresh. Explicit zero rows are written into
`DAILY_ALL` for live campaigns **from their first delivering day onward**, so a stop
shows as data rather than as a gap — but not before that day, or the "Ran" column
back-dates the campaign to the start of the account.

## Nothing about the account may be typed as a number

Three sentences shipped with counts frozen at the moment they were written, and all
three were wrong by 23 Nov. They are now read off `CAMPS_ALL`:

- the masthead said `· 6 campaigns ·`
- the account intro opened `"Six campaigns have run on this account"`
- the campaign table's total row said `All six`

and a fourth named two campaigns by index: *"the two blanks in the result column are
deliberate: `CAMPS_ALL[4]` was built and never switched on, and `CAMPS_ALL[5]` has …"*.
Both conditions had become false — index 4 has since spent, index 5 has since sold — and
the sentence rendered as **"Dream Bat · Direct sales has ."** on the live page. It now
finds the blank rows itself and describes whichever ones exist.

If you add anything to this page, **derive it or leave it out**. The only hand-written
constants left are `SNAP_TS`, `STOP_REASON`, `CHANGE` and the `GROUPS` lookup.

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
| `DAILY` | `[date, spend, impressions, lpv, cart adds, leads]`, one per day, quiz only |
| `REELS` | `[creative, spend, impressions, lpv, cart adds, leads, [campaign indexes]]`, sorted by spend desc |
| `PLACE` | `[platform, position, spend, impressions, lpv, leads]` |
| `AGE` | `[band, gender, spend, impressions, lpv, leads]` |
| `PIXEL` | `[date, ...counts in EV order]` — `EV` names the events |
| `CAMPS_ALL` | `[id, label, group, status, daily budget, result kind, lifetime reach]` |
| `DAILY_ALL` | `[campaign index, date, spend, impressions, reach, link clicks, lpv, cart adds, purchases, leads]` |
| `ADS_ALL` | `[name, campaign index, status, spend, impressions, reach, link clicks, lpv, cart adds, purchases, leads]` |
| `SNAP_DAY` | the still-running day; `SNAP_TS` the snapshot stamp |

**`CAMPS_ALL` order is an append-only contract.** `DAILY_ALL[i][0]` and `ADS_ALL[i][1]`
index into it, so a new campaign goes on the end and nothing is ever reordered. The
first six entries are the order this report was published with.

**The last `DAILY` row must be `SNAP_DAY`.** The page treats that day as still
accruing: it is shown separately, labelled "so far", and excluded from every
headline total and from the blended cost per lead. That is the guard against
Meta's ~48h revisions — a part-day must never set a headline number. `DAILY` now runs
8 Sep – 23 Nov, 77 rows, of which only 12 carry any quiz delivery; the zero rows stay so
the stop reads as data.

### Checks that must pass before publishing

- `REELS` totals equal `DAILY` totals on spend, impressions **and** leads.
- `AGE` totals equal `DAILY` totals on spend and leads.
- `PLACE` matches `DAILY` exactly on leads; a few rupees of spend drift is
  Meta's own breakdown rounding and is reported on the page (`PLACE_DRIFT`).
- The three quiz campaigns in `DAILY_ALL` reconcile against `DAILY` on spend,
  impressions, page views, cart adds and leads.
- **Every day, campaign-level spend and impressions equal the `level: "ad_account"`
  daily totals.** Clean on all 77 days on the 23 Nov pull, to the paisa.
- Each campaign's `DAILY_ALL` rows equal its `ADS_ALL` rows on spend, impressions, link
  clicks, page views, cart adds, purchases and leads — **except a campaign that is still
  delivering**, see below.

### A live campaign cannot reconcile exactly, and the page says so

The day rows and the ad rows are two separate API reads, seconds apart, and Meta keeps
revising the part-day in between. On the 23 Nov pull the one ACTIVE campaign
(`Air Light · Purchase (Copy 2)`) read **4 link clicks and 1 page view** higher at ad
level than at day level; spend, impressions, purchases and leads agreed exactly, as did
every other campaign on every column. Rather than assert an agreement that does not
hold, the page **computes the gap at render time and names it** in the reconciliation
note. Do not chase it by re-pulling — the part-day moves again while you do.

### Pull notes

- **Always pass `effective_status IN ["ACTIVE","PAUSED","ARCHIVED","DELETED",…]`** at
  campaign *and* ad level. Without it Meta silently omits archived and deleted entities.
  Doing it properly on 23 Nov surfaced several never-run duplicate ads that earlier
  pulls had hidden; they now show as "never ran", which is the point.
- A campaign-level pull with `time_increment: "1"` returns a row for **every campaign ×
  every day**, empty or not. 12 campaigns × 77 days is 924 rows, far past the ~200-row
  cap, so split the range: six calls of 11–16 days each came back complete (144, 132,
  192, 180, 144, 132 rows — check the arithmetic every time).
- Ad-level insights do **not** return `campaign_id` unless you request it by name.
- Every one of these responses exceeds the tool's inline output limit and spills to a
  file — parse it with `jq`/python, never inline.

### Store pixel

Pull with `ads_get_dataset_stats`, `aggregation: "event"`, unix
`start_time`/`end_time`; **max lookback 28 days — this is a hard limit.** Events are
stamped **−07:00**, and an IST day D is the 24 buckets from (D−1) 12:00 through D 11:00.
These counts cover all site traffic, not only visitors from ads, so they run ahead of
the Meta lead count — the page says so.

**`PIXEL` is now a 28-day window (27 Oct – 23 Nov), not the whole ad window.** The
September rows could not be re-pulled and the gap from 30 Sep to 26 Oct is unfillable,
so the array carries one contiguous recent window rather than two blocks with a hole
between them. The page states the mismatch itself, comparing `PIXEL[0][0]` against
`ALL_DAYS[0]` — nothing to edit by hand. On the next refresh the window simply rolls
forward; the older rows are gone for good.

What that window shows: 32,990 page views, 394 quiz starts, 141 leads, 2,288 adds to
cart, 763 checkouts and **206 orders**. The shop is selling well with no quiz
advertising behind it at all.

`ClinicBookOpened`, `ClinicSlotPicked` and `Schedule` began firing on 12 Sep 2026
(the clinic booking flow) and are carried in `PIXEL`; the funnel note mentions
them automatically once they have volume.

## Is this still the right report?

The quiz is 11% of lifetime account spend and has been dark for two months. The page is
titled "every Meta campaign" and does cover everything, but **half of it — placement,
age, the pixel funnel — is quiz-only and describes a campaign group that has stopped.**
Air Light now carries the account and has no breakdowns of its own here. That is a scope
decision for the client, not something a refresh should fix on its own: either add
placement/age/pixel sections for the catalogue campaigns, or retire the quiz sections to
an archive tab. Worth putting to them.
