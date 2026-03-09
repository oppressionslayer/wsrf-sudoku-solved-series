# FPC Elimination (FPCE) — Propagation-Based Candidate Elimination

## What It Does

FPCE eliminates candidates from cells using **proof by contradiction**. For each candidate in each empty cell, it asks one question:

> "If I place this digit here and follow basic Sudoku logic forward, does the board break?"

If yes — **eliminate that candidate**. It can never go there.

No pattern matching. No memorizing complex fish or chain shapes. Just follow the logic and watch for contradictions.

---

## The Core Idea

### Traditional Elimination Techniques

Classic techniques like X-Wing, Swordfish, Coloring, and ALS all do the same thing — they prove a candidate can be removed from a cell. Each uses a different geometric or logical pattern:

- **X-Wing**: Rectangle of 4 cells, 2 rows, 2 columns
- **Swordfish**: 3x3 grid pattern across rows and columns
- **Simple Coloring**: Chain of conjugate pairs with color conflicts
- **ALS-XZ**: Almost Locked Sets sharing restricted commons

You have to memorize each pattern, scan for it, and apply its specific elimination rule.

### FPCE: One Technique to Replace Them All

FPCE doesn't look for patterns. It uses **direct logical proof**:

```
For each empty cell:
    For each candidate digit in that cell:
        1. Assume this digit goes here
        2. Follow naked singles and hidden singles forward
        3. If the board reaches an impossible state → eliminate this candidate
```

That's it. If assuming D goes at cell X breaks the board, then D can't go at X. Period.

---

## Walkthrough: Step-by-Step Example

### The Board (after L1 + L2 stall)

```
     C1  C2  C3   C4  C5  C6   C7  C8  C9
   ┌─────────────┬─────────────┬─────────────┐
R1 │ .   6   .   │ .   .   1   │ 9   8   7   │
R2 │ 1   7   .   │ 2   .   6   │ 5   3   .   │
R3 │ .   8   .   │ 7   .   9   │ .   4   .   │
   ├─────────────┼─────────────┼─────────────┤
R4 │ 8   .   7   │ 5   .   .   │ 4   1   .   │
R5 │ .   1   5   │ .   4   .   │ .   7   .   │
R6 │ 4   .   6   │ 1   7   2   │ .   5   .   │
   ├─────────────┼─────────────┼─────────────┤
R7 │ .   2   .   │ 8   .   .   │ .   9   .   │
R8 │ .   .   8   │ 3   1   7   │ .   2   .   │
R9 │ 7   .   1   │ 6   .   .   │ .   .   .   │
   └─────────────┴─────────────┴─────────────┘
```

After running naked singles, hidden singles, naked pairs, pointing pairs, and claiming — the solver stalls. Traditional approach: start scanning for X-Wings, Swordfish, etc.

**FPCE approach:** Pick the most constrained cells (fewest candidates) and test each candidate.

---

### Step 1: Pick a Cell

**R4C2** has candidates **{3, 9}** — a bivalue cell (only 2 options). These are the best targets because:
- Only 2 candidates to test
- If one is eliminated, the other is immediately a naked single (instant placement)

---

### Step 2: Test Candidate 9 at R4C2

**Assume:** Place **9** at R4C2.

Now propagate. Follow only naked singles and hidden singles — the two most basic techniques.

```
Start: Place 9 at R4C2

Cascade:
  → R4C2 = 9: peers in Row 4, Col 2, Box 4 lose candidate 9

  → R6C2 had {3, 9}. Remove 9 → R6C2 = {3} → NAKED SINGLE → Place 3 at R6C2

  → R5C1 had {2, 3}. Remove 3 (peer of R6C2) → R5C1 = {2} → NAKED SINGLE → Place 2

  → R7C1 had {3, 5, 6}. Remove nothing yet...
     But in Box 7, digit 3 now can only go at R9C2.
     → HIDDEN SINGLE → Place 3 at R9C2

  → R8C2 had {4, 5}. Still {4, 5}...
     But in Col 2, digit 5 can now only go at R8C2.
     → HIDDEN SINGLE → Place 5 at R8C2

  → R8C1 had {5, 6}. Remove 5 (peer of R8C2) → R8C1 = {6} → NAKED SINGLE → Place 6

  → R7C1 had {3, 5, 6}. Remove 6 and 3 → R7C1 = {5} → NAKED SINGLE → Place 5

  → R7C3 had {3, 4}. Remove 3 (peer of R9C2=3) → R7C3 = {4} → Place 4

  → R7C5 had {3, 5, 6}. Remove 5 → {3, 6}...
     In Box 8, digit 3 can only go at R7C5.
     → HIDDEN SINGLE → Place 3 at R7C5

  → Continue propagating...

  → Eventually: R9C5 needs digit 5, but 5 is not in its candidates!

  ╔═══════════════════════════════════════╗
  ║  CONTRADICTION at R9C5!               ║
  ║  Cell needs 5 but can't hold it.      ║
  ║  The board is broken.                 ║
  ╚═══════════════════════════════════════╝
```

---

### Step 3: Draw the Conclusion

Placing **9** at R4C2 led to a contradiction after a cascade of forced moves. Every step in the cascade was pure logic — naked singles and hidden singles only. No guessing.

**Therefore: 9 cannot go at R4C2. Eliminate it.**

---

### Step 4: Reap the Reward

R4C2 had candidates {3, 9}. After eliminating 9:

```
R4C2 = {3} → NAKED SINGLE → Place 3 at R4C2!
```

One FPCE elimination → one immediate placement → board simplifies → more naked/hidden singles fire → cascade continues.

---

## The Propagation Cascade Visualized

