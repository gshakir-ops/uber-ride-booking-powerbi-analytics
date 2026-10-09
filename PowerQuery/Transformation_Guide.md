# Power Query Transformation Guide

> **Validation status:** These are proposed M transformations, not an executed ETL pipeline. The source CSV is not in this repository. Confirm actual column names, types, quoting, null patterns, category labels, and row counts before using this code or publishing any result.

## Complete ETL Process for Ride-Booking Analytics

This document provides step-by-step Power Query (M code) transformations to clean and prepare the ride-booking dataset for analysis.

---

## Overview

**Source:** `ncr_ride_bookings.csv` (prior notes describe approximately 150,000 rows and 21 columns; not verified here)  
**Target:** Clean fact table + dimension tables in star schema  
**Tool:** Power BI Power Query Editor

---

## PHASE 1: LOAD SOURCE DATA

### Step 1.1: Import CSV
```m
let
    Source = Csv.Document(
        File.Contents("C:\\path\\to\\ncr_ride_bookings.csv"),
        [Delimiter=",", Columns=21, Encoding=65001, QuoteStyle=QuoteStyle.Csv]
    ),
    PromotedHeaders = Table.PromoteHeaders(Source, [PromoteAllScalars=true])
in
    PromotedHeaders
```

**Validation:** Compare the loaded row count to the actual source file and record the result; 150,000 is an unverified figure from prior notes.

---

## PHASE 2: DATA TYPE CONVERSIONS

### Step 2.1: Convert Date Field
```m
#"Changed Date Type" = Table.TransformColumnTypes(
    PromotedHeaders,
    {{"Date", type date}}
)
```
**Before:** Text "2024-03-23"  
**After:** Date 2024-03-23

---

### Step 2.2: Convert Time Field
```m
#"Changed Time Type" = Table.TransformColumnTypes(
    #"Changed Date Type",
    {{"Time", type time}}
)
```
**Before:** Text "12:29:38"  
**After:** Time 12:29:38

---

### Step 2.3: Convert Numeric Fields
```m
#"Changed Numeric Types" = Table.TransformColumnTypes(
    #"Changed Time Type",
    {
        {"Avg VTAT", type number},
        {"Avg CTAT", type number},
        {"Booking Value", type number},
        {"Ride Distance", type number},
        {"Driver Ratings", type number},
        {"Customer Rating", type number},
        {"Cancelled Rides by Customer", Int64.Type},
        {"Cancelled Rides by Driver", Int64.Type},
        {"Incomplete Rides", Int64.Type}
    }
)
```

---

## PHASE 3: ID FIELD CLEANUP

### Step 3.1: Remove Triple Quotes from Booking ID
```m
#"Cleaned Booking ID" = Table.TransformColumns(
    #"Changed Numeric Types",
    {{"Booking ID", each Text.Replace(Text.Replace(_, """", ""), """", ""), type text}}
)
```
**Before:** `"""CNR5884300"""`  
**After:** `CNR5884300`

---

### Step 3.2: Remove Triple Quotes from Customer ID
```m
#"Cleaned Customer ID" = Table.TransformColumns(
    #"Cleaned Booking ID",
    {{"Customer ID", each Text.Replace(Text.Replace(_, """", ""), """", ""), type text}}
)
```
**Before:** `"""CID1982111"""`  
**After:** `CID1982111`

---

## PHASE 4: DERIVED COLUMNS

### Step 4.1: Extract Hour from Time
```m
#"Added Hour" = Table.AddColumn(
    #"Cleaned Customer ID",
    "HourOfDay",
    each Time.Hour([Time]),
    Int64.Type
)
```
**Result:** New column HourOfDay (0-23)

---

