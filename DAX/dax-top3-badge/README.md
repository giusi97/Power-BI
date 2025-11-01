# Proximity Ranking & Top-3 Badge — DAX Template

![Made with DAX](https://img.shields.io/badge/Made%20with-DAX-1f425f.svg)
![Power BI](https://img.shields.io/badge/Power%20BI-Template-blue)
![License: MIT](https://img.shields.io/badge/License-MIT-green)

> **Purpose**  
> This project provides a reusable **DAX pattern** to:
> 1. Calculate the **rank** of a selected element within a group (e.g., ranking a product or category among its peers).  
> 2. Display a simple **Top‑3 badge** (`+` if ranked within the top 3, `–` otherwise), conditional on a filter being applied.

All table and column names in this project are fully **generic** and safe for public sharing.

---

## Components

### 🧮 Measures included
- `Prox_Rank_SelectedItem` — Calculates the rank of the currently selected item within its group.  
- `Badge_Top3_Proximity` — Returns `"+"` if the item is in the top 3, `"–"` otherwise, when a single filter context exists.

### 📊 Expected fields and measures
- Table: `'proximity_table'`
  - `[source_id]` — group or origin identifier  
  - `[item_label]` — item or destination label
- Table: `'facts'`
  - `[territory_code]` — used for the conditional filter in the badge measure
- Measure: `[Count_Proximity_Records]` — numeric value to rank by (e.g., count, total, score)

> Replace these placeholders with your actual table and column names.  
> The logic remains completely domain‑independent.

---

## Example use cases
- Ranking related products, professions, or skills associated with a common source.  
- Highlighting whether a selected item is among the top‑performing subset in a given region or category.  
- Adding a visual badge (`+` / `–`) for intuitive dashboard interpretation.

---

## How it works
1. **Ranking logic** uses `RANKX` in descending order on the chosen metric (`[Count_Proximity_Records]`), filtered by the same `source_id` group.
2. **Badge logic** checks if a single territory filter is active and returns a visual indicator based on the rank.

---

## License
Released under the **MIT License** — free to use, modify, and distribute with attribution.
