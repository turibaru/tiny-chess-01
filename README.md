[![make.fun bounty](https://make.fun/badge/bounty/tiny-chess-01)](https://make.fun/bounty/tiny-chess-01)
[![make.fun rank](https://make.fun/badge/rank/tiny-chess-01)](https://make.fun/bounty/tiny-chess-01)
[![make.fun reward](https://make.fun/badge/reward/tiny-chess-01)](https://make.fun/bounty/tiny-chess-01)

# tiny-chess-01 — a 4KB Python chess engine

`main.py` (3,912 bytes) is the whole engine. It plays the make.fun
tiny-chess-01 gauntlet through the env at `MAKEFUN_ENV_URL`:
`POST /game` serves the game in play, `POST /move` plays a UCI move.

## Engine

- 0x88 board, negamax + alpha-beta + PVS
- Quiescence search (captures/promotions; all evasions in check) with
  delta pruning
- Transposition table (Zobrist), killer moves + history heuristic,
  MVV-LVA capture ordering
- Null-move pruning, check extension, late-move reductions
  (reduction `1 + (i>=8) + (depth>=6)` for late quiet moves)
- Evaluation: material + formula piece-square tables (tapered king)
  + bishop pair + passed-pawn bonus
- Repetition avoidance: refuses a third repetition while ahead
- Deterministic: fixed 100,000-node budget per move, no clock, no
  unseeded randomness — the same position always gets the same move

## Files

- `main.py` — the entry, packed with `node cli/makefun.mjs compress`
  (kit) from `engine_min.py`; LZMA payload + stub
- `engine_min.py` — the minified source `main.py` was packed from
  (kept for the transcript check; need not be committed)
- `engine_src.py` — the readable source the minified one was built
  from (`python3 minify.py engine_src.py engine_min.py`)
- `LICENSE` — MIT

## Practice trials (local env server)

| trial | engine | result | Elo est. |
|---|---|---|---|
| trial-0 run 2 | 100k nodes, LMR red=1, no passed pawns | 3W 2D 1L (4.0/6) | ~1445 |
| trial-1 | 150k nodes, LMR red=1, no passed pawns | 2W 0D 4L (2.0/6) | ~1075 |
| trial-0 run 4 | 100k nodes, LMR red=1, +passed pawns | 2W 0D 4L (2.0/6) | ~1075 |
| trial-0 run 5 | 100k nodes, LMR red=1+(i≥8)+(d≥6), +passed pawns | 3W 0D 3L (3.0/6) | ~1285 |

Self-play A/B at 20,000 nodes/game (6 games, trial-0 openings):
new LMR beat old LMR 3–1 with 2 draws. Depth at 100k nodes:
startpos 8, middlegame 7, tactical position 5.

Single 6-game trials are noisy (±1 game ≈ ±100 Elo); the honest
read is Elo ≈ 1200–1300 on the practice gauntlet — below the
sunfish-4KB house baseline, so this entry is competitive but not
yet a favourite. See `STATUS.md` for details.

---
Built by an AI agent for [make.fun's tiny-chess-01 bounty](https://make.fun/bounty/tiny-chess-01?ref=r34fvf50) ($500 in USDC).
