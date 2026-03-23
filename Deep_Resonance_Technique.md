# Deep Resonance

*Invented by Lars Rocha (oppressionslayer) — March 2026*

*The technique that makes the "forum hardest" puzzles solvable by pure logic.*

**UPDATE (March 22, 2026):** The deep cascade (Phases 4+) is NOT required. Base mode — propagate + chain follow + one ForcingChain pass — achieves 100% on the first 1,000 forum hardest puzzles (10,000+ validation in progress). This means DeepResonance is essentially the same as applying Forcing Nets as a systematic contradiction test. No recursion, no cascade, no brute force. Andrew Stuart's existing Forcing Nets infrastructure can implement this directly.

---

## The Idea

Standard contradiction testing asks: "If I place digit D at cell X, does the board break?"

Deep Resonance asks a much deeper question: **"If I place digit D at cell X, propagate ALL consequences — including chain reactions, forcing chains, and cascaded forcing chains — does anything eventually break?"**

It searches for contradictions at depths that no other single technique can reach, by chaining together multiple proof engines in sequence.

---

## How It Works

Deep Resonance runs up to 5 phases on each candidate of each cell (2-4 candidates, sorted smallest first). The moment a contradiction is found, that candidate is eliminated.

### Phase 1: Structural Propagation

Place candidate D at cell X. Propagate L1+L2 to fixpoint (naked singles, hidden singles, naked pairs, pointing pairs, claiming, naked triples).

If the board breaks (a cell has zero candidates, or a unit has no place for a digit), **contradiction found** — eliminate D from X.

**Example:** Place 3 at R4C7. Propagation forces R4C9=8, which forces R6C9=5, but R6C9 already can't be 5 — contradiction. Eliminate 3 from R4C7.

### Phase 2: Chain Following

If Phase 1 didn't break, look at the propagated state. Follow strong/weak link chains through the board. If any chain leads to a structural contradiction, eliminate the candidate.

This catches contradictions that propagation alone misses — cases where the chain of consequences is too indirect for L1+L2 to see.

### Phase 3: Forcing Chain on Trial State

If Phase 2 didn't break, build a full trial board from the propagated state and run the ForcingChain detector on it. ForcingChain finds placements by checking bivalue cells — if placing any ForcingChain result causes the trial board to break, **contradiction found**.

This is where Deep Resonance gets its power: it's running a proof engine INSIDE a proof engine.

**Example:** After placing 7 at R2C3 and propagating, the trial board has a bivalue cell R5C8={4,6}. ForcingChain determines R5C8=4. But propagating R5C8=4 on the trial board causes R8C8 to lose all candidates — contradiction. Therefore 7 cannot go at R2C3.

### Phase 4: Cascade Propagation (Deep Mode)

If Phase 3 didn't break, go deeper. Take each ForcingChain placement from the trial board, propagate it, and run the whole chain-follow + ForcingChain sequence again on the RESULTING state. Up to depth 3.

```
Original board
  └─ Place D at X, propagate (Phase 1)
       └─ ForcingChain finds placement P1 (Phase 3)
            └─ Propagate P1, chain-follow (Phase 4, depth 2)
                 └─ ForcingChain finds P2 (Phase 4, depth 3)
                      └─ Propagate P2 — CONTRADICTION
```

Three levels of forcing chain cascades. This is what cracks the puzzles rated 11.0+ by Sudoku Explainer.

### Phase 5: Cross-Candidate Convergence

After testing all candidates of a cell, check if the surviving (non-contradicted) branches **unanimously agree** on anything:

- If ALL surviving branches place the same digit at the same cell, that placement is proven
- If ALL surviving branches eliminate a candidate from a cell, that elimination is proven

Same logic as Forcing Net convergence, but applied across the Deep Resonance cascade depth.

---

## Why It Works

Deep Resonance is **proof by contradiction at extreme depth**:

1. Cell X must contain one of its candidates (Sudoku law)
2. We test each candidate with the full power of propagation + forcing chains + cascaded forcing chains
3. If a candidate causes the board to break at ANY depth, it's impossible in the real solution
4. Therefore it can be safely eliminated

No guessing. No backtracking. Pure deductive logic applied through multiple layers of consequence.

---

## The Architecture

```
For each cell (2-4 candidates, smallest first):
  For each candidate D:
    Phase 1: fast_propagate_full(board, D) → contradiction?
    Phase 2: follow_chain_contra(propagated) → contradiction?
    Phase 3: ForcingChain(trial) → place results → propagate → contradiction?
    Phase 4: Cascade depth 2-3:
             ForcingChain → propagate → chain_follow → ForcingChain → propagate
    If contradiction: eliminate D

  Phase 5: Cross-candidate unanimous check on surviving branches
```