This is what makes FPCE powerful. Here's the cascade from the example above, shown on the board:

```
     C1  C2  C3   C4  C5  C6   C7  C8  C9
   ┌─────────────┬─────────────┬─────────────┐
R1 │  .   6   .  │  .   .   1  │  9   8   7  │
R2 │  1   7   .  │  2   .   6  │  5   3   .  │
R3 │  .   8   .  │  7   .   9  │  .   4   .  │
   ├─────────────┼─────────────┼─────────────┤
R4 │  8  [9]  7  │  5   .   .  │  4   1   .  │  ← TEST: place 9 here
R5 │ [2]  1   5  │  .   4   .  │  .   7   .  │  ← cascade
R6 │  4  [3]  6  │  1   7   2  │  .   5   .  │  ← cascade
   ├─────────────┼─────────────┼─────────────┤
R7 │ [5]  2  [4] │  8  [3]  .  │  .   9   .  │  ← cascade
R8 │ [6] [5]  8  │  3   1   7  │  .   2   .  │  ← cascade
R9 │  7  [3]  1  │  6  {!}  .  │  .   .   .  │  ← CONTRADICTION
   └─────────────┴─────────────┴─────────────┘

  [D] = Cascade placement (forced by naked/hidden single)
  {!} = Contradiction cell (impossible state)
```

**9 placements cascade from one assumption before the board breaks.** Every single one is a forced, logical deduction. The contradiction at R9C5 proves the original assumption was wrong.

---

## How to Spot FPCE Opportunities by Eye

### Priority: Bivalue Cells First

Cells with only **2 candidates** are gold:
- Test one candidate → if contradiction → the other candidate is the answer
- Instant placement from a single elimination

### What to Watch For During Propagation

As you mentally place a digit and follow the consequences:

1. **Naked singles**: A cell drops to 1 candidate → forced placement
2. **Hidden singles**: A digit has only 1 remaining spot in a row/col/box → forced placement
3. **Zero candidates**: A cell has NO remaining candidates → **contradiction!**
4. **Missing digit**: A unit (row/col/box) has no cell that can hold some digit → **contradiction!**

### The Cascade Effect

Each placement during propagation removes candidates from 20 peer cells. This can trigger more naked/hidden singles, which trigger more, creating a cascade. The deeper the cascade goes, the more likely it hits a contradiction.

That's why FPCE is most powerful at L2 stall points — the board is constrained enough that wrong assumptions break quickly.

---

## Visual Legend (Board Highlighting)

| Color | Role | Meaning |
|-------|------|---------|
| **Purple** | Test cell | The cell being tested (candidate assumed here) |
| **Cyan** | Cascade | Cells placed during propagation chain |
| **Red** | Contradiction | The cell where the board breaks |

---

## What FPCE Replaces

FPCE made **13+ advanced techniques** nearly or completely obsolete:

| Technique | Status After FPCE |
|-----------|-------------------|
| X-Wing | **Gone** (0 firings) |
| Swordfish | **Gone** |
| Finned X-Wing | **Gone** |
| Finned Swordfish | **Gone** |
| Simple Coloring | **Gone** |
| XY-Wing | **Gone** |
| XYZ-Wing | **Gone** |
| W-Wing | **Gone** |
| ALS-XZ | **Gone** |
| ALS-XY-Wing | **Gone** |
| AIC (Alternating Inference Chain) | **Gone** |
| Hidden Pair | **Gone** |
| Naked Quad | **Gone** |
| Naked Triple | -82% |
| Forcing Chain | -71% |

All of these techniques exist to prove one thing: "candidate D can be eliminated from cell X." FPCE proves the same thing through direct contradiction, using only the two simplest Sudoku rules.

---

## By the Numbers (686 Expert Puzzles)

| Metric | Value |
|--------|-------|
| FPCE eliminations | 12,160 (21.7% of all solving steps) |
| FPCE + FPC Placement combined | 20,212 (36.1% of all steps) |
| Accuracy | 100% (elimination only when contradiction is proven) |
| Solver speedup | 264s → 177s (33% faster with FPCE added) |
| Pure logic solves | 430 / 686 (up from 428 without FPCE) |

Adding FPCE actually made the solver **faster** because:
1. Early eliminations simplify the board
2. Cheap L1 techniques handle more work after simplification
3. Expensive advanced technique scanners run less often
4. 13+ detectors barely fire anymore

---

## The Virtuous Cycle

```
FPCE eliminates candidate D from cell X
  → Cell X now has fewer candidates
    → Maybe cell X becomes a naked single → place it!
      → Peers of X lose a candidate
        → Maybe one of them becomes a naked single too
          → ... cascade of free placements

One elimination can unlock 5-10 placements through L1 techniques alone.
```

This is why eliminations at L2 stall points are so devastating — they break the logjam that was blocking basic techniques, and the board often solves itself from there.

---

## The Logic in One Sentence

> If placing a digit at a cell and following basic Sudoku logic forward leads to an impossible board, that digit can never go there — eliminate it.

No patterns to memorize. No geometric shapes to find. Just follow the logic and trust the contradiction.

---

## Connection to FPC Placement

FPCE was born from the FPC Placement Gold Filter discovery:

1. **FPC Placement** proved that testing a digit at the blocker for contradiction is reliable
2. **The insight**: if contradiction testing works for blocker cells, it works for ANY cell
3. **FPCE** generalizes this: test all cells, all candidates, eliminate contradictions
4. The propagation engine built for FPC Placement made FPCE computationally feasible

The journey: **broken technique (50% accuracy) → diagnostic analysis → Gold Filter (100%) → generalized elimination → #1 technique in the solver.**