### Step 4.2: Create Time Bucket
```m
#"Added Time Bucket" = Table.AddColumn(
    #"Added Hour",
    "TimeBucket",
    each if [HourOfDay] >= 0 and [HourOfDay] <= 5 then "Early Morning"
         else if [HourOfDay] >= 6 and [HourOfDay] <= 11 then "Morning"
         else if [HourOfDay] >= 12 and [HourOfDay] <= 17 then "Afternoon"
         else if [HourOfDay] >= 18 and [HourOfDay] <= 21 then "Evening"
         else "Night",
    type text
)
```
**Mapping:**
- 00:00-05:59 → "Early Morning"
- 06:00-11:59 → "Morning"
- 12:00-17:59 → "Afternoon"
- 18:00-21:59 → "Evening"
- 22:00-23:59 → "Night"

---

### Step 4.3: Create DateID for Relationship
```m
#"Added DateID" = Table.AddColumn(
    #"Added Time Bucket",
    "DateID",
    each Date.Year([Date]) * 10000 + Date.Month([Date]) * 100 + Date.Day([Date]),
    Int64.Type
)
```
**Example:** 2024-03-23 → 20240323

---

### Step 4.4: Create Status Flags
```m
#"Added IsCompleted" = Table.AddColumn(
    #"Added DateID",
    "IsCompleted",
    each if [Booking Status] = "Completed" then 1 else 0,
    Int64.Type
),

#"Added IsCancelled" = Table.AddColumn(
    #"Added IsCompleted",
    "IsCancelled",
    each if [#"Cancelled Rides by Customer"] = 1 or [#"Cancelled Rides by Driver"] = 1 then 1 else 0,
    Int64.Type
)
```

---

## PHASE 5: TRIM & STANDARDIZE TEXT

### Step 5.1: Trim All Text Columns
```m
#"Trimmed Text" = Table.TransformColumns(
    #"Added IsCancelled",
    {
        {"Booking Status", Text.Trim, type text},
        {"Vehicle Type", Text.Trim, type text},
        {"Pickup Location", Text.Trim, type text},
        {"Drop Location", Text.Trim, type text},
        {"Payment Method", Text.Trim, type text}
    }
)
```
**Purpose:** Remove leading/trailing whitespace

---

### Step 5.2: Standardize Categorical Values (Optional)
```m
#"Standardized Status" = Table.ReplaceValue(
    #"Trimmed Text",
    "cancelled by customer",
    "Cancelled by Customer",
    Replacer.ReplaceText,
    {"Booking Status"}
)
```
**Note:** Apply only if inconsistent capitalization detected

---

## PHASE 6: NULL VALIDATION

### Step 6.1: Validate Null Pattern
**DO NOT replace nulls with 0 for:**
- Avg VTAT (null = ride never started)
- Avg CTAT (null = ride never started)
- Driver Ratings (null = no rating given)
- Customer Rating (null = no rating given)
- Booking Value (null = no charge for some cancelled rides)

**Power Query handles nulls correctly — leave them as-is.**

---

## PHASE 7: CREATE DIMENSION TABLES

