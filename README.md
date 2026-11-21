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
- fixed node budget per move, no clock and no randomness, so replays match move for move

`main.py` is an LZMA-packed build (`makefun compress`) of the readable engine source.

## Build

The readable source is packed into `main.py` with `makefun compress`. The search
budget is 80k nodes per move (doubled in endgames, halved after 24M cumulative
nodes). Grade locally with:

    makefun grade-local --kit . --bot <repo> --driver process

## Results

Local practice grading of the shipped 80k-node build: mean practice Elo ≈ 1706
(trial 0: 1654, trial 1: 1654, trial 2: 1809). Determinism verified: a repeat
trial produced an identical transcript. A 100k-node build was tested but not
shipped (mean ≈ 1618, and slower).

MIT licensed.
