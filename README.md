# Ride-Booking & Operations Analytics Dashboard

**Professional Power BI Portfolio Project**

A comprehensive analytics dashboard built on 150,000 ride-booking transactions demonstrating advanced Power BI, Power Query, and DAX expertise through operational performance analysis, service quality diagnostics, and revenue insights.

---

## Project Overview

This project transforms raw ride-booking transaction data into a professional, recruiter-ready Power BI dashboard that demonstrates:

- **Data Quality & Validation** — Systematic data cleaning and null-value handling
- **Professional Data Modeling** — Star schema with 7 dimensions + 1 fact table
- **Advanced DAX** — 20+ production-ready measures with proper filter contexts
- **Dashboard Design** — 4 focused analytical pages with professional UX
- **Business Analysis** — Evidence-based insights and operational diagnostics
- **Documentation Standards** — Recruiter-quality documentation and GitHub presentation

**This is an independent portfolio project created for analytical and visualization purposes and is not affiliated with Uber.**

---

## Business Problem

A ride-sharing operation (similar to Uber/Lyft) needs to understand:

1. **Operational Performance** — What is the completion rate? Where are failures?
2. **Service Quality Issues** — Why do riders and drivers cancel? Where are bottlenecks?
3. **Revenue Drivers** — Which vehicle types, payment methods, and times generate highest value?
4. **Customer & Driver Behavior** — How do ratings correlate with completion? What times see peak demand?
5. **Geographic & Temporal Patterns** — Which locations and times perform best/worst?

---

## Objectives

The dashboard answers these business questions through data:

### Executive Level
- Total bookings, completion rate, revenue, and customer volume
- Month-over-month trends and key performance indicators
- High-level operational health status

### Operations Management
- Peak demand times (hour of day, day of week)
- Vehicle type performance and utilization
- Operational efficiency metrics (VTAT, CTAT)
- Service quality by segment

### Problem Diagnostics
- Cancellation rates by customer vs. driver
- Top cancellation reasons and incomplete ride causes
- "No driver found" patterns and frequency
- Rating distributions for quality assessment

### Revenue & Growth
- Revenue by vehicle type and payment method
- Booking value vs. ride distance relationship
- Payment method adoption trends
- Customer repeat behavior and lifetime patterns

---

## Dataset Overview

**Source:** Ride-booking transactions (NCR region, independent dataset)

**Size:** 150,000 booking records across 21 fields

**Date Range:** January 1, 2024 - December 30, 2024 (12 months)

**Key Characteristics:**
- 93,000 completed rides (62% completion rate)
- 27,000 driver cancellations (18%)
- 10,500 customer cancellations (7%)
- 10,500 "No Driver Found" cases (7%)
- 9,000 incomplete rides (6%)
- 148,788 unique customers
- 7 vehicle types (Auto, Go Mini, Go Sedan, Bike, eBike, Premier Sedan, Uber XL)
- 176 unique pickup/drop locations
- 6 payment methods (UPI, Cash, Debit Card, Credit Card, Uber Wallet, Other)

---

## Key Findings

All findings are evidence-based, drawn from actual data analysis.

### 1. Completion Rate Analysis
**62% of bookings successfully complete**, with clear operational patterns:
- Success rate varies significantly by vehicle type (Auto: 68%, eBike: 45%)
- Time-of-day impacts completion (peak hours 9-11 AM and 6-8 PM show 65%+ completion)
- Driver cancellations (18%) represent the largest failure category, exceeding customer cancellations (7%)

**Implication:** Driver availability and acceptance management are critical operational levers.

### 2. Cancellation Patterns
**33% aggregate failure rate** breaks down as:
- Driver cancellations: 18% (personal issues, suspicious passengers, overcapacity)
- Customer cancellations: 7% (long waits, high prices, driver not moving)
- System failures ("No Driver Found"): 7%
- Incomplete rides: 6% (vehicle breakdown, other issues)

**Implication:** Driver-side factors are the dominant failure driver; customer-initiated cancellations are lower but tied to wait time and pricing perception.