### DimDate (Separate Query)
```m
let
    StartDate = #date(2024, 1, 1),
    EndDate = #date(2024, 12, 31),
    DayCount = Duration.Days(EndDate - StartDate) + 1,
    DateList = List.Dates(StartDate, DayCount, #duration(1,0,0,0)),
    DateTable = Table.FromList(DateList, Splitter.SplitByNothing(), {"FullDate"}),
    
    #"Changed Type" = Table.TransformColumnTypes(DateTable, {{"FullDate", type date}}),
    
    #"Added DateID" = Table.AddColumn(#"Changed Type", "DateID", 
        each Date.Year([FullDate]) * 10000 + Date.Month([FullDate]) * 100 + Date.Day([FullDate]), Int64.Type),
    
    #"Added Year" = Table.AddColumn(#"Added DateID", "Year", each Date.Year([FullDate]), Int64.Type),
    #"Added Quarter" = Table.AddColumn(#"Added Year", "Quarter", each Date.QuarterOfYear([FullDate]), Int64.Type),
    #"Added Month" = Table.AddColumn(#"Added Quarter", "Month", each Date.Month([FullDate]), Int64.Type),
    #"Added MonthName" = Table.AddColumn(#"Added Month", "MonthName", each Date.MonthName([FullDate]), type text),
    #"Added Day" = Table.AddColumn(#"Added MonthName", "Day", each Date.Day([FullDate]), Int64.Type),
    #"Added DayOfWeek" = Table.AddColumn(#"Added Day", "DayOfWeek", each Date.DayOfWeek([FullDate], Day.Monday) + 1, Int64.Type),
    #"Added DayName" = Table.AddColumn(#"Added DayOfWeek", "DayName", each Date.DayOfWeekName([FullDate]), type text),
    #"Added WeekNumber" = Table.AddColumn(#"Added DayName", "WeekNumber", each Date.WeekOfYear([FullDate]), Int64.Type),
    #"Added IsWeekend" = Table.AddColumn(#"Added WeekNumber", "IsWeekend", 
        each if [DayOfWeek] = 6 or [DayOfWeek] = 7 then true else false, type logical)
in
    #"Added IsWeekend"
```
**Expected result:** 366 rows for a full 2024 calendar. Confirm this date range matches the source before using it.

**Mark as Date Table in Power BI:**
1. Select DimDate table
2. Table Tools → Mark as Date Table
3. Select "FullDate" as date column

---

### DimVehicle (Separate Query)
```m
let
    Source = Table.Distinct(
        Table.SelectColumns(FactBookings, {"Vehicle Type"})
    ),
    #"Added Index" = Table.AddIndexColumn(Source, "VehicleTypeID", 1, 1),
    #"Renamed Column" = Table.RenameColumns(#"Added Index", {{"Vehicle Type", "VehicleType"}}),
    #"Added IsShared" = Table.AddColumn(#"Renamed Column", "IsShared",
        each if [VehicleType] = "Go Mini" or [VehicleType] = "Go Sedan" then true else false,
        type logical
    )
in
    #"Added IsShared"
```
**Expected result from prior notes:** 7 vehicle categories. Recalculate from the source; do not hardcode this count as a validated result.

---

### DimPaymentMethod (Separate Query)
```m
let
    Source = Table.Distinct(
        Table.SelectColumns(FactBookings, {"Payment Method"})
    ),
    #"Removed Blanks" = Table.SelectRows(Source, each [Payment Method] <> null and [Payment Method] <> ""),
    #"Added Index" = Table.AddIndexColumn(#"Removed Blanks", "PaymentMethodID", 1, 1),
    #"Renamed Column" = Table.RenameColumns(#"Added Index", {{"Payment Method", "PaymentMethod"}}),
    #"Added IsDigital" = Table.AddColumn(#"Renamed Column", "IsDigital",
        each if [PaymentMethod] = "Cash" then false else true,
        type logical
    )
in
    #"Added IsDigital"
```
**Expected result from prior notes:** 6 payment categories. Recalculate from the source; do not hardcode this count as a validated result.

---

### DimBookingStatus (Separate Query - Manual Creation)
```m
let
    Source = #table(
        type table [
            BookingStatusID = Int64.Type,
            BookingStatus = text,
            IsSuccess = logical,
            CancellationType = text,
            FailureCategory = text
        ],
        {
            {1, "Completed", true, "None", "Success"},
            {2, "Cancelled by Customer", false, "Customer", "Customer Cancellation"},
            {3, "Cancelled by Driver", false, "Driver", "Driver Cancellation"},
            {4, "No Driver Found", false, "System", "No Driver"},
            {5, "Incomplete", false, "None", "Incomplete"}
        }
    )
in
    Source
```
**Expected result:** 5 status categories only if the source has these exact mutually exclusive labels. Verify status values and mappings.

---

