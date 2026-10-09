# Power BI Implementation Guide

> **Status:** This is a build guide, not proof of a completed or tested report. The repository does not currently contain the source CSV or the editable PBIX/PBIP report. Row counts, visuals, relationships, refresh times, and KPI outputs below are specifications to verify with the actual data and model.

## Step-by-Step Instructions to Build the Ride-Booking Analytics Dashboard

This guide walks through building the complete Power BI project from scratch using the provided documentation.

---

## Prerequisites

- **Power BI Desktop** (current supported version)
- **Source Data:** `ncr_ride_bookings.csv` (150,000 rows)
- **Estimated Time:** depends on source-data cleanup, model design, and visual validation
- **Skill Level:** Intermediate Power BI user

---

## PHASE 1: DATA IMPORT & TRANSFORMATION (45 minutes)

### Step 1: Import Source CSV
1. Open Power BI Desktop
2. Home → Get Data → Text/CSV
3. Navigate to `ncr_ride_bookings.csv`
4. Click Transform Data (opens Power Query Editor)

### Step 2: Apply Power Query Transformations
**Follow the complete transformation guide in:**
`PowerQuery/Transformation_Guide.md`

**Key transformations:**
- Remove triple quotes from Booking ID and Customer ID
- Convert Date to date type
- Convert Time to time type
- Extract HourOfDay and create TimeBucket
- Add DateID for relationships
- Trim all text columns
- Create status flags (IsCompleted, IsCancelled)

**Expected result:** a cleaned fact query, after confirming the source schema and validating each transformation

### Step 3: Create Dimension Tables
**Create six distinct dimension queries in Power Query. The row counts below are expected values from prior notes, not validated counts:**

1. **DimDate** (366 rows) — Full 2024 calendar
2. **DimVehicle** (7 rows) — Vehicle types with IsShared flag
3. **DimPaymentMethod** (6 rows) — Payment methods with IsDigital flag
4. **DimBookingStatus** (5 rows) — Status with IsSuccess and CancellationType
5. **DimLocation** (176 rows) — Unique pickup/drop locations
6. **DimCustomer** (148,788 rows) — Unique customer IDs

**Follow specifications in:** `PowerQuery/Transformation_Guide.md` (Phase 7)

### Step 4: Add Foreign Keys to Fact Table
**Merge fact table with each dimension to add foreign key columns:**
- VehicleTypeID
- PaymentMethodID
- BookingStatusID
- PickupLocationID
- DropLocationID

**Follow merge steps in:** `PowerQuery/Transformation_Guide.md` (Phase 8)

### Step 5: Close & Apply
- Click "Close & Apply" in Power Query Editor
- Wait for the refresh to finish; refresh duration depends on the machine, source, and applied transformations

---

## PHASE 2: DATA MODEL SETUP (30 minutes)

### Step 6: Create Relationships
**Switch to Model View** (left sidebar icon)

**Create seven relationships across six distinct dimensions. Confirm key uniqueness and referential integrity before relying on these relationships:**

| From (Dimension) | To (Fact) | Type | Direction | Status |
|-----------------|-----------|------|-----------|--------|
| DimDate[DateID] | FactBookings[DateID] | One-to-Many | Single | Active |
| DimVehicle[VehicleTypeID] | FactBookings[VehicleTypeID] | One-to-Many | Single | Active |
| DimPaymentMethod[PaymentMethodID] | FactBookings[PaymentMethodID] | One-to-Many | Single | Active |
| DimBookingStatus[BookingStatusID] | FactBookings[BookingStatusID] | One-to-Many | Single | Active |
| DimLocation[LocationID] | FactBookings[PickupLocationID] | One-to-Many | Single | Active |
| DimLocation[LocationID] | FactBookings[DropLocationID] | One-to-Many | Single | **Inactive** |
| DimCustomer[CustomerID] | FactBookings[CustomerID] | One-to-Many | Single | Active |

**How to create:**
- Drag from dimension key to fact foreign key
- Confirm cardinality is "One-to-Many (*)"
- Set DropLocationID relationship as **Inactive** (right-click → Make Inactive)

### Step 7: Mark Date Table
1. Select DimDate table
2. Table Tools → Mark as Date Table
3. Select "FullDate" as date column
4. Click OK

### Step 8: Verify Model
**Checklist:**
- [ ] All 7 documented relationships created and validated
- [ ] No circular dependencies
- [ ] DimLocation has 2 relationships (one active, one inactive)
- [ ] DimDate marked as date table
- [ ] No errors in Relationships view

---

## PHASE 3: DAX MEASURES (30 minutes)

### Step 9: Create Measure Groups
**Create folders to organize measures:**
1. Right-click FactBookings table
2. New Group → "Booking Volume"
3. Repeat for: "Performance Rates", "Revenue", "Operations", "Customer"

