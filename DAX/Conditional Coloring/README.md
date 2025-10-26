# Power BI DAX — Conditional Coloring (Progressive, Didactic)

![DAX](https://img.shields.io/badge/Made%20with-DAX-1f6feb)
![Power BI](https://img.shields.io/badge/Power%20BI-Conditional%20Formatting-f2c744)
![License: MIT](https://img.shields.io/badge/License-MIT-green)

This repository provides **progressive** DAX templates for **conditional formatting** in Power BI — starting from a minimal rule and building up to advanced, production-style logic. All names are **model‑agnostic** (anonymous) so you can map them to any dataset.

---

## Table of Contents
- [What You Get](#what-you-get)
- [Core Logic & Assumptions](#core-logic--assumptions)
- [Quick Start](#quick-start)
- [Progressive Measures](#progressive-measures)
- [Apply in Power BI](#apply-in-power-bi)
- [Troubleshooting](#troubleshooting)
- [FAQ](#faq)
- [License](#license)

---

## What You Get
- **`conditional_coloring_progressive.dax`** — **four levels** of measures for both **font** and **background** colors:
  - **L0 (Base)**: simple threshold (Rank vs MaxRank)
  - **L1 (Cut-aware)**: detect Top‑N cuts
  - **L2 (Tie‑break)**: cut + secondary order (“alpha”)
  - **L3 (Extensible)**: palette helpers, debug flags, and override hooks
- All measures return **HEX color strings** (e.g., `#000000`, `#FFFFFF`).

---

## Core Logic & Assumptions

These templates assume a **ranked list** visual (Table/Matrix) where you may limit rows (Top‑N).

### Key Concepts

| Term | Meaning | DAX Hint |
|------|--------|----------|
| `Rank_Selected` | Rank of the current row/item in the visual context. | `RANKX(ALL(Table[Item]), [Score], , DESC)` |
| `MaxRank_Visual` | Highest rank currently displayed (threshold). | Derived via separate measure or visual-level logic. |
| `RowCount` | Rows actually visible after filters/cuts. | `COUNTROWS(VALUES(Table[Item]))` |
| `RowCount_NoCut` | Rows before any cut/cap (theoretical). | Same dimension without Top‑N/caps. |
| **Cut Active** | `RowCount < RowCount_NoCut` | Signals a Top‑N or similar limit is applied. |
| `AlphaRank` / `AlphaMaxRank` | Optional **tie‑break** (e.g., alphabetical). | Secondary sort to decide beyond‑visible items. |

### Design Intent
1. **L0** highlights when an item is **beyond** a visible threshold (`Rank > MaxRank`).  
2. **L1** becomes **cut‑aware**, differentiating full vs trimmed listings.  
3. **L2** adds **tie‑break** so equal ranks behave deterministically under cuts.  
4. **L3** adds **extensibility**: centralized palette, debug helpers, and override hooks.

---

## Quick Start
1. Copy the measures from `conditional_coloring_progressive.dax`.
2. Map placeholders (`Rank_Selected`, `MaxRank_Visual`, etc.) to your model.
3. In your Table/Matrix: **Format → Conditional formatting → Font/Background → Format by: Field value** and select the measure level you want (e.g., `TextColor_L1`).

> Tip: Start with **L0**, validate logic using the **debug helpers** in **L3**, then move up to **L2** when you need tie‑break behavior.

---

## Progressive Measures

- **L0 (Base)** — single rule: emphasize if `Rank_Selected > MaxRank_Visual`.
- **L1 (Cut‑aware)** — same as L0, but also exposes a **cut flag** for downstream logic.
- **L2 (Tie‑break)** — when `Rank_Selected = MaxRank_Visual` and **cut is active**, highlight only if the item is **beyond** the visible tie‑break range (`AlphaRank > AlphaMaxRank`).
- **L3 (Extensible)** — palette constants, debug helpers, and **override hooks** so teams can customize behavior without rewriting all measures.

---

## Apply in Power BI
1. Select your **Table/Matrix**.
2. Go to **Format → Cell elements → Conditional formatting**.
3. Choose **Font color** (or **Background color**) → **Format by: Field value** → pick the desired **L0/L1/L2/L3** measure.

---

## Troubleshooting
- **Always one color**? Check the **default** branch in `SWITCH`, types (numeric vs text), and rounding.
- **Unexpected branch**? Drop the **debug helpers** from **L3** into the visual to observe TRUE/FALSE flags.
- **Two rules fighting**? Ensure only one conditional rule per property (font/background).

---

## FAQ
**Q: Can I use only background or only text?**  
A: Yes. Use the corresponding `BackgroundColor_*` or `TextColor_*` measures.

**Q: How do I change colors?**  
A: Edit the constants in **L3** (palette).

**Q: No tie‑break available.**  
A: Use **L0** or **L1** only.

---

## License
MIT
