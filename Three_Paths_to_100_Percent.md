# Three Paths to 100% on the Forum Hardest Puzzles

*48,765 puzzles rated SE 11.0+ — the hardest Sudoku puzzles ever collected. All solved by pure logic.*

---

## The Discovery

There isn't just ONE way to solve the forum hardest set. There are **three independent approaches**, each achieving 100% with zero backtracking. A solver implementer can choose whichever fits their architecture best. Also Recursive Forcing Nets also accomplishes the goal!!! 

---

## Path 1: DeepResonance (Cascaded Contradiction)

**The idea:** Place a candidate, propagate all consequences, then run Forcing Chains on the result. If Forcing Chains place something, propagate THAT and run Forcing Chains AGAIN. Cascade up to 3 levels deep. If anything contradicts, the original candidate is eliminated.

```
Place D at cell X
  └─ Propagate L1+L2 → contradiction? ELIMINATE
       └─ Run ForcingChain → finds placement P1
            └─ Propagate P1 → contradiction? ELIMINATE
                 └─ Run ForcingChain → finds P2
                      └─ Propagate P2 → contradiction? ELIMINATE
```

**Results:** 91.8% of puzzles need this technique. It's the primary solver for SE 11.0+ puzzles.

**Example — Puzzle #2:**
```
98.7..6..75..4......3..8.7.8....9.5..3.2..1.....4....6.7...4.3....8..4......1...2
DeepResonance x2, SimpleColoring x2, XCycle x1, ALS_XZ x7, XWing x2
```
DeepResonance fires twice to break through the two hardest stall points, then pattern techniques clean up.

---

## Path 2: Full Pipeline Forcing (FPF) — The Easiest to Implement

**The idea:** Find a bivalue cell (only 2 candidates). Run your ENTIRE solver on each branch. If one branch stalls or contradicts, the other candidate must be correct.

This is the simplest path for someone who already has a working solver. You literally run your solver inside itself.

```
Cell X has candidates {A, B}

Branch A: Place A, run full solver pipeline
  → Solver succeeds? A might be correct.
  → Solver contradicts? A is impossible. Place B.

Branch B: Place B, run full solver pipeline
  → Same logic.

If both succeed: no conclusion (try another cell)
If one contradicts: the other is proven
If both agree on a placement elsewhere: that placement is proven
```

**Why this works for Andrew:** He already has a solver with Forcing Nets, ALS, fish, etc. He just needs to:
1. Clone the board state
2. Place one candidate of a bivalue cell
3. Run his existing solver on the clone
4. Check if it contradicts or succeeds
5. Repeat for the other candidate

That's it. No new technique logic. Just recursion.

**Results — same puzzles, different path:**

```
Puzzle: 98.7..6..75..4......3..8.7.8....9.5..3.2..1.....4....6
  Path 1 (DeepResonance): DeepResonance x2, SimpleColoring x2, XCycle x1, ALS_XZ x7, XWing x2
  Path 2 (FPF):           FPF x1, FPCE x1, XWing x1, SimpleColoring x1, ALS_XZ x2, GF2_XOR x1

Puzzle: 98.7..6..5...9..7...7..4...4...327....65...9.......3.1
  Path 1 (DeepResonance): DeepResonance x2, D2B x2, XCycle x1, ALS_XZ x2, ALS_XYWing x2, XWing x1
  Path 2 (FPF):           FPF x1, ALS_XZ x2, GF2_XOR x1

Puzzle: 98.7..6..7.5.......4..5....3..8..9......2..1......13.4
  Path 1 (DeepResonance): DeepResonance x1, FPCE x1
  Path 2 (FPF):           FPF x1, ALS_XZ x2, FPC x1, XCycle x1

Puzzle: 98.7..6..5...8..7...7..4...32.........94...8.....1...2
  Path 1 (DeepResonance): DeepResonance x1, D2B x1, FPCE x2
  Path 2 (FPF):           FPF x1, XCycle x1, ALS_XYWing x2, Swordfish x1, ALS_XZ x6
```

