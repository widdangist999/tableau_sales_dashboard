# Calculated Fields

This document lists the calculated fields used in the **Year-over-Year Sales & Customer Dashboard**.

## Naming Convention

| Prefix | Meaning |
|--------|---------|
| **CY** | Current Year, the year chosen in the **Select Year** parameter |
| **PY** | Previous Year, which is `Select Year - 1` |
| **% Diff** | Percentage change between CY and PY |
| **MIN/MAX** | Marks the highest and lowest points on a trend line |

Because every chart uses the CY and PY fields, choosing a different year in the filter updates the whole dashboard. For example, selecting **2022** compares 2022 vs 2021.

---

## 1. Sales Dashboard

### 1.1 KPI Sales

**CY Sales**

```
IF YEAR([Order Date]) = [Select Year] THEN [Sales]
END
```

**PY Sales**

```
IF YEAR([Order Date]) = [Select Year] - 1 THEN [Sales]
END
```

**% Diff Sales**

```
(SUM([CY sales]) - SUM([PY sales])) / SUM([PY sales])
```

**MIN/MAX Sales**

```
IF SUM([CY sales]) = WINDOW_MAX(SUM([CY sales])) THEN
    SUM([CY sales])
ELSEIF SUM([CY sales]) = WINDOW_MIN(SUM([CY sales])) THEN
    SUM([CY sales])
END
```

`WINDOW_MAX` and `WINDOW_MIN` return a value only for the highest and lowest month. The sparkline colors these two points blue (highest) and orange (lowest).

### 1.2 KPI Profit

Same logic as KPI Sales, replacing `[Sales]` with `[Profit]` (fields: CY Profit, PY Profit, % Diff Profit, MIN/MAX Profit).

### 1.3 KPI Quantity

Same logic as KPI Sales, replacing `[Sales]` with `[Quantity]`.

---

## 2. Customer Dashboard

### 2.1 KPI Customers

**PY Customers**

```
IF YEAR([Order Date]) = [Select Year] - 1 THEN [Customer ID]
END
```

**CY Customers**

```
IF YEAR([Order Date]) = [Select Year] THEN [Customer ID]
END
```

**% Diff Customers**

```
(COUNTD([CY Customer]) - COUNTD([PY Customer])) / COUNTD([PY Customer])
```

**MIN/MAX Customers**

```
IF COUNTD([CY Customer]) = WINDOW_MAX(COUNTD([CY Customer])) THEN
    COUNTD([CY Customer])
ELSEIF COUNTD([CY Customer]) = WINDOW_MIN(COUNTD([CY Customer])) THEN
    COUNTD([CY Customer])
END
```

### 2.2 KPI Sales per Customer

**CY Sales per Customer**

```
SUM([CY sales]) / COUNTD([CY Customer])
```

**PY Sales per Customer**

```
SUM([PY sales]) / COUNTD([PY Customer])
```

**Diff Sales per Customer**

```
([CY Sales per Consumer] - [PY Sales per Consumer]) / [PY Sales per Consumer]
```

**MIN/MAX Sales per Customer**

```
IF [CY Sales per Consumer] = WINDOW_MAX([CY Sales per Consumer]) THEN
    [CY Sales per Consumer]
ELSEIF [CY Sales per Consumer] = WINDOW_MIN([CY Sales per Consumer]) THEN
    [CY Sales per Consumer]
END
```

### 2.3 KPI Orders

**CY Orders**

```
IF YEAR([Order Date]) = [Select Year] THEN [Order ID]
END
```

**PY Orders**

```
IF YEAR([Order Date]) = [Select Year] - 1 THEN [Order ID]
END
```

**% Diff Orders**

```
(COUNTD([CY Orders]) - COUNTD([PY Orders])) / COUNTD([PY Orders])
```

**MIN/MAX Orders**

```
IF COUNTD([CY Orders]) = WINDOW_MAX(COUNTD([CY Orders])) THEN
    COUNTD([CY Orders])
ELSEIF COUNTD([CY Orders]) = WINDOW_MIN(COUNTD([CY Orders])) THEN
    COUNTD([CY Orders])
END
```

### 2.4 Customers Distribution

**Nr Orders per Customer** (LOD expression)

```
{ FIXED [CY Customer] : COUNTD([CY Orders]) }
```

This counts the number of orders for each customer in the selected year. It is used as the X axis of the *Customers Distribution by Nr of Orders* chart, where the Y axis shows how many customers fall into each group.

---

## Notes

- The `Select Year` control is the year filter on the dashboard, and all CY and PY fields depend on it.
- `[Sales per Consumer]` in the formulas above is the original field name used in the workbook, referring to sales per customer.
