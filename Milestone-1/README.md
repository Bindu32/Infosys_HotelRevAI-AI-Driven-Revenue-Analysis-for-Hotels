# Infosys_HotelRevAI-AI-Driven Revenue Analysis for Hotels  
**Milestone-1**

---

## 📌 Project Statement
Hotels must understand their occupancy patterns, guest demographics, and pricing effectiveness to improve revenue performance.  
This project delivers an analytical solution using **Power BI** to monitor room bookings, Average Daily Rate (ADR), guest profiles, and seasonal trends. The insights generated support hotel management in making data-driven decisions related to **pricing strategies, promotions, upselling, and room optimization**.

---

## 📦 Module 1: Data Modeling and Ingestion

### Tasks Overview
- **Task 1:** Load booking, customer, and room data  
- **Task 2:** Build a star schema with Date, Room, Customer, and Hotel Branch dimensions  
- **Task 3:** Calculate booking duration, room category, and stay type  

---

## 🔹 Step 1: Data Loading and Initial Exploration
- Loaded the dataset into **Power BI**.
- Opened **Power Query Editor** to transform the data.
- Explored all available features and analyzed their relevance.
- Verified data quality by checking:
  - Missing values  
  - Incorrect data types  
  - Data consistency  

---

## 🔹 Step 2: Profit Calculation
- Observed that the **Profit** column was empty.
- Created a calculated column using a custom formula:
Profit = Total_Revenue – Total_Cost

---

## 🔹 Step 3: Booking Duration Calculation
- Created a new column to compute booking duration.
Booking_Duration = Checkout_Date – Checkin_Date

- Booking duration directly depends on check-in and check-out dates.

---

## 🔹 Step 4: Stay Type Classification
- Defined **stay type** based on booking duration:
  - Long Stay
  - Medium Stay
  - Short Stay
Stay_Type =
IF Booking_Duration >= 6 → "Long Stay"
ELSE IF Booking_Duration < 4 → "Short Stay"
ELSE → "Medium Stay"

---

## 🔹 Step 5: Room Category Calculation
- Room categories were derived based on **Occupancy Rate**.
- Room categories include:
  - Standard
  - Premium
  - Deluxe
Room_Category =
IF Occupancy_Rate >= 0.79 → "Deluxe"
ELSE IF Occupancy_Rate <= 0.75 → "Standard"
ELSE → "Premium"


- Ensured correct data types after column creation.

✅ **Task 3 completed:** Booking duration, stay type, and room category calculated.

---

## 🔹 Step 6: Date Dimension Table Creation
- Duplicated the **Fact_Bookings** table.
- Retained only the following columns:
  - Date  
  - Month  
  - Season  
  - Weekday  
  - Holiday  
- Removed duplicate records.

---

## 🔹 Step 7: Room Dimension Table Creation
- Duplicated the **Fact_Bookings** table.
- Retained:
  - Occupancy_Rate  
  - ADR  
  - Room_Category  
  - Available_Rooms  
- Removed duplicates.
- Added an **Index Column** as `Room_ID`.

---

## 🔹 Step 8: Customer Dimension Table Creation
- Duplicated the **Fact_Bookings** table.
- Retained:
  - Guest_Type  
  - Guest_Country  
  - Market_Segment  
  - Average_Review_Score  
- Removed duplicates.
- Added an **Index Column** as `Customer_ID`.

---

## 🔹 Step 9: Hotel Branch Dimension
- Since the dataset contains only one hotel, a **Hotel Branch** table was manually created.
- Added columns:
  - Branch_ID  
  - Branch_Name  

---

## 🔹 Step 10: Star Schema Design
- Built a **star schema** by connecting dimension tables to the fact table.
- Created foreign keys (`Room_ID`, `Customer_ID`, `Branch_ID`) using **Merge Queries**.
- Established **one-to-many** relationships in **Model View**.

✅ **Task 1 & Task 2 completed successfully.**

---

## 📊 Visualizations & Data Analysis

### KPI Overview
- Created KPI cards for:
  - Total Revenue  
  - Profit  
  - Total Bookings  
  - ADR  
  - Occupancy Rate  

These metrics provide a clear overview of hotel performance and financial efficiency.

---

### Revenue Trend Analysis
- **Line Chart:** Total Revenue vs. Days
- Observations:
  - Highest revenue on **5th and 7th days**
  - Lowest revenue on **3rd, 18th, and 30th days**

---

### Occupancy Analysis
- **Pie Chart** used to visualize room utilization efficiency.
- Helps evaluate how effectively rooms are occupied.

---

### Stay Type Analysis
- **Clustered Column Chart:** Bookings vs. Stay Type
- Created **11 visualizations** across different dimensions.
- Key insights:
  - UK customers generally prefer **short stays**
  - During **spring season**, long and medium stays are nearly equal
  - Most customer segments prefer **long stays**

---

### Booking Channel Analysis
- **Clustered Bar Chart:** Total Revenue vs. Booking Channel
- Created **12 visualizations** across different features.
- Key insights:
  - Premium members mostly book through **OTA channels**
  - OTA Revenue: **10M**
  - Direct Channel Revenue: **7M**

---

## ✅ Summary
This milestone establishes a solid analytical foundation through:
- Robust data modeling
- Star schema implementation
- Derived business metrics
- Insightful visual dashboards

The solution provides actionable insights into **revenue trends, customer behavior, stay patterns, and booking channels**, enabling smarter hotel revenue management decisions.

---


