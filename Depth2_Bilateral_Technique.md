# Depth-2 Bilateral FPCE

*The technique that reduced zone deduction from 248 puzzles to 1.*

---

## The Idea

Standard FPCE asks: "If I place digit D at cell X, does the board break?"

Depth-2 Bilateral asks a deeper question: **"No matter which digit goes in cell X, does digit D always get eliminated from cell Y?"**

If the answer is yes, then D can be safely eliminated from Y — no matter what the solution is.

---

## How It Works

### Step 1: Pick a Pivot Cell

Choose a cell with 2-4 candidates. Fewer candidates = fewer branches = faster.

**Example:** Cell R3C5 has candidates {2, 7, 9}.

### Step 2: Branch on Each Candidate

For each candidate in the pivot cell, hypothetically place it and propagate:

**Branch A — Place 2 at R3C5:**
- Propagate naked singles, hidden singles, naked pairs, pointing pairs, claiming
- Board settles into a new state
- Run FPCE on that state: find which candidates contradict
- Result: "In this world, 5 at R7C2 contradicts"

**Branch B — Place 7 at R3C5:**
- Propagate forward
- Run FPCE on resulting state
- Result: "In this world, 5 at R7C2 also contradicts"

**Branch C — Place 9 at R3C5:**
- Propagate forward
- Run FPCE on resulting state
- Result: "In this world, 5 at R7C2 contradicts too"

### Step 3: Find Common Eliminations

The pivot cell MUST contain one of {2, 7, 9} — that's a fact.

In ALL three worlds, 5 at R7C2 leads to contradiction.

Therefore: **5 can never go at R7C2**, regardless of what R3C5 turns out to be.

### Step 4: Eliminate and Continue

Remove 5 from R7C2's candidates. The board simplifies. L1 techniques (naked singles, hidden singles) take over and cascade forward.

---

## Why It Works

This is **proof by exhaustive case analysis**:

1. Cell X must be one of its candidates (that's how Sudoku works)
2. We test every possibility for X
3. Under every possibility, candidate D at cell Y leads to a broken board
4. Therefore D cannot go at Y — it's impossible in the actual solution

No guessing. No backtracking. Pure deductive logic applied across all branches.

---

## The Discovery

The journey to Depth-2 Bilateral:

1. **FPC Elimination (FPCE)** — place a candidate, propagate L1 (naked + hidden singles), check for contradiction. Made 13+ advanced techniques obsolete.

2. **Deep FPCE** — same idea but propagate L1 + L2 (naked pairs, pointing pairs, claiming, naked triples). Tripled contradiction coverage from 21% to 70% of stall points.

3. **Depth-2 Bilateral** — the final step. For each pivot cell, place each candidate, propagate L1+L2 to fixpoint, then run FPCE on the resulting board. Find eliminations common to ALL branches.

Each step is a natural generalization of the previous one.

---

## Results on 686 Expert Puzzles

### Before Depth-2 Bilateral
```
Pure Logic:        438 / 686 (63.8%)
Zone Deduction:    248 / 686
Stall points:      315
```

### After Depth-2 Bilateral
```
Stalls broken:     314 / 315 (99.7%)
Hard-core:         1 stall (Andrew #441)
Expected:          685 / 686 pure logic (99.85%)
```

### The Full Technique Stack
| Technique | Share | Level |
|-----------|-------|-------|
| FPCE (Deep) | 21.5% | L1+L2 propagation |
| FPC Placement | 15.3% | Gold-filtered chains |
| Depth-2 Bilateral | New | Branch + FPCE |
| All others | ~63% | Standard Sudoku |

---

## Comparison with Andrew Stuart's Forcing Nets

Andrew Stuart (sudokuwiki.org) independently developed **Forcing Nets** — a manual, interactive version of the same core principle:

| | Forcing Nets (Andrew) | Depth-2 Bilateral (WSRF) |
|---|---|---|
| **Approach** | Manual click + slider depth | Fully automated |
| **Core idea** | Follow ON/OFF consequences | Branch all candidates |
| **Contradiction** | Yellow cell = impossible | fpcPropagate returns true |
| **Convergence** | "All branches agree on a cell" | Common eliminations across branches |
| **Integration** | Manual tool, not in solver | Integrated in solver pipeline |
| **Depth control** | Slider (1-N) | Fixed: L1+L2 propagation |

Both arrived at the same insight from different directions: **test what happens when you commit to a choice, and find the unavoidable consequences.**

---

## Technical Details

### Pivot Selection
- Cells with 2-4 candidates, sorted by size (smallest first)
- Smaller = fewer branches = faster + more likely to yield common eliminations

### Propagation Engine
Uses `fpcPropagate` — a fast bitmask-based propagator:
- Board: `Uint8Array(81)`
- Candidates: `Uint16Array(81)` with 9-bit masks
- L1: Naked singles (one-bit check) + Hidden singles (unit scan)
- L2: Naked pairs, pointing pairs, claiming, naked triples
- All done with bitwise operations for speed

### Bilateral Test
For each pivot cell with N candidates:
1. Run N propagations (L1+L2 to fixpoint)
2. On each resulting board, run FPCE on all cells
3. Intersect the elimination sets
4. Any candidate in ALL sets → safe to eliminate

---

*One principle — "if it breaks under every assumption, it's impossible" — applied at two levels of depth.*
