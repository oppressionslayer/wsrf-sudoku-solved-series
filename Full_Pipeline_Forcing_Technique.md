# Full Pipeline Forcing — The Final Technique

*The technique that eliminated zone deduction entirely. 686/686 pure logic. Zero heuristics.*

---

## The Idea

Full Pipeline Forcing (FPF) takes a cell with 2-4 candidates and asks: **"What if I place each candidate and run the ENTIRE solver pipeline forward?"**

Not just naked singles and hidden singles. Not just L1+L2. The **full pipeline** — every technique in the solver, from L1 through Depth-2 Bilateral. If all but one candidate causes the pipeline to stall or contradict, the survivor is the answer.

This is the logical endpoint of the contradiction-testing family:

| Technique | What it propagates |
|---|---|
| FPCE | L1 (naked singles + hidden singles) |
| Deep FPCE | L1 + L2 (pairs, pointing, claiming) |
| Depth-2 Bilateral | L1+L2 → then FPCE on the result |
| **Full Pipeline Forcing** | **Everything — L1 through D2B** |

---

## The Band 1 Discovery

Before building FPF, we studied where zone deduction was placing digits. The pattern was striking:

```
Row 1:  35 / 47 placements (74%)
Row 2:   8 / 47 placements (17%)
Row 3:   2 / 47 placements  (4%)
Row 4:   2 / 47 placements  (4%)
Rows 5-9: 0 / 47 placements  (0%)
```

**96% of zone deduction placements landed in Band 1 (rows 1-3).** 74% were in Row 1 alone.

This wasn't a coincidence — it was a signal. The remaining hard puzzles had their ambiguity concentrated in the top band. A technique that could resolve Band 1 candidates would eliminate almost all remaining zone deduction calls.

FPF does exactly that.

---

## How It Works

### Step 1: Pick a Cell

Choose a cell with 2-4 candidates. FPF tries the most constrained cells first.

**Example:** Cell R1C5 has candidates {3, 7, 9}.

### Step 2: Branch and Run the Full Pipeline

For each candidate, hypothetically place it and run the complete solver pipeline:

**Branch A — Place 3 at R1C5:**
```
Place 3 → run L1 (naked singles, hidden singles)
  → run L2 (naked pairs, pointing pairs, claiming)
    → run FPC Placement (gold-filtered chains)
      → run FPC Elimination (contradiction testing)
        → run Forcing Chains + Forcing Nets
          → run Depth-2 Bilateral
            → Pipeline STALLS — board is incomplete but not broken
```
Result: The pipeline couldn't finish. This branch is suspicious but not proven wrong.

**Branch B — Place 7 at R1C5:**
```
Place 7 → run L1...
  → L2...
    → FPC Placement...
      → FPCE finds contradiction at step 14!

  ╔═══════════════════════════════════════╗
  ║  CONTRADICTION!                      ║
  ║  Cell R6C2 has zero candidates.      ║
  ║  The board is broken.                ║
  ╚═══════════════════════════════════════╝
```
Result: 7 is impossible.

**Branch C — Place 9 at R1C5:**
```
Place 9 → run L1...
  → L2...
    → CONTRADICTION at L2 stage!

  ╔═══════════════════════════════════════╗
  ║  CONTRADICTION!                      ║
  ║  Row 3 has no cell for digit 4.      ║
  ║  The board is broken.                ║
  ╚═══════════════════════════════════════╝
```
Result: 9 is impossible.

### Step 3: Draw the Conclusion

```
Branch A (place 3): Pipeline stalls — only survivor ✓
Branch B (place 7): CONTRADICTION ✗
Branch C (place 9): CONTRADICTION ✗
```

Two branches contradict. One survives.

**Place 3 at R1C5.**

Note: the surviving branch doesn't need to solve the board completely — it just needs to NOT contradict. If all other branches break, the survivor must be correct by elimination.

---

## Why "Full Pipeline" Matters

Each technique in the contradiction-testing family propagates deeper:

```
FPCE:     Place → L1 cascade → check
Deep FPCE: Place → L1+L2 cascade → check
D2B:      Place → L1+L2 → FPCE → check
FPF:      Place → L1 → L2 → FPC → FPCE → FC → FN → D2B → check
                  └─────────── entire pipeline ───────────┘
```