### Step 10: Add Core Measures
**Copy-paste formulas from:** `Documentation/DAX_Measures.md`

**Priority measures (create these first):**

**Booking Volume:**
```dax
[Total Bookings] = COUNTA(FactBookings[BookingID])
[Completed Bookings] = CALCULATE([Total Bookings], DimBookingStatus[IsSuccess] = TRUE)
[Cancelled Bookings] = CALCULATE([Total Bookings], DimBookingStatus[CancellationType] IN {"Customer", "Driver"})
[Unique Customers] = DISTINCTCOUNT(FactBookings[CustomerID])
```

**Performance Rates:**
```dax
[Completion Rate %] = DIVIDE([Completed Bookings], [Total Bookings], 0)
[Cancellation Rate %] = DIVIDE([Cancelled Bookings], [Total Bookings], 0)
```

**Revenue:**
```dax
[Total Booking Value] = SUM(FactBookings[BookingValue])
[Completed Revenue] = CALCULATE([Total Booking Value], DimBookingStatus[IsSuccess] = TRUE)
[Revenue per KM] = DIVIDE([Completed Revenue], [Total Completed Distance], 0)
```

**Operations:**
```dax
[Average VTAT] = CALCULATE(AVERAGE(FactBookings[AvgVTAT]), DimBookingStatus[IsSuccess] = TRUE)
[Average Ride Distance] = CALCULATE(AVERAGE(FactBookings[RideDistance]), DimBookingStatus[IsSuccess] = TRUE)
[Average Driver Rating] = AVERAGE(FactBookings[DriverRating])
```

**Total: 20+ measures** (full list in DAX_Measures.md)

### Step 11: Format Measures
**Apply number formatting:**
- Counts: `#,##0`
- Percentages: `0.0%`
- Currency: `$#,##0`
- Ratings: `0.0`
- Distance: `0.0 "km"`

**How to format:**
1. Select measure in Fields pane
2. Measure Tools → Format
3. Choose format type and pattern

### Step 12: Test Measures
**Create a test table visual:**
- Add [Total Bookings], [Completed Bookings], [Completion Rate %]
- Verify values make sense (62% completion rate expected)
- Add Vehicle Type to rows → verify filtering works

---

## PHASE 4: DASHBOARD PAGES (60 minutes)

### Step 13: Create Page Structure
**Create 4 report pages:**
1. Page 1: Executive Overview
2. Page 2: Operations & Performance
3. Page 3: Cancellations & Service Quality
4. Page 4: Revenue & Customer Analytics

**Set page size:** View → Page Settings → 16:9 (1280 x 720)

---

### PAGE 1: EXECUTIVE OVERVIEW

**Layout:**
```
┌─────────────────────────────────────────────┐
│ KPI Cards (3 x 2 grid)                      │
│ [Total Bookings] [Completion %] [Revenue]   │
│ [Cancellation %] [Avg Value] [Customers]    │
├──────────────────┬──────────────────────────┤
│ Monthly Booking  │  Booking Status         │
│ Trend (Line)     │  Distribution (Donut)   │
├──────────────────┴──────────────────────────┤
│ Revenue by Vehicle Type (Horizontal Bar)    │
└─────────────────────────────────────────────┘
```

**Add visuals:**

1. **KPI Cards (6 total):**
   - Insert → Card visual
   - Add measures: [Total Bookings], [Completion Rate %], [Cancellation Rate %], [Completed Revenue], [Average Booking Value], [Unique Customers]
   - Format: Large font (24-28pt), bold values

2. **Monthly Booking Trend:**
   - Insert → Line chart
   - X-axis: DimDate[MonthName] or DimDate[FullDate]
   - Y-axis: [Total Bookings]
   - Line color: #2196F3 (blue)

3. **Booking Status Distribution:**
   - Insert → Donut chart
   - Legend: DimBookingStatus[BookingStatus]
   - Values: [Total Bookings]
   - Colors: Completed=#4CAF50, Cancelled=#FF5252, Other=#FFC107

4. **Revenue by Vehicle Type:**
   - Insert → Horizontal Bar chart
   - Y-axis: DimVehicle[VehicleType]
   - X-axis: [Completed Revenue]
   - Sort descending by value
   - Color: #2196F3

**Add Slicers:**
- Date range slicer (DimDate[FullDate])
- Vehicle Type slicer (DimVehicle[VehicleType])
- Payment Method slicer (DimPaymentMethod[PaymentMethod])

---

### PAGE 2: OPERATIONS & PERFORMANCE

**Add these visualizations:**

