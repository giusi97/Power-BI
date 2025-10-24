# Random numbers between 1.5 and 2.5 with ≥ 0.5 spacing (Power BI / DAX)

This guide explains **step by step** how to create numbers in the range **[1.5, 2.5]**
and what happens when you try to keep at least **0.5 spacing** between them.

Each example can be used as a **calculated column** in Power BI.

---

## 1️⃣ Simple random values

`RAND()` generates a random number between 0 and 1.  
To shift it to **[1.5, 2.5]**, add 1.5:

```DAX
RandomNumber = RAND() + 1.5
```

✅ Produces random decimals between 1.5 and 2.5  
❌ Two rows might be almost identical (1.73 and 1.74)

---

## 2️⃣ Random values with 0.1 steps

If you prefer discrete values (1.5, 1.6, …, 2.5):

```DAX
RandomStep = (RANDBETWEEN(0, 10) * 0.1) + 1.5
```

✅ Easier to read  
❌ Still not enforcing 0.5 spacing (you can get 1.5 and 1.6)

---

## 3️⃣ Deterministic cyclic pattern (global)

This version is **not truly random**, but it’s controlled.  
We rank rows with `RANKX`, cycle ranks through 1→2→3,  
and assign 1.5 / 2.0 / 2.5 accordingly.

```DAX
GlobalRank =
RANKX(
    ALL('Data'),
    'Data'[ID],
    ,
    ASC,
    DENSE
)

DiscreteValueCyclic =
VAR idx = MOD('Data'[GlobalRank] - 1, 3) + 1
RETURN
SWITCH(
    idx,
    1, 1.5,
    2, 2.0,
    3, 2.5
)
```

✅ Deterministic and predictable  
❌ Values repeat every three rows

---

## 4️⃣ Cyclic pattern per group

If you want the pattern repeated **inside each group** (e.g. Category):

```DAX
RankInGroup =
VAR g = 'Data'[Group]
RETURN
RANKX(
    FILTER(ALL('Data'), 'Data'[Group] = g),
    'Data'[ID],
    ,
    ASC,
    DENSE
)

DiscreteValueCyclicPerGroup =
VAR idx = MOD('Data'[RankInGroup] - 1, 3) + 1
RETURN
SWITCH(
    idx,
    1, 1.5,
    2, 2.0,
    3, 2.5
)
```

✅ Keeps spacing 0.5 **within each group**  
❌ Repeats pattern if group has more than 3 rows

---

## 5️⃣ Strict version – unique top 3 per group

Mathematically, only **three distinct values** can fit in [1.5, 2.5]  
if we keep spacing ≥ 0.5.

```DAX
AssignedValueUniqueTop3 =
VAR k = 'Data'[RankInGroup]
RETURN
SWITCH(
    TRUE(),
    k = 1, 1.5,
    k = 2, 2.0,
    k = 3, 2.5,
    BLANK()
)
```

✅ Perfect 0.5 spacing  
❌ Works only for the first 3 rows per group

---

## Summary table

| Version | Works for | Spacing guaranteed? | Notes |
|----------|-----------|--------------------|--------|
| `RAND()+1.5` | simple random | ❌ | Fast and easy |
| `RANDBETWEEN` scaled | discrete steps | ❌ | Nice readable values |
| `Cyclic` | ordered pattern | ⚠️ | Predictable, repeats |
| `Per-group cyclic` | repeated per group | ⚠️ | Good for categories |
| `Unique top-3` | first 3 per group | ✅ | Strict spacing rule |

---

## How to adapt names

Replace `'Data'`, `'Data'[ID]`, and `'Data'[Group]` with your actual table and columns  
(e.g., `Sales`, `Sales[ProductID]`, `Sales[Category]`).

---

## Quick checklist

1. Create the columns you need (they are independent).  
2. Adjust table/column names.  
3. Test results in a simple table visual.  
4. Done — you now have controlled random or spaced values.

---

⭐ **Tip:**  
If you don’t have a unique ID, create one in Power Query (Add Index Column).  
`RANKX` needs a stable order to work correctly.
