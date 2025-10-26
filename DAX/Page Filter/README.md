# Ignoring Page Filters and Slicers in Power BI with DAX

![Difficulty: Easy](https://img.shields.io/badge/Difficulty-Easy-brightgreen)

## 🧠 Context

In Power BI, slicers and page filters allow users to control what data is displayed in visuals.  
However, sometimes you need a **measure** or **table** that ignores one or more of these filters — for instance, to show overall totals or a reference baseline.

This project demonstrates how to **ignore slicers or page filters** using DAX functions such as `ALL()` and `REMOVEFILTERS()`.

---

## 🎯 Goal

To create a DAX measure that **ignores page-level slicers or filters**, such as a slicer on `Region`, while keeping other filters active.

---

## 💻 DAX Implementation

### Example Scenario

You have a table called `Sales` with the following columns:

- `Region`
- `Product`
- `SalesAmount`

A slicer is applied on the report page to filter by `Region`.  
We want a measure that **always shows the total sales** for all regions, even when a region is selected in the slicer.

---

### ✅ Measure: Ignore Region Slicer

```DAX
-- Base measure: total sales amount
Total Sales =
SUM('Sales'[SalesAmount])

-- New measure: ignores slicer on Region
Total Sales Ignoring Region Slicer =
CALCULATE(
    [Total Sales],
    ALL('Sales'[Region])     -- Ignores any filter applied to Region
)
```

When you select a region in the slicer, `[Total Sales Ignoring Region Slicer]` will remain constant, showing the grand total.

---

## 🧩 Alternative: Using REMOVEFILTERS

```DAX
Total Sales Ignoring Region Slicer =
CALCULATE(
    [Total Sales],
    REMOVEFILTERS('Sales'[Region])   -- Removes filters from Region, but keeps others
)
```

**Difference between `ALL` and `REMOVEFILTERS`:**
| Function | Behavior |
|-----------|-----------|
| `ALL()` | Returns all rows of a table or column, completely ignoring filters. |
| `REMOVEFILTERS()` | Removes filters from the specified column(s) or table but preserves the overall filter context. |

Both achieve similar results in this case, but `REMOVEFILTERS()` is often preferred for clarity.

---

## 🧱 Using a Table Visual (Optional)

If you want to create a **table visual** that ignores slicers on `Region`, you can use a virtual table:

```DAX
SalesTableIgnoringRegion =
ADDCOLUMNS(
    SUMMARIZE(
        ALL('Sales'),
        'Sales'[Product],
        'Sales'[Region]
    ),
    "Total Sales", CALCULATE(SUM('Sales'[SalesAmount]))
)
```

This table lists all products and regions with their total sales, unaffected by slicers.

---

## 💬 Key Concepts

- **CALCULATE()** changes the filter context of a calculation.
- **ALL()** and **REMOVEFILTERS()** are used to remove filters from the context.
- **SUMMARIZE()** and **ADDCOLUMNS()** can build virtual tables for advanced visuals.

---

## 🧩 Difficulty

**3 / 10 (Easy)**  
Understanding how DAX manages filters is essential for mastering Power BI.  
This example helps you practice controlling filter context intentionally.

---

## 🧰 Technologies Used

- Power BI Desktop
- DAX

---

## 📘 Author’s Note

This educational example demonstrates how to manipulate context filters in DAX.  
More examples (Rank, KPIs, dynamic visuals) will be added soon to this repository.

---

> 💡 _Repository under setup – more Power BI DAX tutorials coming soon!_
