# Exocet Implementation Spec — Matching Andrew's Output

## Verified Pattern (3 puzzles, identical structure)

### Geometry (always the same cells)
- **Base 1**: H8,H9 (row 7, cols 7,8 — box 8)
- **Base 2**: J5,J6 (row 8, cols 4,5 — box 7) — DIFFERENT row, different box
- **Target 1**: G4 (row 6, col 3 — box 6)
- **Target 2**: J1 (row 8, col 0 — box 6)
- **Base candidates**: 4 digits (union of both base pairs, always match)

### Elimination Rules

**Rule 1** (1 elimination): Remove non-base candidate from target G4
- Always removes exactly 1 digit (the non-base candidate in G4)

**Rule 8** (1 elimination): Remove base digit from target J1
- The specific base digit that has no cover line outside the band

**DX-R1** (13 eliminations): Base digits from cells seeing all 4 bases or both targets
- **J7** (sees all 4 bases): remove ALL 4 base digits
- **G2** (sees both targets): remove 3 base digits (not the R8-removed one)
- **G3** (sees both targets): remove 3 base digits (same 3)
- **H4** (sees all 4 bases): remove 3 base digits (same 3)

**DX-R2** (18 eliminations): Base digits from non-S cells in cover houses
3 of the 4 base digits get R2 eliminations (the 4th is already placed or handled):

| Digit | Cover rows | Cells |
|:------|:-----------|:------|
| A | C (row 3) + F (row 6) | C2,C3,C8,C9 + F2,F9 |
| B | E (row 5) + F (row 6) | E3,E8,E9 + F2,F6,F9 |
| C | C (row 3) + E (row 5) | C2,C3,C6,C9 + E3,E5 |

Total: **33 eliminations** (1 + 1 + 13 + 18)

### Key Target Selection Criteria
G4 + J1 is the ONLY valid target pair because:
1. Different boxes (G4=box6, J1=box6... wait, same box? Need to verify)
2. Don't see each other (different row, different col)
3. Pass companion check
4. Pass cover-line validation (≤2 perpendicular lines per base digit)
