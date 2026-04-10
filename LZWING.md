# LZWing — A Sound Sudoku Technique

**LZWing** (short for "Loose-Z Wing") is a 4-cell pattern elimination technique
related to WXYZ-Wing. It's the strictly sound member of the "restricted common"
wing family: every elimination LZWing emits is logically forced by the 4-cell
pattern alone, not coincidentally correct.

## The pattern

Four cells whose candidate union is **exactly four different digits**. Call
those digits W, X, Y, and Z. Z is the "restricted common" — a digit that
appears in **at least two** of the four cells, and **all cells containing Z
must pairwise see each other** (i.e., share a row, column, or box).

The four pattern cells must be peer-connected as a single group — every cell
must share at least one unit with at least one other pattern cell so the
pattern forms one connected constraint region.

## The claim

**In any valid solution, at least one of the Z-carrier cells must take the
value Z.** Therefore Z can be eliminated from any cell outside the pattern
that sees every Z-carrier cell (because one of them must already be Z, and
peers cannot share a digit).

## When LZWing is sound — the 3-colouring check

The claim "at least one Z-carrier must be Z" is true **if and only if** you
cannot legally fill all four pattern cells using only the other three digits
{W, X, Y}. That's a small 3-colouring problem on the peer subgraph of the
4 cells: try to assign each cell one of the non-Z digits such that no two
peer cells share a digit, respecting each cell's remaining candidates.

- **If no valid assignment exists** → removing Z creates a contradiction →
  one of the Z-carriers must be Z → LZWing fires.
- **If a valid assignment exists** → the pattern does NOT force Z on any
  carrier → LZWing **does not** fire, because the elimination wouldn't be
  logically sound even though it might happen to be correct for the
  specific puzzle.

The 3-colouring check is cheap: four cells, three digits, bounded by
3⁴ = 81 branches (in practice far fewer because cell candidates shrink the
search immediately). LZWing runs this check on every candidate pattern
before emitting an elimination.

## A worked example (from Andrew Stuart's Weekly Expert #15)

    Puzzle: 3..7...1...7.2....82.5....62.94......5.....2......23.91....9.54....8.1...6...5..3

After standard L1/L2 and ALS-XZ, the solver stalls on this puzzle until
LZWing fires on the following 4-cell pattern:

| Cell  | Candidates | Role      |
|-------|------------|-----------|
| R1C2  | `{4, 9}`    | Z-carrier |
| R2C2  | `{1, 4, 9}` | Z-carrier |
| R3C3  | `{1, 4}`    | non-Z     |
| R3C6  | `{1, 3, 4}` | non-Z     |

- Candidate union: `{1, 3, 4, 9}`
- Z (restricted common): **9**
- The two Z-carriers R1C2 and R2C2 share column 2 and box 1, so they see
  each other.
- The four pattern cells are peer-connected: R1C2 ↔ R2C2 (col / box),
  R1C2 ↔ R3C3 (box), R2C2 ↔ R3C3 (box), R3C3 ↔ R3C6 (row).

### The 3-colouring check (why the pattern is sound)

Remove 9 from all four cells and try to assign each cell a digit in
`{1, 3, 4}`:

1. **R1C2** has `{4, 9}` → after removing 9 it only has `{4}`.
   → forced to **4**.
2. **R2C2** has `{1, 4, 9}` → after removing 9 it's `{1, 4}`.
   Since R2C2 is a peer of R1C2 (col 2), it can't be 4.
   → forced to **1**.
3. **R3C3** has `{1, 4}`. R3C3 is a peer of R2C2 (box 1), so it can't be 1.
   R3C3 is also a peer of R1C2 (box 1), so it can't be 4.
   → **no legal digit**. Contradiction.

Because the Z-free assignment is impossible, **at least one of R1C2 or R2C2
must be 9** in every valid solution.

### The elimination

Any cell that sees both R1C2 and R2C2 has Z=9 "used up" by one of them in
every solution, so that cell cannot itself be 9:

- **R2C1**: peer of R1C2 via box 1, peer of R2C2 via row 2. → eliminate 9.
- **R8C2**: peer of R1C2 and R2C2 via column 2. → eliminate 9.

After these eliminations the solver returns to standard L1/L2/ALS-XZ and
finishes the puzzle with only crossHatch, nakedSingle, lastRemaining,
ALS-XZ, and this one LZWing fire — no deeper reasoning required.

## Why "Loose-Z" Wing?

The name comes from the discovery history. The larsdoku solver originally
shipped a WXYZ-Wing implementation with an over-permissive "restricted
common Z" check that accepted any quad where Z appeared in two or more
cells, without verifying the pigeonhole structure. Most of its emissions
were coincidentally correct (the wrong-but-lucky kind), but a subset were
genuinely unsound and caused a cascade of downstream wrong placements in
the mith 158K benchmark. The fix was to split the detector in two:

- `detect_wxyz_wing` — the historical heuristic, now only firing on Z≥3.
  Documented as a heuristic and left in place for puzzles that depend on
  its lucky-correct emissions.
- `detect_lzwing` — validates every quad/Z candidate against the
  3-colouring check above before emitting. Sound on Z=2, Z=3, or Z=4.

On the Weekly Expert 686 curated benchmark, `detect_wxyz_wing` now fires
**zero** times and `detect_lzwing` fires **464 times** — the entire
"restricted common wing" branch of that benchmark is handled by sound
logic alone. Every LZWing elimination is provably forced by its 4-cell
pattern.

## Relationship to other techniques

LZWing is a degenerate case of the **Almost Locked Set pigeonhole** family.
An ALS is a set of *n* cells holding exactly *n+1* candidates; removing
one "unlocks" the set as a naked tuple. In LZWing, the "ALS" is the
restricted-common Z: remove Z from the 4-cell pattern and what remains
must fit in fewer digits than cells, which is impossible under peer
constraints. Where ALS-XZ and ALS-XY-Wing formalise this between separate
ALSs connected by a restricted common, LZWing applies the same structural
reasoning within a single 4-cell subset.

LZWing is also closely related to the canonical **WXYZ-Wing** (one 4-cand
pivot cell plus three bivalue wings, each containing Z plus one unique
non-Z digit). The canonical form is a special case of the LZWing pattern —
any canonical WXYZ-Wing also passes the LZWing 3-colouring check. LZWing
is strictly broader because it also catches non-canonical Z=2, Z=3, and
Z=4 patterns that don't fit the pivot-wings shape but still pass the
soundness test.

## Summary

- **Input**: 4 cells, peer-connected, with exactly 4 distinct candidates in
  their union.
- **Parameter**: a digit Z that appears in ≥2 of the 4 cells and whose
  carriers all see each other.
- **Check**: no valid assignment of the other 3 digits to the 4 cells
  exists under peer constraints (verified by exhaustive 3-colouring on
  the 4-cell peer subgraph).
- **Eliminate**: Z from any cell that is a peer of every Z-carrier.
- **Soundness**: every emission is provably forced by the pattern alone.
  No coincidence, no luck.
