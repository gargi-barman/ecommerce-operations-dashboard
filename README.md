# E-Commerce Operations Dashboard (Excel)

*Originally built: June 2026 · Updated July 2026: added Delivery Speed Tier (INDEX-MATCH), business-day TAT (NETWORKDAYS), Order Summary view (TEXTJOIN), and a full Power Query transformation pipeline.*

## Project Overview

Analysis of 89,316 orders from the Ecommerce Order & Supply Chain Dataset (Kaggle) to track key operational KPIs using Microsoft Excel.

Built as part of my transition into Operations Analytics.

---

## Central Questions

- What share of orders are delivered on time, and how has that changed 2016–2018?
- How has average delivery time (TAT) trended over the years, and where does the biggest improvement come from?
- How reliable is the "cancelled" order signal, and what does it say about operational health?
- Does business-day TAT (excluding weekends) tell a different story than calendar-day TAT?
- What share of orders fall into each delivery speed tier, and does that align with the headline OTD%?

---

## Key KPIs

| Metric            | Value      |
| ----------------- | ---------- |
| Total Orders      | 89,316     |
| OTD%              | 90.3%      |
| Avg TAT           | 11.97 days |
| Cancelled Orders  | 409        |
| Cancellation Rate | 0.46%      |

---

## Key Insights & Recommendations

**1. Order volume grew steadily — from 293 orders (2016) to 47,899 orders (2018).**
Recommendation: With volume scaling this fast, it's worth checking whether fulfillment capacity (staffing, warehouse throughput) scaled at the same pace — rapid growth without matching capacity is often where delivery performance starts slipping.

**2. Most of the improvement in delivery speed happened between 2016 and 2017.** Average TAT dropped from 18.91 days (2016) to 12.35 days (2017), then only slightly further to 11.61 days (2018).
Recommendation: Identify what specific operational change drove the 2016→2017 improvement, and check if it's repeatable. The much smaller 2017→2018 gain suggests the earlier fix may have been a one-time change rather than a sustained process improvement — worth confirming before assuming performance will keep improving on its own.

**3. 90.3% of delivered orders were On Time.**
Recommendation: This is a strong headline number, but it's worth pairing with the Tier findings below before treating it as the full picture of delivery performance (see Insight 5).

**4. Cancellation rate is unusually low at 0.46% (409 of 89,316 orders).** Typical e-commerce cancellation benchmarks run 2–5%, so 0.46% stands out. The likely explanation isn't that this business rarely has problem orders — it's that this dataset's "Cancelled" status is probably a narrow category. Returns, failed deliveries, or orders lost in transit may be tracked under a different status entirely (or not captured at all in this dataset), meaning the true rate of "orders that didn't go as planned" is probably higher than 0.46% suggests.
Recommendation: Before using this number in any real reporting, confirm what "Cancelled" actually excludes. If returns and failed deliveries aren't captured here, cancellation rate alone understates operational risk — a more complete "problem order rate" metric would need to combine Cancelled + Late + Not Delivered.

**5. Delivery Tier reveals a gap the OTD% number hides.** Despite a 90.3% on-time rate, 44.8% of all orders (40,001) fall into the Slow tier (11+ days), with 33.4% (29,840) Medium and only 19.7% (17,586) Fast. This isn't a data error — OTD and Tier are answering two different questions. OTD asks "did we deliver by the date we promised the customer." Tier asks "how many actual days did this take, judged against a fixed internal standard for what counts as fast." An order can clear a generous promise (On Time) while still performing poorly by an absolute speed standard (Slow tier).
Recommendation: Investigate how `Order_estimated_delivery_date` is set. If promises are being set loosely enough that mediocre fulfillment still counts as "on time," OTD% alone isn't a reliable signal of real operational speed — it mainly measures whether the company kept its word, not whether it performed well. A tighter, more honest estimate would surface the real performance gap that's currently hidden.

---

## Skills Demonstrated

- Data Cleaning: TRIM, PROPER, IF
- Calculated Columns: TAT (days), OTD Flag, Delivery Speed Tier, Business-Day TAT
- Lookup Functions: INDEX-MATCH (approximate match) for tier classification
- Date Functions: NETWORKDAYS
- Text Functions: TEXTJOIN
- Formulas: COUNTA, COUNTIF, AVERAGEIF, nested IF
- Power Query (Get & Transform): data type correction, filtering, trimming, text standardization, value replacement, column renaming — full transformation pipeline from raw import
- PivotTables & PivotCharts
- KPI Dashboard design in Excel

---

## Project Walkthrough

### Step 1 — Data Cleaning

### Step 1 — Data Cleaning

Applied TRIM on Order ID and Customer ID. Used PROPER on Order Status. Added TAT_days and OTD_flag calculated columns using nested IF formulas.