1. **Bookings by Hour of Day:**
   - Column chart
   - X-axis: FactBookings[HourOfDay]
   - Y-axis: [Total Bookings]
   - Shows peak hours (8-11 AM, 6-9 PM)

2. **Bookings by Day of Week:**
   - Column chart
   - X-axis: DimDate[DayName]
   - Y-axis: [Total Bookings]

3. **Completion Rate by Vehicle Type:**
   - Combo chart (bars + line)
   - X-axis: DimVehicle[VehicleType]
   - Column: [Completed Bookings]
   - Line: [Completion Rate %]

4. **Average VTAT by Hour:**
   - Line chart
   - X-axis: FactBookings[HourOfDay]
   - Y-axis: [Average VTAT]
   - Optional: Add reference line at 300 seconds (5-min target)

5. **Average CTAT by Hour:**
   - Line chart
   - X-axis: FactBookings[HourOfDay]
   - Y-axis: [Average CTAT]

6. **Ride Distance Distribution:**
   - Histogram or Column chart
   - Create bins: 0-2, 2-5, 5-10, 10-20, 20+ km
   - X-axis: Distance bins
   - Y-axis: Count of rides

**Add Slicers:**
- Date range
- Vehicle Type

---

### PAGE 3: CANCELLATIONS & SERVICE QUALITY

**Add these visualizations:**

1. **Customer Cancellation Card:**
   - Card visual with [Cancelled by Customer]
   - Add sparkline (trend over time)

2. **Driver Cancellation Card:**
   - Card visual with [Cancelled by Driver]
   - Add sparkline

3. **Cancellation Rate Trend:**
   - Line chart with 2 series
   - X-axis: DimDate[FullDate]
   - Lines: [Customer Cancellation Rate %] (red), [Driver Cancellation Rate %] (orange)

4. **Top Customer Cancellation Reasons:**
   - Horizontal bar chart
   - Y-axis: FactBookings[Reason for cancelling by Customer]
   - X-axis: Count
   - Filter: Top 8 reasons

5. **Top Driver Cancellation Reasons:**
   - Horizontal bar chart
   - Y-axis: FactBookings[Driver Cancellation Reason]
   - X-axis: Count
   - Filter: Top 8 reasons

6. **No Driver Found Rate by Vehicle Type:**
   - Bar chart
   - Y-axis: DimVehicle[VehicleType]
   - X-axis: [No Driver Rate %]
   - Sort descending

7. **Driver Rating Distribution:**
   - Histogram
   - Bins: 5.0, 4.5-4.9, 4.0-4.4, 3.5-3.9, <3.5
   - Color gradient: Green → Yellow → Red

8. **Customer Rating Distribution:**
   - Histogram (same structure as driver ratings)

**Add Slicers:**
- Date range
- Vehicle Type

---

### PAGE 4: REVENUE & CUSTOMER ANALYTICS

**Add these visualizations:**

1. **Revenue by Vehicle Type:**
   - Horizontal bar chart
   - Y-axis: DimVehicle[VehicleType]
   - X-axis: [Completed Revenue]
   - Sort descending

2. **Revenue by Payment Method:**
   - Pie chart
   - Legend: DimPaymentMethod[PaymentMethod]
   - Values: [Completed Revenue]
   - Color palette: 5 colors from design system

3. **Booking Value vs Ride Distance:**
   - Scatter plot
   - X-axis: FactBookings[RideDistance] (0-50 km)
   - Y-axis: FactBookings[BookingValue] ($0-$150)
   - Optional: Add linear regression trend line

4. **Revenue Trend by Month:**
   - Line chart with 3 series
   - X-axis: DimDate[MonthName]
   - Lines: [Completed Revenue] (solid blue), Target (dashed green), Average (dotted gray)

5. **Average Booking Value by Time of Day:**
   - Column chart
   - X-axis: FactBookings[HourOfDay]
   - Y-axis: [Average Completed Value]

6. **Revenue per KM Card:**
   - Card visual with [Revenue per KM]
   - Add trend indicator (vs previous month)

7. **Bookings per Customer Distribution:**
   - Histogram
   - Bins: 1-5, 6-10, 11-20, 21-50, 51+
   - X-axis: Booking count bins
   - Y-axis: Customer count

**Add Slicers:**
- Date range
- Vehicle Type
- Payment Method

---

## PHASE 5: DESIGN & FORMATTING (30 minutes)

### Step 14: Apply Color Scheme
**Standard palette:**
- Success (Completed): #4CAF50
- Alert (Cancelled): #FF5252
- Info (Metrics): #2196F3
- Warning (Incomplete): #FFC107
- Neutral (Secondary): #757575

**How to apply:**
1. Select visual
2. Format pane → Data colors
3. Manually set colors for each category

