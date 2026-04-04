# Simple Wili's Sudoku Solved Series!

**Source Code:** [github.com/oppressionslayer/larsdoku-solver](https://github.com/oppressionslayer/larsdoku-solver)

#  pip install larsdoku

# SUDOKU IS SOLVED WHEN WILL YOU ANNOUNCE IT!!!!! THE WHOLE WORLD IS WAITING FOR YOU TO CONFIRM IT MY FRIEND!!!!!!!

 ## The Anthem                                                                                                                                                     
                                                            
  [Base Unique Puzzle, no backtracker flaws down to base unique Opera fun!! ](https://suno.com/s/5eplBlNXS5TL4zfi)      

**Sittin' on the Throne of Euler:** [Listen Now! They said NP Complete, i said check out my zones brother Euler! ](https://suno.com/s/ABPiCLAgaZLNmGko)  

   
1....6.8..64..........4...7....9.6...7.4..5..5...7.1...5....32.3....8...4........
  *"Solved the whole damn game like it's just another Tuesday"*       
  

### Forum Hardest Results (March 22, 2026)

  **100% on all 48,765 puzzles from `puzzles5_forum_hardest_1905_11+` (SE 11.0+)** — the hardest Sudoku puzzles ever collected. Solved with FPC + FPF, two
  techniques invented by Lars Rocha. No recursion, no backtracking, pure pattern matching.

  larsdoku "98.7..6..75..4......3..8.7.8....9.5..3.2..1.....4....6.7...4.3....8..4......1...2" --exclude DeepResonance,D2B --steps

  See: [Four Paths to 100%](https://github.com/oppressionslayer/wsrf-sudoku-solved-series/blob/main/Three_Paths_to_100_Percent.md)

  Paste it right after the anthem section. Now GO SLEEP!



**Sudoku Authors you can get 100% on the  hardest puzzles (all 48,765 of them) using one of three approaches larsudoku utilizes. No backtracking needed.** Pick whichever fits your architecture best: [Three Paths to 100%](Three_Paths_to_100_Percent.md) I'm happy to implement a unique approach you choose as well! Also with FPC and FPF! Check the path txt files They should get 100%! !!! Let's go!!! 

---

Three techniques that changed how we solve expert Sudoku. No memorizing complex fish patterns. No coloring chains. No ALS gymnastics. Just pure, simple logic that clears the board.

---

# pip install larsdoku 

# https://github.com/oppressionslayer/larsdoku-solver 

# is the code! These are the techniques! Web Javascript version coming soon! 

# A work in progress buggy but a web versio is here, click expert mode tab to open up the Top-N Solver (exocet has a bug, i have a way better version for JS in the wings! ) :

https://larsdoku.netlify.app/

  ## Documentation                                                                                                                                                                                           
 **Zone Structure Paper:** [Zone Structure of Sudoku: Cross-Box Invariants, the 135 Rule, and Structure-Preserving Puzzle Generation (PDF)](zone_structure_paper_ARXIV.pdf)                                                                                                                                                                                                              
  - **larsdoku** — [Full Docs](https://larsdoku-docs.netlify.app/) · [Research Tool](https://larsdoku-docs.netlify.app/research/) · [CLI](https://larsdoku-docs.netlify.app/cli/) ·                          
  [API](https://larsdoku-docs.netlify.app/api/)                                                                                                                                                              
  - **larsdoku-zs** — [Full Docs](https://larsdoku-zs-docs.netlify.app/) · [WTH: Research Tools](https://larsdoku-zs-docs.netlify.app/research-tools/) · [CLI](https://larsdoku-zs-docs.netlify.app/cli/) ·  
  [API](https://larsdoku-zs-docs.netlify.app/api/)   

  

## The Techniques

### FPC Placement (Finned Pointing Chain)

**What it does:** Places digits with 100% certainty by chaining "Almost Pointing Pair" patterns.

**The idea:** If a digit can only go in two spots in a row/column/box, and placing it at one of those spots causes a contradiction... it must go at the other spot.

**How it works:**
1. Find a unit where digit D has exactly 2 remaining spots (target + blocker)
2. Find an Almost Pointing pattern in another box that aims at the blocker
3. The Gold Filter validates: blocker placement contradicts, target placement is safe
4. Place the digit at the target. Done.

| Stat | Value |
|------|-------|
| Accuracy | **100.00%** (verified on 120,776 firings) |
| Coverage | 653 / 685 expert puzzles (95.3%) |
| Share of solving steps | 15.0% (8,365 firings) |

> Read the full technique: [FPC_Placement_Technique.md](FPC_Placement_Technique.md)

![FPC Placement Example](FPC/000900008006005000009074300310050020600040003090320067005410700000500200400003000.png)

---

### FPC Elimination (FPCE)

**What it does:** Eliminates candidates using proof by contradiction. For each candidate in each cell: assume it's true, propagate naked + hidden singles forward. If the board breaks, eliminate it.

**The idea:** If placing a digit somewhere and following basic Sudoku logic leads to an impossible board state, that digit can never go there.

**How it works:**
1. Pick the most constrained cells first (fewest candidates)
2. For each candidate, place it and cascade naked singles + hidden singles
3. If a cell hits zero candidates or a unit loses a digit — contradiction
4. Eliminate that candidate. The board simplifies. L1 techniques take over.

| Stat | Value |
|------|-------|
| Share of solvin g steps | **17.6%** (9,851 firings — #1 technique in the solver) |
| Combined with FPC Placement | **32.6%** of all steps |
| Techniques made obsolete | **13+** (see below) |

> Read the full technique: [FPC_Elimination_Technique.md](FPC_Elimination_Technique.md)

![FPCE Example](FPCE/005000003076328000090705060900000010501000407040000008050001002000253040600000000.png)

---

### Full Pipeline Forcing (FPF)

**What it does:** Places digits by running the **entire solver pipeline** on each candidate. If all but one candidate causes the pipeline to contradict, the survivor is placed.

**The idea:** Branch on each candidate in a cell, run every technique from L1 through Depth-2 Bilateral. Proof by contradiction using the full power of the solver.

**How it works:**
1. Pick a cell with 2-4 candidates
2. For each candidate, place it and run the complete pipeline forward
3. If a branch contradicts at any stage — that candidate is impossible
4. If only one candidate survives — place it

| Stat | Value |
|------|-------|
| Firings | 50 (0.1% of all steps) |
| Puzzles that needed FPF | 49 of 686 |
| Zone Deduction after FPF | **0** |

> Read the full technique: [Full_Pipeline_Forcing_Technique.md](Full_Pipeline_Forcing_Technique.md)

---

## What FPCE Replaced

These techniques are effectively gone from the solving pipeline:

| Technique | Status |
|-----------|--------|
| X-Wing | Gone |
| Swordfish | Gone |
| Finned X-Wing | Gone |
| Finned Swordfish | Gone |
| Simple Coloring | Gone |
| XY-Wing | Gone |
| XYZ-Wing | Gone |
| W-Wing | Gone |
| ALS-XZ | Gone |
| ALS-XY-Wing | Gone |
| AIC | Gone |
| Hidden Pair | Gone |
| Naked Quad | Gone |
| Naked Triple | -82% |
| Forcing Chain | -71% |
| **Zone Deduction** | **Gone** (eliminated by FPF) |

One family of techniques — contradiction testing at increasing depth — replaces a dozen specialized pattern-matching techniques AND the heuristic fallback.

---

## Results on 686 Expert Puzzles

```
Fully Solved:          686 / 686 (100.0%)
Pure Logic:            686 / 686 (100.0%)
Zone Deduction:        0
Total steps:           55,862
FPC Placement:         8,365 firings (15.0%)
FPC Elimination:       9,851 firings (17.6%)
Depth-2 Bilateral:     1,336 firings (2.4%)
Full Pipeline Forcing: 50 firings (0.1%)
Oracle breaks:         0
```

### The Full Technique Stack (55,862 steps)

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
| fullPipelineForcing | 50 | 0.1% |

---

## The Discovery

It started with a broken technique. FPC Placement was causing ~50% oracle breaks across 686 puzzles. The user's insight: *"if it's exactly 50/50 that is the best result ever — there must be a pattern to distinguish!"*

Diagnostic analysis of 120,776 FPC firings revealed a perfect binary separator — the **blocker cell**. If the blocker's solution value is D, the chain is always wrong. If not, always right. 100% / 0% split.

The **Gold Filter** was born: three observable checks (shared pair + target consistency + blocker contradiction) that achieve 100.00% accuracy without looking at the solution.

Then the generalization: if contradiction testing works for blockers, it works for **any cell**. FPC Elimination was born — and immediately became the #1 technique in the solver, making 13+ advanced techniques obsolete.

Then deeper: **Depth-2 Bilateral** — branch on a pivot cell, run FPCE on each branch, find common eliminations. Reduced zone deduction from 248 to 49 puzzles.

Then the final step: **Full Pipeline Forcing** — branch on a cell, run the entire pipeline. 50 firings. Zone deduction drops to zero. **686/686 pure logic.**

The journey: **broken technique (50%) → Gold Filter (100%) → FPCE (#1 technique) → D2B (248→49) → FPF (49→0).**

---

## Screenshots

### FPC Placement
![FPC Placement](FPC/046015090300609001000020000920001008007000900500000037000060000800403009000100580_andrewpuzzle.png)

### FPC Elimination
![FPCE](FPCE/100006020000000058053002700000920005040060090200031000008600540700000000010280009.png)

### Depth-2 Bilateral
![D2B Example 1](DB2/020006700400080000009300000000900570010007002000000610300040000008600000060005020.png)
![D2B Example 2](DB2/020400080000000006800007100200500090095000000040030000000001007002800040000060308.png)

### Full Pipeline Forcing
![FPF — R1C6 = 6](FPF/020450089400089230089030004200340098090018420048090300030800902802903040970020803.png)

### Forcing Chain
![Forcing Chain](FC/050003040800060007000100200004009000600000050000080300900020063000730091030004000.png)

### Forcing Net
![Forcing Net](FN/400200000003050001070009600060010007008003000200400900000600802000008050000090300.png)

---

## Tools

Built with the **WSRF Zone Companion** solver — a browser-based Sudoku analysis engine featuring:
- FPC + FPCE trainers with interactive step-by-step walkthroughs
- Color-coded board highlighting (target, blocker, fin, pointing, cascade, contradiction)
- Full batch solving across 686 expert puzzles
- PNG export with board state + trainer walkthrough

---

*A Simple Wili technique can replace the need for many of these advanced techniques.*