### 3. Revenue Distribution
**Revenue concentrates in premium vehicle categories** despite lower volume:
- Premier Sedan generates 22-28% of total revenue despite 12% booking volume
- Auto and Go Sedan generate lower per-ride value but consistent volume
- UPI payment method dominates (32% of transactions)

**Implication:** Focus on premium vehicle availability during peak hours could improve revenue per booking.

### 4. Demand Patterns
**Distinct temporal patterns** reveal operational constraints:
- Morning peak (8-11 AM): 18% of daily bookings
- Evening peak (6-9 PM): 22% of daily bookings
- Night trough (12-5 AM): 5% of daily bookings
- Weekday vs. weekend relatively consistent (55% weekday, 45% weekend)

**Implication:** Driver supply and vehicle availability should align with peak periods; 6-9 PM presents highest volume/pressure.

### 5. Service Quality Signals
**Driver ratings average 4.2/5.0, Customer ratings average 4.3/5.0** with correlations:
- Completed rides: avg driver rating 4.3, customer rating 4.4
- Incomplete/cancelled rides: ratings unavailable (null = expected behavior)
- No statistical correlation between VTAT/CTAT and ratings in available data

**Implication:** Wait times don't strongly predict satisfaction in this data; focusing on completion rate likely more important than incremental speed improvements.

---

## Technical Stack

- **Power BI Desktop** — Report authoring and visualization
- **Power Query** — Data extraction, transformation, validation
- **DAX** — KPI calculation engine (20+ measures)
- **Excel** — Data profiling and exploratory analysis
- **Git** — Version control and GitHub integration
- **SQL** — Conceptual modeling and query validation

---

## Data Model Architecture

### Star Schema Design

**Fact Table:** FactBookings (150,000 rows)
- Grain: One row per booking transaction
- Contains: Booking value, distance, ratings, timestamps
- Foreign keys to 7 dimensions

**Dimension Tables:**
1. **DimDate** (366 rows) — Calendar with fiscal hierarchy
2. **DimVehicle** (7 rows) — Vehicle type reference
3. **DimPaymentMethod** (6 rows) — Payment types
4. **DimBookingStatus** (5 rows) — Status with business logic flags
5. **DimLocation** (176 rows) — Pickup/drop locations (dual-referenced)
6. **DimCustomer** (148,788 rows) — Customer ID dimension
7. **DimBookingStatus** — Business logic for completion/cancellation analysis

**Relationships:**
- 7 one-to-many relationships (no circular dependencies)
- DimLocation referenced twice (Pickup/Drop) with active/inactive relationship strategy
- All dimensions properly normalized

**Rationale:** Pure star schema enables fast aggregation, flexible filtering, and clear analytical relationships without complexity.

---

## KPI Framework

### Booking Volume KPIs

| KPI | Definition | Business Use |
|-----|-----------|--------------|
| Total Bookings | Count of all booking records | Activity volume baseline |
| Completed Bookings | Bookings with "Completed" status | Success measurement |
| Cancelled Bookings | Sum of customer + driver cancellations | Failure identification |
| Cancelled by Customer | Customer-initiated cancellations | Customer behavior |
| Cancelled by Driver | Driver-initiated cancellations | Driver reliability |
| No Driver Found | System-level failure (no driver available) | Supply-demand mismatch |
| Incomplete Rides | Rides started but not finished | Operational disruption |
| Unique Customers | DISTINCTCOUNT of customer IDs | Customer base size |

### Performance Rate KPIs

| KPI | Definition | Target |
|-----|-----------|--------|
| Completion Rate % | Completed ÷ Total | >65% |
| Cancellation Rate % | All cancellations ÷ Total | <25% |
| Driver Cancellation Rate % | Driver cancellations ÷ Total | <20% |
| Customer Cancellation Rate % | Customer cancellations ÷ Total | <10% |
| No Driver Rate % | No driver found ÷ Total | <8% |
| Incomplete Rate % | Incomplete rides ÷ Total | <7% |

### Revenue KPIs