### Step 15: Format Text & Layout
**Typography:**
- Page titles: 20pt, bold
- Section headers: 16pt, semi-bold
- KPI labels: 11pt, regular
- KPI values: 14pt, bold

**Spacing:**
- Page margins: 20px all sides
- Gap between cards: 16px
- Consistent alignment (use snap-to-grid)

### Step 16: Configure Interactions
**For each page:**
1. Format → Edit Interactions
2. Set cross-highlighting (not filtering) for status charts
3. Allow slicers to filter all visuals
4. Test with sample date/vehicle selections

### Step 17: Add Navigation
**Create page navigation buttons (optional):**
1. Insert → Buttons → Blank
2. Add text: "Overview", "Operations", "Quality", "Revenue"
3. Button action → Page Navigation → Select target page
4. Copy buttons to all pages for consistent navigation

---

## PHASE 6: TESTING & VALIDATION (15 minutes)

### Step 18: Data Validation
**Verify calculations:**
- [ ] Total Bookings = 150,000
- [ ] Completion Rate ≈ 62%
- [ ] Cancelled + Completed + No Driver + Incomplete ≈ 100%
- [ ] Unique Customers ≈ 148,788
- [ ] All ratings in 0-5 range
- [ ] No negative booking values or distances

### Step 19: Slicer Testing
**Test filter cascades:**
- [ ] Date slicer affects all date-dependent visuals
- [ ] Vehicle Type slicer filters bookings correctly
- [ ] Payment Method slicer filters revenue measures
- [ ] All slicers can be cleared

### Step 20: Performance Check
**Verify responsiveness:**
- [ ] Page load time <3 seconds
- [ ] Slicer changes apply within 1 second
- [ ] No visuals show "Loading..." indefinitely
- [ ] File size <100 MB

---

## PHASE 7: SAVE & DOCUMENTATION

### Step 21: Save Project
**Save as:**
- File → Save As
- Name: `Ride_Booking_Analytics.pbix`
- Location: `PowerBI/` folder in repository

**Optional: Save as PBIP (Project Format):**
- File → Save As → Power BI Project (.pbip)
- Enables source control for report definition

### Step 22: Take Screenshots
**Capture each page:**
1. View → Full Screen
2. Screenshot tool (Windows: Win+Shift+S)
3. Save to `Screenshots/` folder:
   - 01_Executive_Overview.png
   - 02_Operations_Performance.png
   - 03_Cancellations_Quality.png
   - 04_Revenue_Analytics.png

### Step 23: Document Assumptions
**Create notes file if needed:**
- Any deviations from specifications
- Custom calculations added
- Known limitations or issues

---

## TROUBLESHOOTING

### Issue: Relationships Not Working
**Solution:**
- Verify data types match exactly (both Int64 or both String)
- Check for null values in foreign keys
- Confirm relationship cardinality is "One-to-Many"

### Issue: Measures Return Blank
**Solution:**
- Check filter context (status-specific measures need CALCULATE)
- Verify table references are correct
- Test with simple SUM/COUNT first

### Issue: DimLocation Inactive Relationship
**Solution:**
- Use USERELATIONSHIP() in measures for Drop location analysis
- Example: `CALCULATE([Total Bookings], USERELATIONSHIP(FactBookings[DropLocationID], DimLocation[LocationID]))`

### Issue: Slow Performance
**Solution:**
- Remove unnecessary calculated columns (use measures instead)
- Reduce visual complexity (fewer visuals per page)
- Disable auto-refresh on data connections

---

## NEXT STEPS

**Completion status:** Mark the dashboard complete only after the report file is saved, refreshed, and all validation checks pass.
- 4 professional pages
- 20+ DAX measures
- Star schema data model
- Evidence-based insights

✅ **Ready for:**
- Recruiter portfolio review
- Business stakeholder presentations
- Operational decision-making
- Further analysis enhancements

✅ **Optional Enhancements:**
- Add drill-through pages (detailed cancellation analysis)
- Implement row-level security (if multi-user)
- Create mobile-optimized layouts
- Add bookmarks for saved views
- Publish to Power BI Service (cloud)

---

## SUPPORT RESOURCES

- **Data Dictionary:** `Documentation/Data_Dictionary.md`
- **DAX Formulas:** `Documentation/DAX_Measures.md`
- **Power Query Steps:** `PowerQuery/Transformation_Guide.md`
- **Business Questions:** `Documentation/Business_Questions.md`
- **Key Insights:** `Documentation/Key_Insights.md`

---

**Implementation Guide Version:** 1.0  
**Last Updated:** October 8, 2024  
**Estimated Total Time:** 2-3 hours  
**Difficulty:** Intermediate  
**Prerequisites:** Power BI Desktop, Source CSV file
