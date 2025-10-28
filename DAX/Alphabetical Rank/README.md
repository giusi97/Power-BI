# DAX — Alphabetical Rank (10 letters, reverse mapping A→26 … Z→1)

This repository contains an **anonymized** DAX pattern to:
1) create an alphabetical tie-breaker from the first **10** letters of a text label using **reverse mapping** (A=26 … Z=1), and  
2) compute a **rank** where a numeric metric has priority and the alphabetical value is only used to break ties.

> **Anonymization**
> - Table: `Items`
> - Column: `Items[Label]` (text to order)
> - Measure: `[Metric]` (your numeric score, e.g., incidence/weight)

## Rationale

- Reverse alphabetical mapping (A=26 → “comes first” in DESC rank) lets you flip classic A=1…Z=26 with minimal code.
- The 10-letter number is built as a **positional code** (base 10 for readability):  
  `L1*10^9 + L2*10^8 + … + L9*10 + L10`  
  Letters map to **26..1**; non-letters → **0**.
- Final sorting key:  
  ```
  RankKey = [Metric] + [Alpha10_Desc_Number] / 10^12
  ```
  `[Metric]` dominates; the alpha fragment is tiny and only resolves ties.

> Why `/ 10^12`?  
> Because the 10-digit alpha number is at most ~`26,25,24,...` per digit (≤ 2.6e10); dividing by `10^12` guarantees it never overtakes the integer part of `[Metric]`.

## Measures

- `Alpha10_Desc_Number` — the 10-letters reverse-alphabetical number (A=26 … Z=1, non letters → 0).
- `Rank_By_Metric_Then_Alpha10` — stable rank (DESC by metric), using `RANKX` + `ALL`.

## Usage (Power BI)

1. Ensure you have:
   - **Table** `Items`
   - **Column** `Items[Label]` (Text)
   - **Measure** `[Metric]` (Numeric)
2. Paste `alphabetical_rank_10letters.dax` into Power BI.
3. In a Table visual:
   - Rows: `Items[Label]`
   - Values: `Rank_By_Metric_Then_Alpha10` (and optionally `[Metric]`, `Alpha10_Desc_Number`).

## Notes

- Non-A/Z chars are treated as **0**.
- Shorter strings are handled safely (missing chars → 0).
- `ALL('Items'[Label])` keeps the **rank stable** even when some rows are filtered out in the visual.
- To switch to **A=1 … Z=26 (ascending)**, invert the mapping in `CharToNum_DESC_Safe`.
