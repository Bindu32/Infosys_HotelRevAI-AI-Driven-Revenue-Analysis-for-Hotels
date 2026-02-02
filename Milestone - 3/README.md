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

### 🔹 Data Preparation in Power BI

- A **Date dimension table** was created to ensure all 12 months are available, even if historical data spans fewer months.
- Monthly aggregation of booking data was performed.
- Month sorting was handled using a **Month Number** column to ensure chronological order.
- Modelling -> Create Table -> Date_of_each

```DAX
  Date_of_each = 
ADDCOLUMNS (
    CALENDAR (
        DATE ( YEAR ( MIN ( Fact_Bookings[Date] ) ), 1, 1 ),
        DATE ( YEAR ( MAX ( Fact_Bookings[Date]) ), 12, 31 )
    ),
    "Year", YEAR ( [Date] ),
    "Month Number", MONTH ( [Date] ),
    "Month Name", FORMAT ( [Date], "MMM" ),
    "Year-Month", FORMAT ( [Date], "YYYY-MM" ),
    "Month End Date", EOMONTH ( [Date], 0 )
)
```

### 🔹 Monthly Bookings Table (DAX)

```DAX
Monthly_Bookings = 
SUMMARIZE (
    'Date_of_each',
    'Date_of_each'[Year],
    'Date_of_each'[Month Number],
    'Date_of_each'[Month Name],
    'Date_of_each'[Month End Date],
    "Total_Bookings", COALESCE ( [Total Bookings], 0 )
)
)
```
### Forecasting Using Python (Google Colab)

Forecasting was implemented using Facebook Prophet to model seasonality and trends in booking data.

```python
# ==============================
# 1. Upload file (Google Colab)
# ==============================
from google.colab import files
uploaded = files.upload()

# ==============================
# 2. Imports
# ==============================
import pandas as pd
from prophet import Prophet

# ==============================
# 3. Load dataset
# ==============================
# Make sure the filename matches exactly
df = pd.read_csv("monthly_bookings.csv")

# ==============================
# 4. Check columns (Debug – optional)
# ==============================
print("Columns in dataset:", df.columns)

# ==============================
# 5. Create datetime column (ds)
# ==============================
# Prefer Month end date if present
if "Month end date" in df.columns:
    df["ds"] = pd.to_datetime(df["Month end date"])
else:
    # Create date using Year + Month + Day
    df["ds"] = pd.to_datetime(
        df["Year"].astype(str) + "-" +
        df["Month"].astype(str) + "-" +
        df["Day"].astype(str)
    )

# ==============================
# 6. Rename target column to 'y'
# ==============================
# IMPORTANT: Column name must match exactly
df.rename(columns={"Sum of Total_Bookings": "y"}, inplace=True)

# ==============================
# 7. Verify required columns
# ==============================
print(df[["ds", "y"]].head())

# ==============================
# 8. Keep only Prophet-required columns
# ==============================
df = df[["ds", "y"]]

# ==============================
# 9. Sort by date
# ==============================
df = df.sort_values("ds")

# ==============================
# 10. Initialize Prophet model
# ==============================
model = Prophet(
    yearly_seasonality=True,
    weekly_seasonality=False,
    daily_seasonality=False,
    changepoint_prior_scale=0.05  # smoother trend, avoids sharp drops
)

# ==============================
# 11. Fit model
# ==============================
model.fit(df)

# ==============================
# 12. Create future dataframe (12 months)
# ==============================
future = model.make_future_dataframe(periods=1, freq="M")

# ==============================
# 13. Forecast
# ==============================
forecast = model.predict(future)

# ==============================
# 14. Fix negative forecast values (IMPORTANT)
# ==============================
forecast["yhat"] = forecast["yhat"].clip(lower=0)
forecast["yhat_lower"] = forecast["yhat_lower"].clip(lower=0)
forecast["yhat_upper"] = forecast["yhat_upper"].clip(lower=0)

# ==============================
# 15. Preview forecast
# ==============================
print(forecast[["ds", "yhat", "yhat_lower", "yhat_upper"]].tail(15))

# ==============================
# 16. Export forecast to CSV
# ==============================
forecast[["ds", "yhat", "yhat_lower", "yhat_upper"]].to_csv(
    "Forecast.csv",
    index=False
)

# ==============================
# 17. Download file
# ==============================
files.download("Forecast.csv")
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
