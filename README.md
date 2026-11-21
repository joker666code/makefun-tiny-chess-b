# makefun-tiny-chess-b

A tiny chess engine in 4 KB of Python, entered in the
[make.fun tiny-chess-01 bounty](https://make.fun/bounty/tiny-chess-01).

0x88 board, negamax + alpha-beta with iterative deepening, transposition
table, MVV-LVA + killer move ordering (256 layers), null-move pruning,
quiescence search with delta pruning, and a 38-entry opening book
(white's first move, precomputed offline). Fixed 20,000-node budget
per move. Deterministic: same position and history give the same move —
no wall clock in the search, as the competition's replay rule requires.

This is lane B of the tiny-chess-01 entry. It is built from the v3
engine (Michniewski piece-square tables, 50,000-node budget) by
lowering the node budget to 20,000 so every move stays well under the
10-second limit; the QS delta pruning and 256-layer killer table come
from the rescue analysis of the v2 timeouts. There is deliberately no
wall-clock guard: the rules replay every final trial byte for byte,
and a clock-dependent cutoff would make the replay nondeterministic.

## Practice trials (official scorer, 6 games each)

| Trial | Budget | Result | Elo | Timeouts | Longest think |
|---|---|---|---|---|---|
| trial-0 | 20,000 nodes | 2 won, 1 drawn, 3 lost | 1191 | 0 | 2.89 s |
| trial-1 | 20,000 nodes | 4 won, 1 drawn, 1 lost | 1545 | 0 | 2.70 s |
| **Mean** | | | **1368** | **0** | **2.89 s** |

Budget measurements on the same trial-0 games: 12,000 nodes scored
997, 20,000 nodes scored 1191; the 50,000-node parent version scored
1654 / 1451 on diagnostic gauntlets (mean 1552.5). The 20,000-node
budget was chosen as the point where the time headroom (longest think
under 3 s) and the Elo meet the 1,154 final floor with margin.

## Build

`bot/main.py` (4,086 bytes) is generated from `bot/engine.py` with the
kit's packer — never edited by hand:

```sh
node tiny-chess-01-kit/cli/makefun.mjs compress engine.py --out main.py
```

## License

MIT. See `LICENSE`.