The deeper the propagation, the more contradictions you find. FPF pushes this to the maximum — running every technique the solver has. Contradictions that hide from FPCE and D2B are exposed when the full pipeline grinds through them.

---

## The Evolution Chain

FPF is the natural endpoint of a progression:

```
FPCE (2026)
  │  "Place a candidate, propagate L1, check for contradiction"
  │  Made 13+ advanced techniques obsolete
  │
  ├── Deep FPCE
  │    "Same, but propagate L1+L2"
  │    Coverage: 21% → 70% of stall points
  │
  ├── Depth-2 Bilateral (D2B)
  │    "For each candidate in a pivot cell, propagate L1+L2, then run FPCE"
  │    Reduced zone deduction from 248 → 49 puzzles
  │
  └── Full Pipeline Forcing (FPF)
       "For each candidate, run the ENTIRE solver pipeline"
       Reduced zone deduction from 49 → 0 puzzles
       686/686 pure logic
```

Each step is a natural generalization: propagate deeper, find more contradictions.

---

## Results on 686 Expert Puzzles

### The Final Numbers

```
Full Pipeline Forcing firings:  50   (0.1% of all steps)
Puzzles that needed FPF:        49   (of 686)
Zone Deduction after FPF:       0    (was 248 before D2B, 49 before FPF)
Pure Logic:                     686 / 686 (100.0%)
```

### The Complete Technique Stack (55,862 total steps)

| Technique | Firings | Share |
|-----------|---------|-------|
| hiddenSingle | 10,186 | 18.2% |
| nakedSingle | 9,919 | 17.8% |
| fpcElimination | 9,851 | 17.6% |
| finnedPointingChain | 8,365 | 15.0% |
| lastRemaining | 7,720 | 13.8% |
| fullHouse | 3,143 | 5.6% |
| pointingPair | 1,708 | 3.1% |
| depth2Bilateral | 1,336 | 2.4% |
| forcingChain | 1,274 | 2.3% |
| nakedPair | 778 | 1.4% |
| claiming | 490 | 0.9% |
| forcingNet | 437 | 0.8% |
| juniorExocet | 222 | 0.4% |
| bowmanBingo | 137 | 0.2% |
| nakedTriple | 52 | 0.1% |
| **fullPipelineForcing** | **50** | **0.1%** |

50 firings. That's all it took to close the gap from 49 unsolved puzzles to zero.

---

## Comparison: FPF vs Zone Deduction

| | Full Pipeline Forcing | Zone Deduction |
|---|---|---|
| **Type** | Deterministic — proof by contradiction | Heuristic — pattern scoring |
| **Guarantee** | 100% correct if pipeline is sound | Best guess, may need backtracking |
| **Approach** | Branch on candidates, run full pipeline | Score zone patterns, pick highest |
| **When it fires** | Only when needed (50 times in 686 puzzles) | Was firing 248+ times before |
| **Result** | Pure logic placement | Heuristic placement |

FPF is strictly stronger: every placement it makes is logically proven. Zone deduction is a heuristic fallback — it usually works, but it's not a proof. With FPF in the pipeline, zone deduction is no longer needed.

---

## Where It Fits in the Pipeline

```
L1 (naked singles, hidden singles)
  → L2 (naked pairs, pointing pairs, claiming)
    → FPC Placement (gold-filtered chains)
      → FPC Elimination (contradiction testing)
        → Forcing Chain (bivalue/trivalue branching)
        → Forcing Net (wider branching)
          → L5-L6 (extended logic)
            → Depth-2 Bilateral (branch + FPCE)
              → ★ FULL PIPELINE FORCING ★  ← you are here
                → Zone Deduction (no longer needed)
```

FPF sits at the very end of the deterministic pipeline, just before what used to be zone deduction. It's the last resort before heuristics — and it turns out to be powerful enough that the heuristics are never needed.

---

## The Logic in One Sentence

> Place each candidate, run the entire solver pipeline, and eliminate the ones that break — the last one standing is the answer.

That's Full Pipeline Forcing. The final technique. 50 firings to complete the journey from 63.8% pure logic to 100%.

---

*A Simple Wili technique can replace the need for many of these advanced techniques.*