### DimLocation (Separate Query)
```m
let
    Pickup = Table.Distinct(Table.SelectColumns(FactBookings, {"Pickup Location"})),
    Drop = Table.Distinct(Table.SelectColumns(FactBookings, {"Drop Location"})),
    #"Renamed Pickup" = Table.RenameColumns(Pickup, {{"Pickup Location", "LocationName"}}),
    #"Renamed Drop" = Table.RenameColumns(Drop, {{"Drop Location", "LocationName"}}),
    Combined = Table.Combine({#"Renamed Pickup", #"Renamed Drop"}),
    Distinct = Table.Distinct(Combined),
    #"Added Index" = Table.AddIndexColumn(Distinct, "LocationID", 1, 1),
    #"Reordered Columns" = Table.ReorderColumns(#"Added Index", {"LocationID", "LocationName"})
in
    #"Reordered Columns"
```
**Expected result from prior notes:** 176 unique location names. Recalculate after normalising text and handling blanks.

---

### DimCustomer (Separate Query)
```m
let
    Source = Table.Distinct(
        Table.SelectColumns(FactBookings, {"Customer ID"})
    ),
    #"Renamed Column" = Table.RenameColumns(Source, {{"Customer ID", "CustomerID"}})
in
    #"Renamed Column"
```
**Expected result from prior notes:** 148,788 unique customer IDs. Recalculate from nonblank customer IDs.

---

## PHASE 8: CREATE FACT TABLE WITH FOREIGN KEYS

### Step 8.1: Merge FactBookings with DimVehicle
```m
#"Merged Vehicle" = Table.NestedJoin(
    #"Final Cleaned Data",
    {"Vehicle Type"},
    DimVehicle,
    {"VehicleType"},
    "DimVehicle",
    JoinKind.Inner
),
#"Expanded Vehicle" = Table.ExpandTableColumn(
    #"Merged Vehicle",
    "DimVehicle",
    {"VehicleTypeID"},
    {"VehicleTypeID"}
)
```

### Step 8.2: Merge with DimPaymentMethod
```m
#"Merged Payment" = Table.NestedJoin(
    #"Expanded Vehicle",
    {"Payment Method"},
    DimPaymentMethod,
    {"PaymentMethod"},
    "DimPaymentMethod",
    JoinKind.LeftOuter
),
#"Expanded Payment" = Table.ExpandTableColumn(
    #"Merged Payment",
    "DimPaymentMethod",
    {"PaymentMethodID"},
    {"PaymentMethodID"}
)
```

### Step 8.3: Merge with DimBookingStatus
```m
#"Merged Status" = Table.NestedJoin(
    #"Expanded Payment",
    {"Booking Status"},
    DimBookingStatus,
    {"BookingStatus"},
    "DimBookingStatus",
    JoinKind.Inner
),
#"Expanded Status" = Table.ExpandTableColumn(
    #"Merged Status",
    "DimBookingStatus",
    {"BookingStatusID"},
    {"BookingStatusID"}
)
```

### Step 8.4: Merge with DimLocation (Pickup)
```m
#"Merged Pickup Location" = Table.NestedJoin(
    #"Expanded Status",
    {"Pickup Location"},
    DimLocation,
    {"LocationName"},
    "PickupLoc",
    JoinKind.Inner
),
#"Expanded Pickup" = Table.ExpandTableColumn(
    #"Merged Pickup Location",
    "PickupLoc",
    {"LocationID"},
    {"PickupLocationID"}
)
```

### Step 8.5: Merge with DimLocation (Drop)
```m
#"Merged Drop Location" = Table.NestedJoin(
    #"Expanded Pickup",
    {"Drop Location"},
    DimLocation,
    {"LocationName"},
    "DropLoc",
    JoinKind.Inner
),
#"Expanded Drop" = Table.ExpandTableColumn(
    #"Merged Drop Location",
    "DropLoc",
    {"LocationID"},
    {"DropLocationID"}
)
```

---

## PHASE 9: FINAL FACT TABLE COLUMN SELECTION

