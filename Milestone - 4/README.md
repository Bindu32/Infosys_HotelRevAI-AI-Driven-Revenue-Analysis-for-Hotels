# Hotel Revenue Strategy — Milestone 4  

## Overview  

This Milestone 4 extends the Power BI model from Milestone 3 to provide a **comprehensive Revenue Strategy Dashboard**. The dashboard offers insights into:  

- Revenue and occupancy trends  
- Room category and pricing performance  
- Upsell potential by guest segment and season  
- Booking channel and branch revenue comparison  
- Pricing tier recommendations  
- Seasonal promotion opportunities  

All visuals are **interactive** with slicers for Branch, Room Type, Booking Source, and Date (Month / Year).  

---

## Measures Used  

| Measure | Description |
|---------|-------------|
| **Revenue Growth %** | Month-over-month growth in total revenue |
| **Upsell Potential %** | Extra revenue from add-ons (dining, spa, upgrades) as % of room revenue |
| **Profit Margin %** | Profit relative to total revenue |
| **ADR_new** | Average Daily Rate per room category |
| **RevPAR_new** | Revenue per available room |

```DAX
Revenue Growth % = 
VAR CurrentRevenue = [Total Revenue]
VAR PreviousRevenue =
    CALCULATE(
        [Total Revenue],
        DATEADD('Date'[Date], -1, MONTH)
    )
RETURN
DIVIDE(CurrentRevenue - PreviousRevenue, PreviousRevenue)
```

``` DAX
Upsell Potential % = 
DIVIDE(
    SUM('Fact_Bookings'[Revenue]) - SUM('Fact_Bookings'[Room_Revenue]),
    SUM('Fact_Bookings'[Room_Revenue]),
    0
) * 100
```
```DAX
Profit Margin % = 
DIVIDE(SUM('Fact_Bookings'[Profit]),'Fact_Bookings'[Total Revenue])
```

---

## Calculated Columns  

- **Season** — Winter, Spring, Summer, Fall  
- **Room Category / Type** — Standard, Deluxe, Premium  
- **Pricing Tier** — Premium / Standard / Budget (based on ADR thresholds)  
- **Branch / Location** — Hotel branch for filtering  

```DAX
Pricing Tier = 
SWITCH(
    TRUE(),
    [ADR] >= 130, "Premium",
    [ADR] >= 120, "Standard",
    "Budget"
)
```
---
## Tooltip Measures for Visual Insights  

These measures are **added to the Tooltip field** of each visual to show insight notes when hovering.

```DAX
-- KPI Cards
KPI_Insights =
"Upsell Potential % = 79.50 → strong opportunity to push premium services
ADR_new = 9.33K → high average daily rate
RevPAR_new = 2.69K → moderate revenue per available room
Revenue Growth % = 0.41 → stable growth month-over-month"

-- Column Chart 1: Total Revenue by Season & Room Category
Revenue_By_Season_Insights =
"Winter is the highest revenue season, followed by Summer, then Spring.
Premium rooms generate most revenue.
Deluxe rooms contribute very low revenue → opportunity to upsell or reprice."

-- Column Chart 2: Upsell Potential % by Guest Type & Season
Upsell_By_Guest_Insights =
"Summer shows highest upsell potential across all guest types.
Focus promotional campaigns and upsell offers in Summer."

-- Column Chart 3: Revenue, RevPAR, Occupancy, ADR by Room Category
RoomCategory_Performance_Insights =
"Total revenue is high across room types.
RevPAR and occupancy are very low at 0 for certain categories → underutilized rooms.
Standard rooms have low ADR; other categories show higher rates → pricing adjustment needed."

-- Scatter Plot: ADR vs Occupancy by Room Category & Season
Scatter_ADR_Occ_Insights =
"Winter occupancy shows outlier → very high or low compared to others.
Most rooms cluster near similar ADR and occupancy → pricing mostly aligned.
Outliers represent opportunity to optimize price or promote underperforming rooms."

-- Map: Total Revenue & Occupancy by Guest Country
Map_Revenue_Country_Insights =
"Shows revenue distribution geographically.
Highlights top-performing countries → target marketing or upsell campaigns."

-- Line Plot: Total Revenue & Occupancy % Trend by Month
Revenue_Monthly_Insights =
"March shows drop in revenue and occupancy → possible seasonal dip.
Remaining months: Total revenue 6–8M, Occupancy 20–30% → stable.
Opportunity: target March with promotions or packages to boost occupancy."
```

## Dashboard Visuals & Insights

- KPI Cards: ADR, RevPAR, Revenue Growth %, Upsell Potential %
- Revenue & Occupancy Trend Chart
- Season Performance Column Chart
- Room Type Performance Chart
- Pricing Effectiveness Scatter Plot (ADR vs Occupancy)
- Upsell Opportunity Chart
- Booking Source & Branch Comparison
- Pricing Tier Recommendation (Season & Room Type)
- Seasonal Promotion Insight Chart
All visuals are interactive with slicers: Branch, Room Type, Booking Source, and Date (Month / Year).

---

## Strategy Recommendations
### Data-driven Pricing
- Increase ADR for underpriced but high-demand rooms (from scatter plot)
- Offer discounts or promotional pricing for low-demand rooms and seasons

### Upsell & Room Upgrade
- Target guest segments with high Upsell Potential % (summer, premium guests)
- Promote add-ons: spa, dining, premium packages, room upgrades

### Seasonal Promotions
- Focus marketing campaigns on low occupancy months (e.g., March)
- Package offers to improve occupancy without heavily discounting high-revenue seasons

### Booking Channels & Branch Strategy
- Prioritize high-revenue, low-cost booking channels
- Focus on top-performing branches for upsell campaigns
- Optimize underperforming branches through promotions and targeted marketing

## GitHub Repository Structure
```
## Repository Structure

Hotel_Business_Understand_Improve_Revenue/
|-- Milestone 4/
    |-- Milestone4_PowerBI.pbix
    |-- screenshots/
        |-- Revenue_Strategy_Dashboard.png
    |-- README.md

```

## Business Impact
- Clear pricing adjustments increase revenue per room and RevPAR
- Upsell targeting improves ancillary revenue streams
- Seasonal promotions improve occupancy during low-demand months
- Channel & branch analysis optimizes marketing spend and focus
- Overall, the hotel can make data-driven decisions to maximize total revenue and profitability