### Key Implementation Details

- **Pivot selection**: Cells with 2-4 candidates, sorted by candidate count (bivalue first)
- **Propagation engine**: `fast_propagate_full` — JIT-compiled bitmask propagation (L1+L2 to fixpoint)
- **Trial boards**: `_build_trial` creates a fresh BitBoard from propagated arrays for detector use
- **Cascade depth**: Fixed at 3 (propagate → chain → propagate → chain → propagate)
- **Early exit**: Returns immediately on first elimination found (greedy)

---

## Results on Forum Hardest Puzzles

The `puzzles5_forum_hardest_1905_11+` set contains 48,765 puzzles rated 11.0+ by Sudoku Explainer — the hardest known puzzles.

### Larsdoku Results (first 50)
```
Solved: 50/50 (100%) — pure logic, zero backtracking
```

Deep Resonance fires on nearly every puzzle in this set. It is the single most important technique for solving SE 11.0+ puzzles.

### Technique Frequency (first 50 puzzles)

DeepResonance appears in **44/50 puzzles** (88%), often firing multiple times per puzzle:

| Technique | Puzzles using it |
|-----------|-----------------|
| DeepResonance | 44/50 (88%) |
| ALS_XZ | 32/50 (64%) |
| FPC | 27/50 (54%) |
| FPCE | 24/50 (48%) |
| ALS_XYWing | 15/50 (30%) |
| SimpleColoring | 14/50 (28%) |
| KrakenFish | 10/50 (20%) |
| D2B | 10/50 (20%) |
| XCycle | 10/50 (20%) |
| JuniorExocet | 10/50 (20%) |
| XWing | 8/50 (16%) |
| FPF | 5/50 (10%) |
| Swordfish | 3/50 (6%) |
| SueDeCoq | 1/50 (2%) |
| DeathBlossom | 1/50 (2%) |
| ForcingChain | 1/50 (2%) |


---

## The Discovery

The evolution of contradiction-based techniques in the WSRF engine:

1. **FPCE** — Place a candidate, propagate L1, check for contradiction. Simple but limited depth.

2. **Depth-2 Bilateral (D2B)** — For each pivot cell, branch ALL candidates, propagate L1+L2, run FPCE on each branch. Find common eliminations.

3. **Deep Resonance** — The final form. Branch each candidate, propagate L1+L2, then run ForcingChain on the resulting state, then cascade: propagate ForcingChain results and run ForcingChain AGAIN on the cascaded state, up to depth 3.

Each step roughly triples the contradiction detection depth:

```
FPCE:             propagation depth ~1
D2B:              propagation depth ~2 (bilateral + FPCE)
Deep Resonance:   propagation depth ~5-6 (cascade × 3 levels)
```

---

## Comparison with Other Techniques

| | Deep Resonance | Forcing Nets | D2B | Bowman's Bingo |
|---|---|---|---|---|
| **Depth** | 3 cascade levels | Flat (depth 6 max) | 2 levels | Full backtrack |
| **Inner engine** | ForcingChain | ON/OFF tracing | FPCE | Full solver |
| **Convergence** | Phase 5 (cross) | Net convergence | Common elims | N/A |
| **Sound** | Yes (no backtrack) | Yes | Yes | Yes (but brute force) |
| **Speed** | Fast (early exit) | Slow (exhaustive) | Medium | Slowest |
| **SE 11.0+ rate** | ~100% | ~9% | ~70% | 100% (but cheating) |

Deep Resonance fills the gap between D2B and backtracking — it has the depth to crack extreme puzzles without the brute force of trial-and-error.

---

## Try It Yourself

Import these forum hardest puzzles and watch Deep Resonance fire:

```
98.7..6..75..4......3..8.7.8....9.5..3.2..1.....4....6.7...4.3....8..4......1...2
98.7..6..5...9..7...7..4...4...327....65...9.......3.11...8.5...2.........54...8.
98.7..6..7.....5....4.8..9.4...3..7..7...2.....31....53....61...4..9..3.........2
```

The first needs DeepResonance x2 + SimpleColoring x2 + XCycle + ALS_XZ x7 + XWing x2.

The second needs DeepResonance x2 + D2B x2 + XCycle + ALS_XZ x2 + ALS_XYWing x2 + XWing + SimpleColoring.

The third needs JuniorExocet + DeepResonance x7 + D2B + ALS_XZ x4 + KrakenFish + FPC + XCycle.

---

*Proof by contradiction, cascaded three levels deep — the engine that turns "unsolvable" into "solved."*
