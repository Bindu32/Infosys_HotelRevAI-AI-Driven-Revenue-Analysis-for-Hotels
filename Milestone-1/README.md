# Infosys_HotelRevAI-AI-Driven-Revenue-Analysis-for-Hotels/Milestone-1

### Project Statement:
Hotels must understand their occupancy patterns, guest demographics, and pricing effectiveness to
improve revenue. This project builds an analytical solution in Power BI to track room bookings, average
daily rates, guest profiles, and seasonal trends. It helps hotel management make decisions around
promotions, upselling, pricing strategies, and room optimization.

### Module - 1:
#### Data Modeling and Ingestion
- Task-1: Load booking, customer, and room data
- Task-2: Build a star schema with Date, Room, Customer, Hotel branches
- Task-3: Calculate booking duration, room category, and stay type

### Step - 1:
- Loading dataset into PowerBI.
- Transform the data then power query Editor opens.
- Observe the features what are there, analyse them in what way they are useful.
- Here we are going to check the data that is, is there any errors, all the data types of features are correctly represented or not. These all can be see by clicking view

### Step - 2:
- I have noticed that , profit column is empty.
- we fill profit column by applying formula.
- Add column -> custom column -> formula
- *profit* *=* *Total_Revenue* - *Total_cost*

### Step - 3:
#### Calculate booking duration
- Add column -> custom column -> formula
- #### booking_duration = checkin - checkout
- booking duration depends on checkin and checkout

### Step - 4:
#### Calculate stay_type
- stay type is depends on booking duration , as it says about time. if booking duration is high then long stay , low - short stay.
- Add column -> custom column -> formula
- *stay_type* =
- if [booking_duration] >= 6 then "long stay"
  else if [booking_duration] < 4 then "short stay"
  else "medium stay"

### Step - 5:
#### Calculate room_category
- room_category is calculated based on occupancy_rate feature.
- room_category are of 3 types : Standard, Premium , Deluxe
- Add column -> custom column -> formula
- *room_category* =
- if [occupancy_rate] >= 0.79 then "Deluxe"
  else if [occupancy_rate] <= 0.75 then "Standard"
  else "Premium"
- Detect data type

### Task 3 : Calculate booking duration, room category, and stay type is completed

### Step - 6:
#### Creating Data Dimension Table
- Duplicate Fact_Bookings Table
- Remove columns other than Date, Month, Season, Weekday, Holiday
- Remove duplicates by selecting all features.

### Step - 7:
#### Creating Room Dimension Table
- Duplicate Fact_Bookings Table
- Remove columns other than Occupancy_rate, ADR, room_category, Available_rooms
- remove duplicates of selecting all columns
- Add Index column and rename as room_id

### Step - 8:
#### Creating Customer Table
- Duplicate Fact_Bookings Table
- Remove columns other than Guest_type, Guest_country, Market_segment, Average_Review_Score
- Remove duplicates of selecting all columns
- Add Index column

### Step - 9:
#### Creating Hotel Branch
- The data is about only one hotel, so as per task, I have created 2 columns : Branch_id, Branch_name by "Enter data".

### Task-1 : Load booking, customer, and room data completed

### Step - 10:
#### Building star schema
- To build star schema,  we required a foreign key in fact table
- Primary key in Dimension table , i.e , common column must there.
- Therefore, we created room_id , customer_id, branch_id in fact_bookings table by applying merge queries.
- merging fact_bookings with room, cutomer, hotel branch table individualy
- close and apply
- click o model view, and connect the dimension table to fact table in one to many cardinality.

### Task-2 : Build a star schema with Date, Room, Customer, Hotel branches completed

### Visualizations ; Data Analysis
- Click on Report view
- I created card view of Total_Revenue, Profit , Bookings, ADR, Occupancy_rate.
- From this analysis, we clearly get the data that, how efficiently hotel used, easily calculate expenses and we get finacial knowledge.
- Line graph between Total revenue and days.
- we observe that 5th and 7th day , total_revenue is high and 3,18, 30 th days have low revenue
- page 2 : Pie chart is about how effieciently rooms are used
- page 3: Clustered column chart between Bookings and stay type
- I have shown 11 charts on Bookings vs stay type based on different features
- we can see that UK customers are not for long stay.
- In spring season, both long and medium stay approx. near to each other.
- Most of the features says that maximum are willing to stay for long
- page 4: Bar clustered chart between Total Revenue and Booking_channel
- I have shown 12 charts on Total Revenue vs Booking_channel based on different features
- Premium members most of them booked through OTA channel.
- OTA channel total revenue is 10M, and Direct channel - 7M
