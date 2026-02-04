# 🏨 HotelRevAI – Milestone 3  
## Forecasting & Cancellation Trend Analysis (Power BI + Python)

---

## 📌 Project Overview

Milestone 3 of the **HotelRevAI – AI Driven Revenue Analysis for Hotels** project focuses on converting historical booking data into **forward-looking, actionable insights**.  
This milestone extends the Power BI model developed in **Milestone 2** by incorporating **forecasting**, **cancellation analysis**, **no-show trends**, and **booking behavior insights** to support strategic hotel decision-making.

---

## 🎯 Objectives

- Forecast future hotel booking demand
- Analyze cancellation and no-show behavior
- Identify seasonal and trend-based booking patterns
- Build an interactive Power BI dashboard for business users

---

## 📂 Dataset Overview

Key columns used from the `Fact_Bookings` table:

- Date, Month, Year  
- Total Bookings  
- New Bookings  
- Cancellations  
- No_shows  
- Occupancy Rate  
- ADR  
- Branch_Id  

---

## 🧠 Forecasting Approach

### 🔹 Monthly Bookings Table (DAX)

```DAX
Monthly_Bookings = SUMMARIZE( Fact_Bookings,    Fact_Bookings[Month_End_Date],
    "Total_Bookings", COALESCE ( [Total Bookings], 0 ))
```
### Forecasting Using Python (Google Colab)

Forecasting was implemented using Facebook Prophet to model seasonality and trends in booking data.

```python

# 1. Upload file
from google.colab import files
uploaded = files.upload()

# 2. Imports
import pandas as pd
from prophet import Prophet

# 3. Load dataset
df = pd.read_csv("monthly_bookings.csv")

# 4. Prepare data
df["ds"] = pd.to_datetime(df["Month_End_Date"])
df.rename(columns={"Sum of Total_Bookings": "y"}, inplace=True)
df = df[["ds", "y"]].sort_values("ds")

# 5. Train Prophet model
model = Prophet(
    yearly_seasonality=True,
    weekly_seasonality=False,
    daily_seasonality=False,
    changepoint_prior_scale=0.05
)
model.fit(df)

# 6. Forecast next 36 months
future = model.make_future_dataframe(periods=36, freq="M")
forecast = model.predict(future)

# 7. Remove negative values
forecast["yhat"] = forecast["yhat"].clip(lower=0)
forecast["yhat_lower"] = forecast["yhat_lower"].clip(lower=0)
forecast["yhat_upper"] = forecast["yhat_upper"].clip(lower=0)

# 8. Create full date range (2024–2026)
full_dates = pd.date_range(
    start="2024-01-31",
    end="2026-12-31",
    freq="M"
)

full_df = pd.DataFrame({"ds": full_dates})

# 9. Merge forecast with full date range
final_forecast = full_df.merge(
    forecast[["ds", "yhat", "yhat_lower", "yhat_upper"]],
    on="ds",
    how="left"
)

# 10. Fill missing 2024 months (backfill)
final_forecast[["yhat", "yhat_lower", "yhat_upper"]] = (
    final_forecast[["yhat", "yhat_lower", "yhat_upper"]]
    .fillna(method="bfill")
)

# 11. Add helper columns for Power BI
final_forecast["Year"] = final_forecast["ds"].dt.year
final_forecast["Month"] = final_forecast["ds"].dt.strftime("%B")
final_forecast["Month_Number"] = final_forecast["ds"].dt.month

# 12. Export final forecast
final_forecast.to_csv("Forecast_2024_2026.csv", index=False)
files.download("Forecast_2024_2026.csv")

```

### Power BI Forecast Integration

- The generated Forecast.csv file was imported into Power BI.
- Forecast data was connected to the Date table.
- Forecasted values were compared against actual historical bookings.

## DAX Measures Used
### Cancelled Bookings
```
Cancelled Bookings = 
SUM ( Fact_Bookings[Cancellations] )
```

### Cancellation Percentage
```
Cancellation % = 
DIVIDE ( [Cancelled Bookings], [Total Bookings], 0 )
```

### No-Shows
```
No Shows = 
SUM ( Fact_Bookings[No_Shows] )
```

### Forecasted Bookings
```
Forecasted Bookings = 
SUM(Forecast[yhat])
```

### Actual Bookings
```
Total Actual Bookings = SUM(Monthly_Bookings[Total_Bookings])
```

### Peak Forecast Month
```
Peak Forecast Month = 
MAXX(
    VALUES('Forecast'[ds]),
    SUM('Forecast'[yhat])
)

```

### Total Forecast Bookings
```
Total Forecasted Bookings = 
CALCULATE(
    SUM('Forecast'[yhat]),
    'Forecast'[ds] > MAX('Date'[Date])
)

```

### Average Cancellation Rate
```
Avg Cancellation Rate = 
AVERAGEX(
    VALUES('Date_of_each'[Month End Date]),
    [Cancellation %]
)

```

## Dashboard Visuals
### 🔹 Forecast & Demand Analysis
- Actual vs Forecasted Bookings
- Forecasted Booking Trend Over Time
- Forecast Confidence Range (Upper & Lower Bounds)
- 
###🔹 Cancellation & No-Show Analysis
- Monthly Cancellation Rate and Cancelled Bookings
- No-Show Trends by Month

### 🔹 KPIs
- Total Bookings
- Forecasted Demand
- Cancellation Percentage
- No-Show Count

### 🔹 Interactivity
Slicers for:
- Hotel Branch
- Year
- Month
