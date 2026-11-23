# Token Radar — make.fun bounty entry (token-radar-01)

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/exc-ana/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/exc-ana/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

Build the best token launch radar. Account: **@exc-ana (Exc Ana)**. Prize pool
$1,325 (1st $1,000, 2nd–5th $50, 6th–10th $25, USDC on Solana). Ends Mon 14 Dec
2026 17:00 WIB. **Status: program built and verified locally. NOT submitted.**
No money was spent; only free tools were used (Node.js, Playwright + Chromium).

## What it is

`main.ts` (Node 22, standard library only) reads the sealed snapshot
(`/input/snapshot.json`), scores every rankable pre-token protocol with the
radar-2 model in `lib/model.ts`, and writes:

- `/output/board.json` — the ranked board (198 rows)
- `/output/data.js` — the dashboard's dataset, beside the page
- `/output/dashboard/` — copy of `dashboard/` (index.html + app.css + app.js)
- `/output/screenshots/` — copy of `screenshots/` (3 PNGs)
- `/output/dossier.json` — written with `buildDossier` from
  `vendor/makefun-board.ts` (the kit's file, byte for byte)

Local run:

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input token-radar-01-kit/input/snapshot.json
```

Node here is v24.22.1 (requirement: 22.18+).

## Kit findings

- Kit downloaded from `https://make.fun/c/token-radar-01/kit` and unpacked to
  `token-radar-01-kit/` (bundle sha256 in its `kit.json`).
- Snapshot: DefiLlama protocols fetched 2026-11-14, `as_of` 2026-11-14,
  floor $50,000,000, **758 protocols, 40 on the watchlist**, 342 pre-token
  in total. Every protocol carries exactly the ten documented fields.
- The starter is a working entry (200 rows) and is also the house's second
  fixed judge anchor ("2-plain"), so handing it in unchanged is an ordinary
  entry. This entry was written fresh instead: new model, new dashboard,
  new farm plans. Only `vendor/makefun-board.ts` is copied from the kit,
  as the kit requires (the scorer runs that exact file).
- The sealed gate has seven rows (dashboard_files, board_file, sorted_by_value,
  coverage, farm_steps, screenshots, dossier_matches); only a valid entry
  reaches the pairwise AI judge. The judge's criteria in order: (1) working
  radar covering the whole watchlist, pre-token first; (2) value model
  following from snapshot figures (TVL, 7-day growth, age with no token);
  (3) farm plans with concrete steps, costs and time; (4) dashboard as a
  page — sortable/filterable, readable at a glance, detail view per protocol;
  (5) looks — chosen palette, consistent spacing, readable type.

## Method

**Coverage (198 rows, not 200).** The board ranks every protocol that passes
the watchlist's own rule, read from the snapshot's fields: no token on record
(no market cap, no CoinGecko id, no symbol), and not a wrapped, staked or
bridged product of a token that already trades — category not Bridge /
Canonical Bridge / Liquid Staking / Liquid Restaking / Restaked BTC / CEX,
name with no whole word `wrapped`/`staked`/`staking`, name not beginning with
an exchange name, name not ending in a version marker. That is 40 watchlist
rows + 158 more, above and below the floor. Applying the rule to every row
(rather than only to the watchlist, as the starter does) keeps products such
as WBTC and Binance staked ETH — Bitcoin on Ethereum, ETH in a staking
product — out of the top of a board about launches that have not happened yet.
No protocol name is listed or special-cased anywhere; the rule is fields only.
Protocols whose token already trades are set aside in a Live tab with their
market caps (top 24 by market cap), never ranked.

**Value model (radar-2), stated on the dashboard:**

```
implied = round( tvl_usd x 0.30
                 x (1 + clamp(change_7d, -50, +150) / 100 x 0.60)
                 x (0.55 + min(age_days, 1460) / 1460 x 0.45) )
```

Age is days since first listing (unknown age: 365 days). Growth is clamped
asymmetrically because a bad week should not erase a launch thesis; maturity
saturates at four years. The dashboard recomputes each row in the page and
shows that it equals the number `board.json` carries. Top 5:
Steakhouse Financial $584.9M, Sentora Curator $566.7M, Gauntlet $351.0M,
Veda $332.0M, USDD $229.9M. Implied values range $780,968–$584,881,060.

**Farm plans (7 steps each).** Derived per protocol from category, chains,
TVL and age: fresh wallet; fund with capital ($50/$100/$150/$250 by TVL) plus
gas token; the category's core action (supply, provide liquidity, bridge,
stake, margin, vault deposit, mint, cover…); a category-specific weekly
qualifying action; hold 30/45/60 days by age; check the protocol's own
points/rewards page and claim only from its published link, never pay to
claim; log everything and plan the exit in tranches. Every step carries a
cost and a time. The page regenerates the plan with the same derivation; a
cross-check against `board.json` for 14 protocols across categories matches
exactly (see Verification).

