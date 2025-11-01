# QoQ Delta KPI (Anonymized DAX Pattern)

![Made with DAX](https://img.shields.io/badge/Made%20with-DAX-1f425f.svg)
![Power BI](https://img.shields.io/badge/Power%20BI-Template-blue)
![License: MIT](https://img.shields.io/badge/License-MIT-green)

> **Purpose**  
> This mini‑project provides an **anonymized** DAX pattern to compute a quarter‑over‑quarter style KPI, including:  
> 1) the **ratio** between two comparable periods (e.g., *current quarter vs comparison quarter*),  
> 2) a **sign token** (`+`, `-`, `N/A`) for quick UI cues, and  
> 3) a **label** that concatenates the sign with a formatted percentage (with optional decimal comma).  
>
> All sensitive or domain‑specific names have been replaced with neutral placeholders to keep this ready for public GitHub use.

---

## What you get

- `measures.dax` – three reusable measures:
  - `KPI_A2_3_Ratio`: numeric ratio between two period counts
  - `KPI_A2_3_Sign`: quick sign indicator (`+`, `-`, `N/A`)
  - `KPI_A2_3_Label`: user‑friendly label combining sign and percent text

These are anonymized equivalents of the original measures and keep the original logic intact while improving safety and clarity.

---

## How to adapt to your model

Define **two base measures** in your model that represent the two periods you want to compare. For example:

- `[Count_Current_Period]` → e.g., *records in the current quarter*
- `[Count_Comparison_Period]` → e.g., *records in the comparison quarter* (previous quarter, or same quarter last year — your choice)

> ⚠️ **Required**: Replace those two placeholders with your actual base measures.  
> The rest of the measures will work as‑is.

---

## Measure overview

- **`KPI_A2_3_Ratio`**  
  - If the comparison period is **non‑zero**, returns `(Current − Comparison) / ABS(Comparison)` (signed ratio).
  - If the comparison period is **zero** and both periods are zero, returns `0` (neutral).
  - If the comparison period is **zero** but the current period is non‑zero, returns the **raw difference** (so you retain the magnitude information).

- **`KPI_A2_3_Sign`**  
  - Returns `"+"` if the ratio is `>= 0` and the comparison period is non‑zero.
  - Returns `"-"` if the ratio is `< 0`.
  - Returns `"N/A"` if the ratio is `BLANK()` (e.g., when both inputs are missing).

- **`KPI_A2_3_Label`**  
  - Concatenates the sign and the formatted percentage (`0.0%`).  
  - For negative values the minus sign in the formatted percent is sufficient, so it shows just the percent (no leading `+`).  
  - Includes a `SUBSTITUTE("." , ",")` so you can display a **decimal comma** for EU locales. Remove that line if you prefer the decimal dot.

---

## Usage tips

- Use `KPI_A2_3_Label` in a **card** or **table** for quick interpretation (`+1.2%`, `-0.7%`, `N/A`).
- Use `KPI_A2_3_Sign` to drive **conditional formatting** colors (e.g., green for `"+"`, red for `"-"`, gray for `"N/A"`).
- Keep the measure names as provided or rename them consistently. Measure names are intentionally **domain‑neutral**.

---

## Anonymization policy

- All domain terms (e.g., original business labels) have been replaced with neutral names like **Records** / **Period** / **Comparison**.
- No real datasets or client identifiers are included.  
- When you integrate this into your repository, ensure your own base measures do not reveal sensitive information in their names.

---

## License

This project is released under the **MIT License**. You’re free to use, copy, modify, and distribute with attribution.
