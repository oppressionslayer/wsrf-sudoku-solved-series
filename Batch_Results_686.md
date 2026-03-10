# Batch Results: 686/686 Pure Logic — Zero Zone Deduction

*The full suite, every technique enabled, nothing held back.*

---

## Configuration

```
Top-9 Batch: Andrew #1–#686 (686 puzzles, 486.0s)
Dynamic Ranks: OFF | Zone Deductions: ON | Extended Logic: ON | FPC: ON | SIRO Boost: ON
```

---

## Results

```
Fully Solved:                    686 / 686 (100.0%)
Pure Logic (no zone deduction):  686 / 686
  └ Used L6+ techniques:        189 / 686
Used Zone Deduction:             0 / 686
```

**Every technique was available. Zone deduction was ON. It was never needed.**

---

## Technique Usage (55,862 total steps)

### The Core — 96.9% of all steps

| # | Technique | Firings | Share |
|---|-----------|---------|-------|
| 1 | hiddenSingle | 10,186 | 18.2% |
| 2 | nakedSingle | 9,919 | 17.8% |
| 3 | fpcElimination | 9,851 | 17.6% |
| 4 | finnedPointingChain | 8,365 | 15.0% |
| 5 | lastRemaining | 7,720 | 13.8% |
| 6 | fullHouse | 3,143 | 5.6% |
| 7 | pointingPair | 1,708 | 3.1% |
| 8 | depth2Bilateral | 1,336 | 2.4% |
| 9 | forcingChain | 1,274 | 2.3% |
| 10 | nakedPair | 778 | 1.4% |

### The Support — 2.9% of all steps

| # | Technique | Firings | Share |
|---|-----------|---------|-------|
| 11 | claiming | 490 | 0.9% |
| 12 | forcingNet | 437 | 0.8% |
| 13 | juniorExocet | 222 | 0.4% |
| 14 | bowmanBingo | 137 | 0.2% |
| 15 | nakedTriple | 52 | 0.1% |
| 16 | fullPipelineForcing | 50 | 0.1% |

### The Bench — Fully implemented, barely needed

| # | Technique | Firings | Share |
|---|-----------|---------|-------|
| 17 | finnedXWing | 37 | 0.1% |
| 18 | nakedQuad | 30 | 0.1% |
| 19 | template | 22 | 0.0% |
| 20 | finnedSwordfish | 20 | 0.0% |
| 21 | xyzWing | 14 | 0.0% |
| 22 | simpleColoring | 14 | 0.0% |
| 23 | alsXZ | 13 | 0.0% |
| 24 | xWing | 11 | 0.0% |
| 25 | swordfish | 8 | 0.0% |
| 26 | wWing | 6 | 0.0% |
| 27 | xyWing | 6 | 0.0% |
| 28 | aic | 5 | 0.0% |
| 29 | hiddenPair | 4 | 0.0% |
| 30 | alsXYWing | 2 | 0.0% |
| 31 | sueDeCoq | 1 | 0.0% |
| 32 | uniqueRect | 1 | 0.0% |

---

## The Story in Numbers

```
FPC family (FPC + FPCE + D2B + FPF):  19,602 firings  (35.1% of all steps)
Classic advanced techniques:             156 firings   (0.3% of all steps)
Zone Deduction:                            0 firings   (0.0%)
```

32 techniques were available. The FPC family handled over a third of the work. The classic advanced techniques — X-Wing, Swordfish, Coloring, Wings, ALS — combined for just 156 firings across 686 puzzles. They're not disabled. They're not broken. They're just not needed.

---

## What This Means

Every technique Andrew Stuart has documented on sudokuwiki.org is implemented and enabled in this run. The full arsenal. Nothing held back.

And yet:
- **FPC Elimination alone** (9,851 firings) does more work than all classic advanced techniques combined (156 firings) — by a factor of **63x**
- **Full Pipeline Forcing** needed just **50 firings** to eliminate zone deduction entirely
- **Zone Deduction was ON** — it simply never triggered

The techniques aren't gone. They're just watching from the bench while the FPC family runs the show.

---

*686/686 pure logic. All techniques enabled. Zero zone deduction. Sudoku: Solved.*
