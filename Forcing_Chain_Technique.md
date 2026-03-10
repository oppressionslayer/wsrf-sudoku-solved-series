# Forcing Chain — Contradiction-Driven Placement

*When the board stalls, pick a cell, follow the consequences, and let the logic do the rest.*

---

## The Idea

A Forcing Chain takes the simplest possible approach to breaking through a stall point:

1. Find a cell with only 2 or 3 candidates
2. Hypothetically place each candidate and follow the chain of forced moves
3. If a branch leads to an impossible board — that candidate is eliminated
4. If only one candidate survives — place it

No pattern recognition. No geometric shapes. Just: "What happens if I put this digit here?" Follow the dominoes. See what breaks.

---

## The Building Block: Following the Chain

### What "Follow the Chain" Means

When you place a digit, it removes that candidate from all 20 peer cells (same row, column, and box). Some of those peers may drop to a single candidate — a naked single. Others may become the only spot for a digit in their unit — a hidden single. Each of those forced placements triggers more removals, more naked singles, more hidden singles.

That cascade of forced moves IS the chain.

```
Place digit A at cell X
  → Cell Y in the same row drops to one candidate → forced placement
    → Cell Z in the same box drops to one candidate → forced placement
      → Cell W in the same column drops to one candidate → forced placement
        → ... keep going until the cascade stops or something breaks
```

### What Counts as a Contradiction

Two things can go wrong during a cascade:

| Contradiction Type | What Happened |
|---|---|
| **Zero candidates** | A cell has no remaining candidates — nothing can go there |
| **Missing digit** | A row, column, or box has no cell that can hold some digit |

Either one proves the original assumption was wrong. That candidate is impossible.

---

## Walkthrough: Step-by-Step Example

### The Setup

After running L1 techniques (naked singles, hidden singles) and L2 techniques (naked pairs, pointing pairs, claiming) through FPC Placement and FPC Elimination, the board has stalled. Traditional solvers would reach for even more exotic techniques. Forcing Chain reaches for common sense.

### Step 1: Pick a Cell

**R5C3** has candidates **{2, 7}** — a bivalue cell. Only two options to test. If one contradicts, the other is the answer.

---

### Step 2: Test Branch 1 — Place 2 at R5C3

Assume 2 goes at R5C3. Now follow the chain:

```
Start: Place 2 at R5C3

Chain:
  → R5C3 = 2: peers in Row 5, Col 3, Box 4 lose candidate 2

  → R5C7 had {5, 8}. In Row 5, digit 5 can now only go at R5C7.
    → HIDDEN SINGLE → Place 5 at R5C7

  → R5C1 had {3, 8}. In Row 5, digit 8 can now only go at R5C1.
    → HIDDEN SINGLE → Place 8 at R5C1

  → R2C1 had {3, 9}. Col 1 now needs 3. Only R2C1 can hold it.
    → HIDDEN SINGLE → Place 3 at R2C1

  → R2C3 had {3, 9}. Remove 3 (peer of R2C1) → R2C3 = {9}...
    but 9 is already placed in Col 3!

  ╔═══════════════════════════════════════╗
  ║  CONTRADICTION at R2C3!              ║
  ║  Cell forced to 9, but 9 already     ║
  ║  exists in Column 3.                 ║
  ║  The board is broken.                ║
  ╚═══════════════════════════════════════╝
```

**Branch 1 result:** Placing 2 at R5C3 breaks the board after 4 forced moves.

---

### Step 3: Test Branch 2 — Place 7 at R5C3

Assume 7 goes at R5C3. Follow the chain:

```
Start: Place 7 at R5C3

Chain:
  → R5C3 = 7: peers lose candidate 7
  → Cascade continues...
  → No cell hits zero candidates
  → No unit loses a digit
  → Board remains consistent
```

**Branch 2 result:** No contradiction found. The board holds together.

---

### Step 4: Draw the Conclusion

```
Branch 1 (place 2): CONTRADICTION ✗
Branch 2 (place 7): No contradiction ✓
```

2 is impossible at R5C3. Only 7 remains.

**Place 7 at R5C3.**

The chain of logic that proves it:

```
  R5C3 = 2
    → forces 5 at R5C7
      → forces 8 at R5C1
        → forces 3 at R2C1
          → R2C3 has zero valid candidates
            → IMPOSSIBLE

  Therefore R5C3 = 7. Done.
```

---

