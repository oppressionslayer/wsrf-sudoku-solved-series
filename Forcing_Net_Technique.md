# Forcing Net — Cast a Wider Net, Catch Harder Puzzles

*Same principle as Forcing Chain, but with more branches and deeper reach.*

---

## The Idea

A Forcing Net is a Forcing Chain with the limiter removed. Instead of restricting to cells with 2-3 candidates, Forcing Nets tackle cells with **3-4 candidates** and follow consequences through the entire constraint network — not just a single linear chain, but multiple paths that can split, converge, and interact.

The logic is identical:

1. Pick a cell with 3-4 candidates
2. For each candidate, place it and follow ALL forced consequences
3. If a branch contradicts — eliminate that candidate
4. If all branches force the same digit into the same cell — place it
5. If all but one branch contradict — the survivor is the answer

The difference is power. More branches means more coverage. More coverage means harder puzzles fall.

---

## Forcing Chain vs. Forcing Net

| | Forcing Chain | Forcing Net |
|---|---|---|
| **Cell candidates** | 2-3 (bivalue/trivalue) | 3-4 (trivalue/quadvalue) |
| **Branching** | 2-3 branches | 3-4 branches |
| **Chain shape** | Mostly linear | Network — paths split and reconverge |
| **Contradiction needed** | 1 branch breaks → eliminate | 1+ branches break → eliminate |
| **Convergence** | 2 branches agree → place | 3-4 branches agree → place |
| **Power** | Handles most L4 stalls | Handles what chains can't |

---

## The Key Difference: Networks, Not Lines

### Forcing Chain (Linear)

A Forcing Chain follows one path of consequences:

```
Place A → forces B at R2C5 → forces C at R2C9 → forces D at R7C9 → contradiction
```

One cell leads to the next, like dominoes in a line.

### Forcing Net (Branching)

A Forcing Net follows ALL consequences simultaneously. A single placement can force moves in multiple directions, and those paths can converge:

```
Place A → forces B at R2C5 ─→ forces D at R7C9 ──┐
                                                    ├─→ both force 3 at R8C4
       → forces C at R4C1 ─→ forces E at R8C1 ──┘
```

Multiple chain fragments converge on the same conclusion. This is why Forcing Nets find results that linear chains miss — the proof may require information from two separate parts of the board combining.

---

## Walkthrough: Step-by-Step Example

### The Setup

The board has stalled after L1, L2, FPC Placement, FPC Elimination, and even Forcing Chains. Everything with 2-3 candidates has been exhausted. Time to cast a wider net.

### Step 1: Pick a Cell

**R3C7** has candidates **{1, 4, 6}** — a trivalue cell. Three branches to explore.

---

### Step 2: Test Branch 1 — Place 1 at R3C7

```
Start: Place 1 at R3C7

Network:
  → R3C7 = 1: peers lose candidate 1

  Path A:
    → R3C2 had {1, 5}. Remove 1 → R3C2 = {5} → NAKED SINGLE → Place 5
      → R8C2 had {5, 9}. Remove 5 → R8C2 = {9} → Place 9
        → R8C6 had {4, 9}. Remove 9 → R8C6 = {4} → Place 4

  Path B (simultaneously):
    → R1C7 had {1, 6}. Remove 1 → R1C7 = {6} → NAKED SINGLE → Place 6
      → R9C7 had {3, 6}. Remove 6 → R9C7 = {3} → Place 3

  Paths converge:
    → Box 9 now needs digit 4. Only R7C8 can hold it.
      → HIDDEN SINGLE → Place 4 at R7C8

  → Continue propagating... board remains consistent.
```

**Branch 1 result:** No contradiction. Board holds.

---

### Step 3: Test Branch 2 — Place 4 at R3C7

```
Start: Place 4 at R3C7

Network:
  → R3C7 = 4: peers lose candidate 4

  Path A:
    → R3C9 had {4, 8}. Remove 4 → R3C9 = {8} → Place 8
      → R6C9 had {2, 8}. Remove 8 → R6C9 = {2} → Place 2
        → R6C1 had {2, 7}. Remove 2 → R6C1 = {7} → Place 7

  Path B:
    → R5C7 had {4, 9}. Remove 4 → R5C7 = {9} → Place 9
      → R5C4 had {6, 9}. Remove 9 → R5C4 = {6} → Place 6
        → R4C4 had {3, 6}. Remove 6 → R4C4 = {3} → Place 3

  Paths continue independently, then:
    → R1C1 needs digit 3, but 3 is already in Row 1!

  ╔═══════════════════════════════════════╗
  ║  CONTRADICTION at R1C1!              ║
  ║  Digit 3 needed but already present  ║
  ║  in Row 1. The board is broken.      ║
  ╚═══════════════════════════════════════╝
```

**Branch 2 result:** Contradiction after 6 forced moves across two paths.

---

### Step 4: Test Branch 3 — Place 6 at R3C7

```
Start: Place 6 at R3C7

Network:
  → R3C7 = 6: peers lose candidate 6

  Path A:
    → R1C7 had {1, 6}. Remove 6 → R1C7 = {1} → Place 1
      → R1C3 had {1, 9}. Remove 1 → R1C3 = {9} → Place 9

  Path B:
    → R3C2 had {1, 5}. Still {1, 5}...
      → In Box 1, digit 1 can only go at R3C2.
      → HIDDEN SINGLE → Place 1 at R3C2

  But wait — Path A just placed 1 at R1C7 (Row 1),
  and Path B placed 1 at R3C2 (Box 1). These don't conflict.

  → Continue propagating...
  → Col 5 needs digit 7. No cell in Col 5 can hold 7!

  ╔═══════════════════════════════════════╗
  ║  CONTRADICTION in Column 5!          ║
  ║  Digit 7 has no remaining cell.      ║
  ║  The board is broken.                ║
  ╚═══════════════════════════════════════╝
```