Notice: FPF fires exactly **once** in each puzzle — one strategic branch point breaks the logjam, then standard techniques cascade to completion.

---

## Path 3: GF(2) Linear Algebra + FPF

**The idea:** Encode all Sudoku constraints as equations over GF(2) (binary field). Gaussian elimination resolves all linear dependencies instantly. The remaining non-linear constraints are handled by FPF.

This is the mathematical approach — no chain following, no pattern matching. Pure algebra.

```
Build GF(2) constraint matrix (cell + row + col + box constraints)
  └─ Two-phase Schur complement elimination
       └─ Phase 1: Box-local (9 independent ~20-var problems)
       └─ Phase 2: Cross-box residual
  └─ Extract placements (variable = 1) and eliminations (variable = 0)
  └─ FPF handles remaining non-linear patterns
```

**Results:** GF(2) + FPF solves the same puzzles DeepResonance does, often with fewer total technique firings because the algebra handles constraints that would take multiple DeepResonance rounds.

---

## Side-by-Side Comparison

| | Path 1: DeepResonance | Path 2: FPF | Path 3: GF(2)+FPF |
|---|---|---|---|
| **Core idea** | Cascade forcing chains 3 deep | Run full solver on each branch | Linear algebra + branching |
| **New code needed** | Trial propagation + chain cascade | Just recursion (solver calls itself) | GF(2) elimination engine |
| **Implementation effort** | Medium (state cloning + depth control) | **Easy** (wrap existing solver) | Hard (matrix math) |
| **Speed** | Fast (early exit on contradiction) | Moderate (runs full solver per branch) | Fast (algebra is O(n^3) once) |
| **Solve rate** | 100% | 100% | 100% |
| **Best for** | Pattern-based solvers | Anyone with a working solver | Math-oriented implementations |

---

## Recommendation for Andrew

**Start with Path 2 (FPF).** You already have a solver. You already have Forcing Nets. Just:

1. After all your existing techniques stall, find a bivalue cell
2. Clone the board
3. Place candidate A, run your full solver
4. If it contradicts → place candidate B (proven)
5. If it succeeds → try candidate B on a fresh clone
6. If both succeed but agree on a placement → that placement is proven

Your Forcing Nets at depth 6 get 9%. Adding this one recursive wrapper gets you to 100%. The technique instances that currently appear AFTER Forcing Nets in your cascade — the ones you said "99% seem to be replaced by Forcing Nets" — FPF catches all of them and more.

Later, if you want the elegance of Path 1 (DeepResonance), you can make your Forcing Nets recursive — when a Forcing Net places a digit, propagate and search for another Forcing Net on the result. Same principle, your own building blocks.

---

## The Data

All 48,765 puzzles from `puzzles5_forum_hardest_1905_11+` solved with full technique lists:

- Results: [forum_hardest_larsdoku.txt](forum_hardest_larsdoku.txt)
- Live solver: [https://larsdoku.netlify.app/](https://larsdoku.netlify.app/)
- Source: [https://github.com/oppressionslayer/larsdoku-solver](https://github.com/oppressionslayer/larsdoku-solver)
- Technique writeups: [https://github.com/oppressionslayer/wsrf-sudoku-solved-series](https://github.com/oppressionslayer/wsrf-sudoku-solved-series)

---

## Try It Yourself

These puzzles demonstrate the difference between the paths:

```
98.7..6..75..4......3..8.7.8....9.5..3.2..1.....4....6.7...4.3....8..4......1...2
98.7..6..5...9..7...7..4...4...327....65...9.......3.11...8.5...2.........54...8.
98.7..6..7.5.......4..5....3..8..9......2..1......13.42...8..93.7....2....92...6.
98.7..6..5...8..7...7..4...32.........94...8.....1...2.9...31....68...5.....9...7
........1.....2.3...4.5.6.....3...7...8.674...7...1..2..5......69..84...84....9..
```

Load any of these in the solver and watch DeepResonance crack them. Then exclude DeepResonance and watch FPF take the same puzzles through a completely different path.

---

*One problem. Three proofs. Zero backtracking.*