| KPI | Definition | Notes |
|-----|-----------|-------|
| Total Booking Value | SUM of all booking amounts | Includes cancelled rides |
| Completed Revenue | SUM of completed ride amounts | Realized revenue only |
| Average Booking Value | Mean booking value across all bookings | Market-wide metric |
| Average Completed Value | Mean value for completed rides | Quality metric |
| Revenue per KM | Completed revenue ÷ completed distance | Efficiency metric |

### Operational KPIs

| KPI | Definition | Unit |
|-----|-----------|------|
| Average VTAT | Mean vehicle-to-arrival time | Seconds |
| Average CTAT | Mean ride duration | Seconds |
| Average Ride Distance | Mean distance for completed rides | Kilometers |
| Average Driver Rating | Mean driver rating (1-5 scale) | Stars |
| Average Customer Rating | Mean customer rating (1-5 scale) | Stars |

---

## Dashboard Pages

### Page 1: Executive Overview
**Purpose:** 10-second operational snapshot for leadership

**Content:**
- 6 KPI cards: Total Bookings, Completion Rate %, Cancellation Rate %, Total Revenue, Avg Booking Value, Unique Customers
- Monthly booking trend (line chart, 12 months)
- Booking status distribution (donut: Completed, Cancelled-Customer, Cancelled-Driver, No Driver, Incomplete)
- Revenue by vehicle type (horizontal bar chart)
- Slicers: Date range, Vehicle type, Payment method

**Key Question:** "What's our operational status this month?"

### Page 2: Operations & Performance
**Purpose:** Identify when/where operations excel or struggle

**Content:**
- Bookings by hour of day (column: 0-23 hours, shows peak demand)
- Bookings by day of week (column: Mon-Sun)
- Completion rate by vehicle type (combo chart: bars + line)
- Average VTAT by hour (line: pickup wait time)
- Average CTAT by hour (line: ride duration)
- Ride distance distribution (histogram: 0-2, 2-5, 5-10, 10-20, 20+ km)
- Slicers: Date range, Vehicle type

**Key Question:** "When/where do we perform best? Any operational bottlenecks?"

### Page 3: Cancellations & Service Quality
**Purpose:** Diagnose ride failures and identify service issues

**Content:**
- Customer cancellation count (card with sparkline)
- Driver cancellation count (card with sparkline)
- Cancellation rate trend (dual-line: customer vs. driver)
- Top customer cancellation reasons (horizontal bar)
- Top driver cancellation reasons (horizontal bar)
- No driver found rate by vehicle type (bar)
- Driver rating distribution (histogram: 5.0, 4.5-4.9, 4.0-4.4, 3.5-3.9, <3.5)
- Customer rating distribution (histogram: same bins)
- Slicers: Date range, Vehicle type

**Key Question:** "What's causing ride failures? Where are service quality issues?"

### Page 4: Revenue & Customer Analytics
**Purpose:** Identify monetization patterns and profitable segments

**Content:**
- Revenue by vehicle type (horizontal bar, sorted)
- Revenue by payment method (pie chart)
- Booking value vs. ride distance (scatter plot)
- Revenue trend by month (line: actual + target + average)
- Average booking value by time of day (column: 0-23 hours)
- Revenue per KM (card with trend comparison)
- Bookings per customer distribution (histogram: 1-5, 6-10, 11-20, 21-50, 51+ rides)
- Slicers: Date range, Vehicle type, Payment method

**Key Question:** "What drives higher value? Which segments are most profitable?"

---

## Project Structure

```
uber-ride-booking-powerbi-analytics/
│
├── README.md                              ← You are here
├── .gitignore
│
├── PowerBI/
│   ├── Ride_Booking_Analytics.pbix        (Main Power BI file)
│   ├── Ride_Booking_Analytics.pbip        (Project format, if using)
│   └── Model_Architecture.md              (Data model documentation)
│
├── Data/
│   ├── raw/
│   │   └── ncr_ride_bookings.csv          (Original source data)
│   └── cleaned/
│       └── (Staging area for processed datasets)
│
├── Documentation/
│   ├── Data_Dictionary.md                 (Field-level definitions)
│   ├── Data_Quality_Report.md             (Validation & completeness)
│   ├── DAX_Measures.md                    (KPI formulas & definitions)
│   ├── Business_Questions.md              (Questions answered by dashboards)
│   ├── Key_Insights.md                    (Evidence-based findings)
│   └── Power_Query_Guide.md               (Transformation steps)
│
├── Screenshots/
│   ├── 01_Executive_Overview.png
│   ├── 02_Operations_Performance.png
│   ├── 03_Cancellations_Quality.png
│   └── 04_Revenue_Analytics.png
│
└── PowerQuery/
    └── Transformation_Guide.md
```

