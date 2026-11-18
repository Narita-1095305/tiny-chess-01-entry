# tiny-chess-01

A standalone, deterministic Python chess entry. `main.py` contains the entire
engine in a Latin-1 string holding an LZMA payload; Python's standard library
unpacks it in memory. No auxiliary engine files are loaded at runtime.

The readable implementation is `dev/engine.py`. Its 0x88 board uses incremental
middle/endgame piece-square scores, cached pawn structure, bishop pairs, rook
files, king shelter and endgame king proximity. Search uses iterative deepening,
PVS, a transposition table, null moves, late move reductions, killers and history.
Quiescence is bounded, including check evasions. All promotions are generated.
The last completed iteration supplies the move, with an environment-provided
legal fallback. Each move gets a fixed 74,000-node budget; no clock or randomness
influences play. Tables reset on every call.

Design informed by the supplied Design A reference and its failure analysis;
implemented here with a separate readable source and packing/verification tools.
The environment driver follows the starter's POST /game and POST /move protocol
at MAKEFUN_ENV_URL. Nothing has been submitted or graded against house anchors.

Offline reproduction, from this directory:

```
python3 dev/pack.py
python3 dev/verify.py
python3 dev/verify.py --spots
python3 dev/protocol_check.py
```

Verification covers five standard perft suites, an independent legal-move
reference, incremental evaluation, the legality shortcut, special moves, and
repeat/source/packed choices on all 18 practice opening positions. Black-entry
openings are tested immediately after the supplied eight plies, before any
unknown house reply. Detailed CPU times and completed depths are recorded in
`dev/spot-results.json`.

Known limits: selective search can miss tactics; capped check evasions can
misvalue deep forcing lines. No local Elo claim is made. Verification used Python
3.12.3; the entry targets the required Python 3.13 using compatible stdlib APIs.

MIT licensed; see LICENSE. The repository snapshot consists of main.py, LICENSE,
README.md and .makefun/owner.json; dev/ is retained for review and reproduction.