**Dashboard.** Static page, no network, no framework. Sage canvas, deep-green
accent, amber Watchlist pill. Header facts (watchlist 40/40, 198 rows, board
TVL $20.71B, median implied $4.5M, 24 live set aside), top-5 leaders strip,
search, category filter, chain filter (top 12), Watchlist-only toggle,
Pre-token/Live tabs, sortable columns (implied, name, TVL, 7d, age),
per-protocol detail dialog (model breakdown with multipliers and the
recompute check, plus the 7-step farm plan), methodology section stating the
formula and the exclusion rule, and a disclaimer that implied value is a
model estimate and no token is guaranteed. Deep links via URL hash
(`#p=<slug>`, `#q=`, `#category=`, `#chain=`, `#wl=1`); a `hashchange`
listener keeps detail/filter state in sync. Dashboard source is 36,372 bytes
total, so the dossier's text shows it whole (no cut).

**Screenshots** (1440×900, Playwright + headless Chromium, free):
`1-board.png` (141 KB) the board, `2-detail.png` (195 KB) the detail view
for Steakhouse Financial, `3-filtered.png` (141 KB) the Watchlist-only
filtered view. Taken from the built `out1/dashboard/` output.

## Verification (19 Nov 2026, local, kit snapshot)

- Two runs into separate directories: `diff -r` clean — **byte-identical**.
- Board: 198 rows; watchlist covered **40/40**; ranks exactly 1..198;
  implied values non-increasing with rank, all distinct, $780,968–
  $584,881,060; farm steps 7 per row, 113–189 chars each; every implied
  value recomputed independently from TVL/growth/age and matched.
- Farm plans: page-rendered plans equal `board.json` for 14 protocols
  across categories (script in `/tmp/shot/check-farm.mjs`, Playwright).
- Dossier: rebuilt independently with the vendored library and compared
  byte for byte — **match** (686,710 bytes, ≤1.5 MiB); text 36,636 bytes
  (≤65,536, whole source shown, nothing cut); 3 images, cover is
  `1-board.png`.
- Gate sweep: dashboard 3 files / 36,372 bytes (≤400 files, ≤4 MiB);
  board 314,425 bytes (≤8 MiB); screenshots 1440×900, each ≤512 KiB;
  no `http://`/`https://` address in any dashboard, `main.ts` or `lib/`
  file. (The snapshot's own provenance URL lives in `data.js`'s data, as
  in the starter; the rendered page shows no URL.)
- Screenshots inspected visually: board, detail dialog, and filtered view
  all render correctly with data, bars, pills, and farm steps.

## Layout

```
main.ts                  entry point
lib/model.ts             radar-2 model, board builder, farm plans, snapshot parser
vendor/makefun-board.ts  kit's dossier library, byte for byte
dashboard/               index.html, app.css, app.js (static page, no network)
screenshots/              1-board.png, 2-detail.png, 3-filtered.png
token-radar-01-kit/      the downloaded kit (rules, starter, harness, snapshot)
kit.tar.gz               the kit as downloaded
out1/ out2/              the two verification runs (byte-identical)
```

## Remaining risks / blockers before submission

1. **Not submitted.** Submission needs the make.fun flow (repo + submit
   commands in the kit's RULES.txt / competition.yaml "Enter" section),
   which this task explicitly excluded. The parent agent must arrange it;
   do not submit from here.
2. **Transcript gate (unverified).** The kit says the transcript gate counts
   only code the uploaded agent sessions wrote. This entry's `main.ts`,
   `lib/model.ts` and dashboard were written by this agent session in this
   workspace; `vendor/makefun-board.ts` is the kit's required verbatim copy.
   Whether the submission path captures this session's transcript is for
   the parent agent to confirm — if the entry must be built inside a
   specific session/tool (e.g. superbot.gg), it may need rebuilding there.
3. **Sealed grading is the real test.** Local checks mirror the documented
   gate limits, but the sealed scorer runs in its own sandbox with the
   sealed snapshot (same bytes as the kit's, per the kit) and the judge is
   pairwise and non-deterministic. Nothing here guarantees a prize place.
4. **Judgement calls in the model.** The 0.30 base multiple, the growth
   clamp (-50/+150) and the maturity curve are modelling choices, stated
   openly; a judge could prefer the starter's flatter 0.25/±-60..120 version.
   The ranking is TVL-dominated under either, so the difference is small.
5. **Sentora Curator at rank 2** passes the field-based rule but is not on
   the watchlist (the snapshot's `parentProtocol`, which the file does not
   carry, is the likely reason). If the house's sealed watchlist logic is
   the judge's mental model, a reviewer could question it; the README and
   dashboard state the rule used, and no name was special-cased.
6. **Screenshots are of the local build** (`out1/`), taken with Playwright
   Chromium 1148. They depict the shipped dashboard accurately; if any
   dashboard file changes after this, retake them and re-run `main.ts`
   twice before submitting.