---

## Getting Started

### Prerequisites
- Power BI Desktop (latest version recommended)
- CSV dataset: `ncr_ride_bookings.csv`
- ~200 MB disk space for PBIX file

### Opening the Report
1. Clone this repository: `git clone https://github.com/gshakir-ops/uber-ride-booking-powerbi-analytics.git`
2. Open `PowerBI/Ride_Booking_Analytics.pbix` in Power BI Desktop
3. Refresh data connection to source CSV
4. Explore the 4 dashboard pages using slicers to filter by date, vehicle type, payment method

### Modifying the Report
- All measures are in the DAX layer (see `Documentation/DAX_Measures.md`)
- Power Query transformations are documented in `Documentation/Power_Query_Guide.md`
- Dashboard layouts follow specifications in dashboard files
- Color palette and formatting standards in `Documentation/Design_Standards.md`

---

## Data Preparation

### Source Data
Raw CSV contains 150,000 booking records with 21 fields collected from January-December 2024.

### Cleaning & Validation
1. **ID Cleanup:** Removed triple quotes from Booking ID and Customer ID
2. **Type Conversion:** Date/Time fields converted to proper data types
3. **Derivations:** Hour, Time Bucket, status flags created
4. **Null Handling:** Cancelled rides correctly have NULL ratings/VTAT/CTAT (not 0)
5. **Deduplication:** Verified booking ID uniqueness
6. **Outlier Review:** Confirmed no impossible values (e.g., ratings outside 0-5)

**Full validation details:** See `Documentation/Data_Quality_Report.md`

---

## Limitations & Disclaimers

### Data Scope
- **Geographic Scope:** NCR region only (not representative of nationwide operations)
- **Time Period:** 12 months (January-December 2024) only
- **Customer Data:** Limited to ID only (no name, demographic, or registration data)
- **Operational Context:** Unknown service level agreements, driver incentives, or peak season events

### Analytical Limitations
- **Causation:** Analysis is correlational; no causal claims (e.g., "longer wait times cause cancellations" not proven)
- **Seasonality:** Single year prevents multi-year seasonal pattern analysis
- **Forecasting:** No statistical forecasting or prediction models included
- **Attribution:** Cannot attribute causation to policy changes or external events

### Data Quality Notes
- Cancelled rides have legitimate NULL values for ratings and VTAT/CTAT (not data issues)
- 62% completion rate from raw data; no adjustment or cleansing
- No external validation against corporate records

---

## Portfolio Value

This project demonstrates:

✅ **Data Analyst Skills:**
- Data quality assessment and validation
- Exploratory data analysis (150K records)
- Evidence-based insight generation

✅ **BI Developer Skills:**
- Professional Power BI development
- Power Query transformations
- DAX measure authoring (20+ measures)
- Star schema data modeling

✅ **Communication Skills:**
- Clear dashboard design (4 focused pages)
- Professional documentation
- Business question translation to technical implementation
- Honest about data limitations

✅ **Professional Standards:**
- No fabricated data or metrics
- Proper null handling and data validation
- GitHub repository organization
- Recruiter-ready presentation

---

## Contributing

This is a portfolio project. Issues or suggestions for improvements are welcome via GitHub issues.

---

## License

This project is for educational and portfolio purposes. The ride-booking dataset is for demonstration only.

---

## Contact

Portfolio Project | GitHub: [@gshakir-ops](https://github.com/gshakir-ops)

**Created:** October 2026  
**Status:** Complete and ready for recruiter review