**Before (v1):**

[

![Cleaned Data - Original](https://github.com/gargi-barman/ecommerce-operations-dashboard/raw/main/Cleaned_Data.png)

](/gargi-barman/ecommerce-operations-dashboard/blob/main/Cleaned_Data.png)

**After (v2) — added Delivery_Tier and TAT_business_days columns:**

[

![Cleaned Data - Updated](https://github.com/gargi-barman/ecommerce-operations-dashboard/raw/main/Cleaned_Data_Updated.png)

](/gargi-barman/ecommerce-operations-dashboard/blob/main/Cleaned_Data_Updated.png)

### Step 2 — Delivery Speed Tier (INDEX-MATCH) & Business-Day TAT (NETWORKDAYS)

Built a reference table (Fast / Medium / Slow, based on TAT thresholds) and used INDEX-MATCH with approximate match (match_type 1) to classify every order into a delivery speed tier. **This reference table is an assumption built for this analysis — it is not part of the original dataset.** Also added a `TAT_business_days` column using NETWORKDAYS to measure delivery time excluding weekends, as a complement to the calendar-day TAT_days column.

### Step 3 — Order Summary View (TEXTJOIN)

Built a separate `Order_Summary` sheet combining Order ID, Status, and Tier into a single readable line per order — a compact, scannable export view rather than a column bolted onto the working data table.

[

![Order Summary](https://github.com/gargi-barman/ecommerce-operations-dashboard/raw/main/Order_Summary.png)

](/gargi-barman/ecommerce-operations-dashboard/blob/main/Order_Summary.png)

### Step 4 — Power Query Transformation Pipeline

Imported Raw_Data through Power Query's Query Editor and applied a full transformation chain: corrected data types, filtered for blank Order_IDs (none found), trimmed whitespace on text fields, standardized status casing, corrected a spelling inconsistency ("Canceled" → "Cancelled"), and renamed columns to match project naming conventions. Loaded to a dedicated `PowerQuery_Cleaned_Data` sheet.

[

![Power Query Applied Steps](https://github.com/gargi-barman/ecommerce-operations-dashboard/raw/main/Power_Query_Steps.png)

](/gargi-barman/ecommerce-operations-dashboard/blob/main/Power_Query_Steps.png)

[

![Power Query Cleaned Data](https://github.com/gargi-barman/ecommerce-operations-dashboard/raw/main/Power_Query_Cleaned_Data.png)

](/gargi-barman/ecommerce-operations-dashboard/blob/main/Power_Query_Cleaned_Data.png)

### Step 5 — Pivot Summary & KPI Calculations

Built three PivotTables: Monthly Order Volume, OTD Flag Summary, Average TAT by Year. Calculated OTD% and Cancellation Rate using COUNTIF and COUNTA formulas.

[

![Pivot Summary](https://github.com/gargi-barman/ecommerce-operations-dashboard/raw/main/Pivot_Summary.png)

](/gargi-barman/ecommerce-operations-dashboard/blob/main/Pivot_Summary.png)

### Step 6 — KPI Dashboard

Complete dashboard with 5 KPI cards and 3 charts — Monthly Order Volume (line chart), OTD Flag Breakdown (column chart), Average TAT by Year (column chart).

[

![Dashboard](https://github.com/gargi-barman/ecommerce-operations-dashboard/raw/main/Dashboard.png)

](/gargi-barman/ecommerce-operations-dashboard/blob/main/Dashboard.png)

---

## Assumptions & Notes

- The Delivery Speed Tier reference table (Fast: 0–5 days, Medium: 6–10 days, Slow: 11+ days) is an assumption built for this analysis to enable INDEX-MATCH classification — it is not part of the original Kaggle dataset.
- The cancellation rate (0.46%) reflects this dataset's own status definitions and likely does not capture returns or failed deliveries separately.

---

## Version History

- **v1 (June 2026):** Initial build — data cleaning, KPI dashboard, pivot analysis
- **v2 (July 2026):** Added Delivery Speed Tier (INDEX-MATCH), business-day TAT (NETWORKDAYS), Order Summary view (TEXTJOIN), full Power Query transformation pipeline, Central Questions and Recommendations sections

---

## Dataset

Source: [Ecommerce Order & Supply Chain Dataset — Kaggle](https://www.kaggle.com/datasets/bytadit/ecommerce-order-dataset)

---

## Tools Used

- Microsoft Excel (PivotTables, PivotCharts, Formulas, Power Query, Dashboard Design)

---

## Files

- [Ecommerce_Operations_Dashboard.xlsx](https://github.com/gargi-barman/ecommerce-operations-dashboard/blob/main/Ecommerce_Operations_Dashboard.xlsx)

---

*Built by Gargi Barman | Aspiring Operations Analyst*
