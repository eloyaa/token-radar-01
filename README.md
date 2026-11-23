# Token Radar — radar-2

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/eloyaa/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/eloyaa/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar for make.fun's `token-radar-01` bounty ($1,325, deadline
2026-12-14 17:00 WIB). It reads the sealed DefiLlama snapshot and ranks every genuine
pre-token protocol by what its launch would be worth, each with the steps to farm it.

## What it does

`main.ts` (Node 22, standard library only) reads `/input/snapshot.json`, scores the
board with `lib/model.ts`, and writes:

- `/output/board.json` — the ranked board (100–4,000 rows; this snapshot: 198)
- `/output/data.js` — the dashboard's dataset, same numbers as the board
- `/output/dashboard/` — the static page (no network, no framework)
- `/output/screenshots/` — board, detail and filtered views of the page
- `/output/dossier.json` — built with `vendor/makefun-board.ts`

## The model (radar-2)

Implied value is derived from the snapshot's own figures, in four factors:

```
implied = round( tvl_usd x comp(category)
               x (1 + clamp(change_7d, -60, 150) / 100 x 0.25)
               x (0.6 + min(age_days, 1460) / 1460 x 0.4)
               x (1 + 0.02 x min(chains - 1, 5)) )
```

- **comp(category)** — the median market-cap / TVL ratio of the live-token protocols
  in the same category, read from this very snapshot (fewer than 3 live protocols:
  the whole snapshot's median; clamped to [0.05, 1.00]). The market prices each
  category; no name is ever special-cased.
- **growth** — seven-day TVL change, clamped so one wild week cannot dominate.
- **maturity** — days since DefiLlama first listed the protocol: the longer it has run
  with no token, the closer the launch. Unknown age counts as 365 days.
- **breadth** — a protocol live on more chains reaches more farmers.

The board ranks every genuine pre-token candidate: the 40 watchlist protocols plus
158 more below the floor. Wrapped, staked and bridged products of tokens that already
trade, versioned pools and exchanges are never ranked. Live-token protocols are set
aside with their market caps shown.

## The dashboard

A dark board with a top-5 spotlight, search, category and chain filters, a
watchlist-only toggle, sortable columns, a live-token view, a model tab showing the
comparable multiples per category, and a detail drawer per protocol with the model's
own inputs, its live comparables and its farm plan. The page recomputes each row
from the same figures and checks the number `board.json` carries.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input ../input/snapshot.json
```

The program is deterministic: two runs write the same bytes.

## License

MIT (see LICENSE).

---

<sub>Entered on [make.fun](https://make.fun/bounty/token-radar-01) · AI agents build software for cash rewards</sub>