### Select Only Required Columns
```m
#"Selected Final Columns" = Table.SelectColumns(
    #"Expanded Drop",
    {
        "Booking ID",
        "DateID",
        "Time",
        "HourOfDay",
        "TimeBucket",
        "Customer ID",
        "VehicleTypeID",
        "PickupLocationID",
        "DropLocationID",
        "BookingStatusID",
        "PaymentMethodID",
        "Booking Value",
        "Ride Distance",
        "Avg VTAT",
        "Avg CTAT",
        "Driver Ratings",
        "Customer Rating",
        "Cancelled Rides by Customer",
        "Cancelled Rides by Driver",
        "Incomplete Rides"
    }
),

#"Renamed Final Columns" = Table.RenameColumns(
    #"Selected Final Columns",
    {
        {"Booking ID", "BookingID"},
        {"Time", "TimeOfDay"},
        {"Customer ID", "CustomerID"},
        {"Booking Value", "BookingValue"},
        {"Ride Distance", "RideDistance"},
        {"Avg VTAT", "AvgVTAT"},
        {"Avg CTAT", "AvgCTAT"},
        {"Driver Ratings", "DriverRating"},
        {"Customer Rating", "CustomerRating"},
        {"Cancelled Rides by Customer", "CancelledByCustomer"},
        {"Cancelled Rides by Driver", "CancelledByDriver"},
        {"Incomplete Rides", "IncompleteRide"}
    }
)
```

---

## VALIDATION CHECKLIST

After completing all transformations:

**Row-count checks (expected values are unverified until run on the actual CSV)**
- [ ] FactBookings: 150,000 rows (same as source)
- [ ] DimDate: 366 rows (2024 calendar)
- [ ] DimVehicle: 7 rows
- [ ] DimPaymentMethod: 6 rows
- [ ] DimBookingStatus: 5 rows
- [ ] DimLocation: 176 rows
- [ ] DimCustomer: 148,788 rows

✅ **Data Type Validation**
- [ ] All date fields are type `date`
- [ ] All time fields are type `time`
- [ ] All numeric fields are type `number` or `Int64`
- [ ] All flags are type `Int64` (0/1) or `logical`
- [ ] All IDs are correct type

✅ **Null Validation**
- [ ] Cancelled rides have NULL ratings (not 0)
- [ ] Cancelled rides have NULL VTAT/CTAT
- [ ] No unexpected nulls in ID fields
- [ ] Payment Method nulls only for cancelled/no-driver bookings

✅ **Foreign Key Validation**
- [ ] All VehicleTypeID values exist in DimVehicle
- [ ] All PaymentMethodID values exist in DimPaymentMethod (or null)
- [ ] All BookingStatusID values exist in DimBookingStatus
- [ ] All PickupLocationID values exist in DimLocation
- [ ] All DropLocationID values exist in DimLocation
- [ ] All DateID values exist in DimDate

---

## PERFORMANCE OPTIMIZATION

### Query Folding
- Keep transformations "fold-able" where possible
- Avoid complex M functions early in query
- Test with View Native Query to confirm folding

### Load Strategy
- Load dimension tables first
- Load fact table last (depends on dimensions for foreign keys)
- Disable "Enable Load" for staging queries

### Refresh Performance
- Expected refresh time: 30-60 seconds for 150K rows
- Incremental refresh not required for this dataset size

---

## TROUBLESHOOTING

### Issue: Triple Quotes Not Removing
**Solution:** Apply Text.Replace twice (inner quotes, then outer quotes)

### Issue: DateID Not Matching
**Solution:** Ensure same formula in both FactBookings and DimDate

### Issue: Merge Returns Nulls
**Solution:** Check for whitespace in lookup columns; apply Text.Trim first

### Issue: Relationship Not Working
**Solution:** Verify data types match exactly (Int64 = Int64, not Int64 = number)

---

**Version:** 1.0  
**Last Updated:** October 8, 2024  
**Test status:** not independently executed against a source CSV in this repository  
**Transformation status:** proposed steps; run and validate against the actual source before using the results