## The Three Forcing Chain Outcomes

Not every Forcing Chain fires through contradiction. There are three ways it can produce a result:

### 1. Contradiction Elimination (Most Common)

One or more branches lead to a broken board. Eliminate those candidates. If only one remains, place it.

```
Cell has {A, B}
  Branch A: contradiction
  Branch B: no contradiction
  → Eliminate A → Place B
```

### 2. Convergence Placement

ALL branches force the same digit into the same cell. Regardless of which branch is true, that placement is guaranteed.

```
Cell X has {A, B}
  Branch A: ... cascade ... → forces 5 at R8C4
  Branch B: ... cascade ... → forces 5 at R8C4
  → Place 5 at R8C4 (true no matter what)
```

### 3. No Result

All branches survive without contradiction and don't converge on anything useful. Move on to the next cell.

---

## How to Spot Forcing Chain Opportunities by Eye

### What to Look For

1. **Find bivalue cells** — cells with exactly 2 candidates are ideal (only 2 branches to follow)
2. **Prefer constrained neighborhoods** — cells whose peers also have few candidates produce longer cascades and faster contradictions
3. **Follow naked singles first** — they're the easiest to track mentally. When a cell drops to one candidate, place it and keep going
4. **Watch for hidden singles** — after each placement, check if any row/col/box now has only one spot for a digit

### Mental Shortcut

Start with the most constrained bivalue cell. Test the candidate that interacts with more peers. If the cascade runs 3-4 placements deep without breaking, switch to the other candidate. Contradictions usually show up within 3-6 steps if they're going to show up at all.

---

## By the Numbers (686 Expert Puzzles)

| Metric | Value |
|--------|-------|
| Forcing Chain firings | 436 (0.8% of all solving steps) |
| Pipeline position | After FPC Elimination exhausts |
| Cell types | Bivalue and trivalue (2-3 candidates) |
| Techniques used in cascade | Naked singles + hidden singles |

### Why the Low Firing Count

Forcing Chains fire so rarely because **FPC Placement and FPC Elimination handle 36.1% of all steps** before the chain ever gets a turn. By the time the solver reaches for Forcing Chains, FPC has already cleared most of the hard work. The 436 firings represent the stubborn stall points that even FPCE couldn't crack — and Forcing Chains break through them cleanly.

---

## Cascading for Speed

The cascade — the chain of forced naked singles and hidden singles that follows each hypothetical placement — is the engine that makes this technique practical. Because the cascade only uses the two simplest Sudoku techniques, it runs extremely fast:

- **Naked single:** One bitmask comparison per cell.
- **Hidden single:** One pass through 9 cells per unit.

A typical cascade runs 3-20 forced placements. With bitmask operations, this takes microseconds. The solver can test every candidate in every bivalue cell on the board faster than a traditional solver can detect a single Swordfish or ALS pattern.

**The recommendation:** any solver implementation looking for speed gains should consider using cascading as a core strategy. Rather than cycling through dozens of specialized pattern-matching techniques (X-Wings, Coloring, W-Wings, ALS), a single cascade-based hypothesis test can cover the same ground — faster and more generally. The cascade naturally discovers the same eliminations those techniques find, without needing to recognize any specific pattern. It scales well and simplifies the solver pipeline significantly.

---

## Where It Fits in the Pipeline

```
L1 (naked singles, hidden singles)
  → L2 (naked pairs, pointing pairs, claiming)
    → FPC Placement (gold-filtered chains)
      → FPC Elimination (contradiction testing)
        → ★ FORCING CHAIN ★  ← you are here
          → Forcing Net (wider branching)
            → L5-L6 (extended logic)
              → Depth-2 Bilateral (branch + FPCE)
                → Full Pipeline Forcing (entire pipeline)
                  → Zone Deduction (no longer needed)
```

---

## The Logic in One Sentence

> Pick a cell with few candidates, follow each one to its logical conclusion, and eliminate the ones that break the board.

That's Forcing Chain. Simple hypothesis testing, applied to Sudoku. No memorization required.

---

---

## Try It Yourself

Import this puzzle into the WSRF Zone Companion and watch Forcing Chain fire:

```
050003040800060007000100200004009000600000050000080300900020063000730091030004000
```

### Screenshot

![Forcing Chain Example](FC/050003040800060007000100200004009000600000050000080300900020063000730091030004000.png)

---

*A Simple Wili technique can replace the need for many of these advanced techniques.*
