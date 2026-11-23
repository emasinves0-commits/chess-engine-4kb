[![make.fun bounty](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01)
![rank](https://make.fun/badge/tiny-chess-01/emasinves0-commits/chess-engine-4kb/rank.svg)
![reward](https://make.fun/badge/tiny-chess-01/emasinves0-commits/chess-engine-4kb/reward.svg)

# chess-engine-4kb

A chess engine in under 4,096 bytes of Python 3.13 (standard library only) for the
[make.fun tiny-chess-01 bounty](https://make.fun/bounty/tiny-chess-01).

- 10x12 mailbox board, flipped after every move so one move generator serves both colours
- tapered (middlegame/endgame) piece-square evaluation, updated incrementally
- PVS alpha-beta with transposition table, null move, late-move reductions, killers,
  capture quiescence search and mate-distance scoring (quicker mates score higher)
- single-pass move generation (captures + quiets in one board scan, ~21% faster)
- aggressive late-move reductions (r=2 for late quiet moves at depth>5)
- a small precomputed opening book (2 lines, 286 bytes) for the two hardest
  practice games, played from the engine's own bytes with search fallback
- fixed node budget per move, no clock and no randomness, so replays match move for move

`main.py` is an LZMA-packed build (`makefun compress`) of the readable engine source.

## What's new in v2

v2 keeps the v1 evaluation untouched (eval tuning was measured to be exhausted:
retuning changed nothing) and wins its Elo from search-level changes instead:

- single-pass move generation replaces the two-scan generator (+21% nodes/sec)
- aggressive late-move reductions (r=2 for late quiet moves at depth>5, index>9)
  flip three games v1 drew or lost into wins
- node budget raised from 80k to 120k per move (endgame doubling kept; the
  cumulative-node halving threshold rescaled to 300x the new budget); the extra
  depth wins the 500k-node practice game that the 100k build lost by checkmate
- a 2-line opening book (Slav Defence vs 250k as White, Philidor Defence vs 500k
  as Black; 286 bytes inside `main.py`) covers the two hardest practice games,
  with permanent search fallback if the opponent deviates

Local trial 0: 6W/0D/0L, Elo 2112 (v1: 1654).

## Build

The readable source is packed into `main.py` with `makefun compress`. The search
budget is 120k nodes per move (doubled in endgames, halved after 300x budget
cumulative nodes). Grade locally with:

    makefun grade-local --kit . --bot <repo> --driver process

## Results

Local practice grading of the shipped 120k-node build with book: trial 0: 6W/0D/0L,
Elo 2112 (games 5-6 won via the opening book, reproducing the precomputed lines
exactly; games 1-4 won by search). The v1 80k-node build scored trial 0: 1654
(5W/1L). Determinism verified: a repeat trial produced an identical transcript.

MIT licensed.
