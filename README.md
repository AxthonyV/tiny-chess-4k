# tiny-chess-4k

A chess engine for the make.fun [tiny-chess-01](https://make.fun/bounty/tiny-chess-01)
bounty: the strongest play I could fit in 4,096 bytes of Python 3.13, standard
library only.

[![bounty](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/AxthonyV/tiny-chess-4k/rank.svg)](https://make.fun/bounty/tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/AxthonyV/tiny-chess-4k/reward.svg)](https://make.fun/bounty/tiny-chess-01)

## How it plays

`main.py` is the whole entry: an LZMA-packed stub that unpacks and runs the
engine. It talks to the contest env over HTTP, reads the position from the FEN
it is served each turn, searches, and replies in UCI.

- **Board**: a 0x88 mailbox — a square is `rank*16+file`, off-board exactly
  when `sq & 0x88` — with full legal move generation (castling, en passant,
  promotion, pins). Validated with perft on five positions, exact to d4.
- **Search**: iterative-deepening negamax with alpha-beta and a
  principal-variation search, quiescence (captures, promotions, check
  evasions), a transposition table, MVV-LVA / killer / history move ordering,
  null-move pruning and a check extension.
- **Evaluation**: material, tapered piece-square tables (a midgame set plus a
  king-endgame table), and a bishop-pair bonus, from the side-to-move's view.
- **Determinism**: the search runs to a **fixed node budget**, never a clock,
  with no unseeded randomness — so a replay plays the same moves, move for move.

## Build

The readable source is `engine.py`; the committed `main.py` is produced with

    node <kit>/cli/makefun.mjs compress engine.py --out main.py

## Licence

[MIT](LICENSE).