**Branch 3 result:** Contradiction.

---

### Step 5: Draw the Conclusion

```
Branch 1 (place 1): No contradiction ✓
Branch 2 (place 4): CONTRADICTION    ✗
Branch 3 (place 6): CONTRADICTION    ✗
```

Both 4 and 6 are impossible at R3C7. Only 1 remains.

**Place 1 at R3C7.**

```
  R3C7 = 4 → ... → 3 conflicts in Row 1 → IMPOSSIBLE
  R3C7 = 6 → ... → 7 has no home in Col 5 → IMPOSSIBLE

  Therefore R3C7 = 1. Done.
```

---

## Convergence: The Other Way to Win

Contradiction isn't the only path to a result. Forcing Nets can also find **convergence** — all branches agree on a placement:

```
R3C7 has {1, 4, 6}

  Branch 1 (place 1): ... cascade ... → forces 5 at R8C2
  Branch 2 (place 4): ... cascade ... → forces 5 at R8C2
  Branch 3 (place 6): ... cascade ... → forces 5 at R8C2

  All three branches force 5 at R8C2.
  One of {1, 4, 6} MUST be at R3C7 — that's a fact.
  In every possible world, 5 ends up at R8C2.

  → Place 5 at R8C2.
```

This is especially powerful with Forcing Nets because more branches make convergence more meaningful — three or four independent lines of reasoning all pointing to the same conclusion.

---

## How to Spot Forcing Net Opportunities by Eye

### What to Look For

1. **Find cells with 3-4 candidates** after Forcing Chains have been exhausted on bivalue cells
2. **Prefer cells at intersections** — cells whose peers span multiple boxes create wider cascades
3. **Follow all paths simultaneously** — mentally track what each placement forces across rows, columns, and boxes
4. **Watch for convergence** — if two branches already agree on a placement, check if the third does too

### The Mental Challenge

Forcing Nets are harder to track mentally than chains because you're following 3-4 branches instead of 2, and each branch can split into multiple paths. This is where a solver tool shines — it can explore all paths instantly and find contradictions or convergence that would take minutes to trace by hand.

---

## By the Numbers (686 Expert Puzzles)

| Metric | Value |
|--------|-------|
| Forcing Net firings | 431 (0.8% of all solving steps) |
| Pipeline position | Works alongside Forcing Chains in L4-L5 |
| Cell types | Trivalue and quadvalue (3-4 candidates) |
| Techniques used in cascade | Naked singles + hidden singles |

### The Partnership with Forcing Chains

Forcing Chains (bivalue/trivalue) and Forcing Nets (trivalue/quadvalue) overlap on trivalue cells and work as a team in the L4-L5 pipeline. Together they contribute roughly 867 firings — cleaning up the stall points that even FPC Elimination couldn't resolve.

---

## Cascading for Speed

The cascade — the chain of forced naked singles and hidden singles that follows each hypothetical placement — is what makes Forcing Nets practical despite testing 3-4 branches per cell. Because the cascade only uses the two simplest techniques, each branch resolves in microseconds:

- **Naked single:** One bitmask comparison per cell.
- **Hidden single:** One pass through 9 cells per unit.

Even with 3-4 branches and cascades of 10-40 forced placements each, the total work per cell is trivial at bitmask speed. The solver can exhaustively test every trivalue and quadvalue cell on the board faster than a traditional solver can detect a single complex pattern like ALS-XY-Wing or Death Blossom.

**The recommendation:** any solver looking for both power and speed should consider cascade-based hypothesis testing. Rather than implementing dozens of specialized techniques, a single cascade engine covers the same ground — faster, more generally, and with far less code. The cascade naturally finds what those techniques were designed to find, without recognizing any specific pattern.

---

## Where It Fits in the Pipeline

```
L1 (naked singles, hidden singles)
  → L2 (naked pairs, pointing pairs, claiming)
    → FPC Placement (gold-filtered chains)
      → FPC Elimination (contradiction testing)
        → Forcing Chain (bivalue/trivalue branching)
        → ★ FORCING NET ★  ← you are here (alongside chains)
          → L5-L6 (extended logic)
            → Zone Deduction (last resort)
```

---

## Credit Where It's Due

Andrew Stuart of [sudokuwiki.org](https://www.sudokuwiki.org) independently developed an interactive Forcing Net implementation with a depth slider that lets users manually explore branches at different depths. Same core insight — test what happens when you commit to each possibility — arrived at from different directions.

| | Andrew Stuart's Version | WSRF Version |
|---|---|---|
| **Approach** | Interactive, manual click-through | Fully automated in solver pipeline |
| **Depth control** | User-adjustable slider | Fixed: L1 + L2 propagation |
| **Visualization** | ON/OFF chain coloring | Cascade highlighting (cyan/red) |
| **Integration** | Standalone tool | Integrated with FPC, FPCE, and full pipeline |

---

## The Logic in One Sentence

> Pick a cell with several candidates, follow every possibility through the constraint network, and eliminate the ones that break the board — or place what all branches agree on.

That's Forcing Net. Forcing Chain's bigger sibling. Same logic, wider reach, harder puzzles solved.

---

*A Simple Wili technique can replace the need for many of these advanced techniques.*
