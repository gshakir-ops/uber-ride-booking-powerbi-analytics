# DAX Measures Reference

## Production-Ready KPI Library for Ride-Booking Analytics

This document provides exact DAX formulas for all measures used in the Power BI dashboard. Copy-paste ready for implementation.

---

## 1. CORE BOOKING VOLUME MEASURES

### [Total Bookings]
```dax
[Total Bookings] = COUNTA(FactBookings[BookingID])
```
**Definition:** Total count of all booking records regardless of status.  
**Format:** Whole number, thousands separator: `#,##0`  
**Filter Context:** Responds to all slicers (date, vehicle type, location, etc.)

---

### [Completed Bookings]
```dax
[Completed Bookings] = 
CALCULATE(
    [Total Bookings],
    DimBookingStatus[IsSuccess] = TRUE
)
```
**Definition:** Count of bookings with "Completed" status only.  
**Format:** Whole number: `#,##0`  
**Filter Context:** Overrides status slicer to force IsSuccess = TRUE

---

### [Cancelled Bookings]
```dax
[Cancelled Bookings] = 
CALCULATE(
    [Total Bookings],
    DimBookingStatus[CancellationType] IN {"Customer", "Driver"}
)
```
**Definition:** Combined count of customer + driver cancellations.  
**Format:** Whole number: `#,##0`  
**Filter Context:** Includes both customer and driver cancellations

---

### [Cancelled by Customer]
```dax
[Cancelled by Customer] = 
CALCULATE(
    [Total Bookings],
    DimBookingStatus[CancellationType] = "Customer"
)
```
**Definition:** Bookings cancelled by customer only.  
**Format:** Whole number: `#,##0`

---

### [Cancelled by Driver]
```dax
[Cancelled by Driver] = 
CALCULATE(
    [Total Bookings],
    DimBookingStatus[CancellationType] = "Driver"
)
```
**Definition:** Bookings cancelled by driver only.  
**Format:** Whole number: `#,##0`

---

### [No Driver Found]
```dax
[No Driver Found] = 
CALCULATE(
    [Total Bookings],
    DimBookingStatus[BookingStatus] = "No Driver Found"
)
```
**Definition:** Bookings where system could not assign a driver.  
**Format:** Whole number: `#,##0`

---

### [Incomplete Bookings]
```dax
[Incomplete Bookings] = 
CALCULATE(
    [Total Bookings],
    DimBookingStatus[BookingStatus] = "Incomplete"
)
```
**Definition:** Rides that started but did not complete.  
**Format:** Whole number: `#,##0`

---

### [Unique Customers]
```dax
[Unique Customers] = DISTINCTCOUNT(FactBookings[CustomerID])
```
**Definition:** Count of distinct customer IDs in filtered context.  
**Format:** Whole number: `#,##0`

---

## 2. PERFORMANCE RATE MEASURES

### [Completion Rate %]
```dax
[Completion Rate %] = 
DIVIDE(
    [Completed Bookings],
    [Total Bookings],
    0
)
```
**Definition:** Percentage of bookings successfully completed.  
**Format:** Percentage, one decimal: `0.0%`  
**Null Handling:** Returns 0% if no bookings (DIVIDE protects against division by zero)

---

### [Cancellation Rate %]
```dax
[Cancellation Rate %] = 
DIVIDE(
    [Cancelled Bookings],
    [Total Bookings],
    0
)
```
**Definition:** Combined customer + driver cancellations as % of total.  
**Format:** Percentage: `0.0%`

---

### [Customer Cancellation Rate %]
```dax
[Customer Cancellation Rate %] = 
DIVIDE(
    [Cancelled by Customer],
    [Total Bookings],
    0
)
```
**Definition:** Customer-initiated cancellations as % of total bookings.  
**Format:** Percentage: `0.0%`

---

### [Driver Cancellation Rate %]
```dax
[Driver Cancellation Rate %] = 
DIVIDE(
    [Cancelled by Driver],
    [Total Bookings],
    0
)
```
**Definition:** Driver-initiated cancellations as % of total bookings.  
**Format:** Percentage: `0.0%`

---

### [No Driver Rate %]
```dax
[No Driver Rate %] = 
DIVIDE(
    [No Driver Found],
    [Total Bookings],
    0
)
```
**Definition:** "No Driver Found" cases as % of total bookings.  
**Format:** Percentage: `0.0%`

---

### [Incomplete Rate %]
```dax
[Incomplete Rate %] = 
DIVIDE(
    [Incomplete Bookings],
    [Total Bookings],
    0
)
```
**Definition:** Incomplete rides as % of total bookings.  
**Format:** Percentage: `0.0%`

---

## 3. REVENUE MEASURES

### [Total Booking Value]
```dax
[Total Booking Value] = SUM(FactBookings[BookingValue])
```
**Definition:** Sum of all booking amounts including cancelled rides.  
**Format:** Currency, no decimals: `$#,##0`  
**Note:** May include partial charges for cancelled rides depending on business policy

---

### [Completed Revenue]
```dax
[Completed Revenue] = 
CALCULATE(
    [Total Booking Value],
    DimBookingStatus[IsSuccess] = TRUE
)
```
**Definition:** Revenue from completed rides only (realized revenue).  
**Format:** Currency: `$#,##0`

---

### [Average Booking Value]
```dax
[Average Booking Value] = 
AVERAGE(FactBookings[BookingValue])
```
**Definition:** Mean booking value across all bookings (including nulls naturally excluded).  
**Format:** Currency: `$#,##0`  
**Null Handling:** AVERAGE automatically excludes blank values

---

### [Average Completed Value]
```dax
[Average Completed Value] = 
CALCULATE(
    AVERAGE(FactBookings[BookingValue]),
    DimBookingStatus[IsSuccess] = TRUE
)
```
**Definition:** Mean booking value for completed rides only.  
**Format:** Currency: `$#,##0`

---

### [Revenue per KM]
```dax
[Revenue per KM] = 
DIVIDE(
    [Completed Revenue],
    SUM(FactBookings[RideDistance]),
    0
)
```
**Definition:** Completed revenue divided by total completed distance.  
**Format:** Currency per unit: `$#,##0.00 "/km"`  
**Filter Context:** Only applies to completed bookings (via [Completed Revenue])

---

## 4. OPERATIONAL EFFICIENCY MEASURES

### [Average VTAT]
```dax
[Average VTAT] = 
CALCULATE(
    AVERAGE(FactBookings[AvgVTAT]),
    DimBookingStatus[IsSuccess] = TRUE
)
```
**Definition:** Mean Vehicle Time at Arrival (pickup wait time) for completed rides.  
**Format:** Decimal: `#,##0.0 "sec"` or convert to minutes: `#,##0.0 "min"`  
**Null Handling:** AVERAGE excludes nulls (cancelled rides)  
**Conversion to Minutes:** Add `/ 60` if displaying as minutes

---

### [Average CTAT]
```dax
[Average CTAT] = 
CALCULATE(
    AVERAGE(FactBookings[AvgCTAT]),
    DimBookingStatus[IsSuccess] = TRUE
)
```
**Definition:** Mean Customer Time at Arrival / Ride Duration for completed rides.  
**Format:** Decimal: `#,##0.0 "sec"` or `#,##0.0 "min"`  
**Null Handling:** AVERAGE excludes nulls

---

### [Average Ride Distance]
```dax
[Average Ride Distance] = 
CALCULATE(
    AVERAGE(FactBookings[RideDistance]),
    DimBookingStatus[IsSuccess] = TRUE
)
```
**Definition:** Mean distance for completed rides only.  
**Format:** Decimal: `#,##0.0 "km"`  
**Null Handling:** Excludes cancelled/no-driver bookings naturally

---

### [Total Completed Distance]
```dax
[Total Completed Distance] = 
CALCULATE(
    SUM(FactBookings[RideDistance]),
    DimBookingStatus[IsSuccess] = TRUE
)
```
**Definition:** Sum of all ride distances for completed bookings.  
**Format:** Whole number: `#,##0 "km"`

---

### [Average Driver Rating]
```dax
[Average Driver Rating] = 
AVERAGE(FactBookings[DriverRating])
```
**Definition:** Mean driver rating (1-5 scale) across all bookings with ratings.  
**Format:** Decimal: `0.0 "⭐"`  
**Null Handling:** Automatically excludes nulls (cancelled/incomplete bookings)  
**Critical:** Do NOT convert nulls to 0 before averaging

---

### [Average Customer Rating]
```dax
[Average Customer Rating] = 
AVERAGE(FactBookings[CustomerRating])
```
**Definition:** Mean customer rating (1-5 scale) across all bookings with ratings.  
**Format:** Decimal: `0.0 "⭐"`  
**Null Handling:** Automatically excludes nulls

---

### [Rating Gap]
```dax
[Rating Gap] = 
[Average Customer Rating] - [Average Driver Rating]
```
**Definition:** Difference between customer and driver average ratings.  
**Format:** Decimal with +/- sign: `+0.0` or `-0.0`  
**Interpretation:** Positive = customers rate higher, Negative = drivers rate higher

---

## 5. CUSTOMER BEHAVIOR MEASURES

### [Rides per Customer]
```dax
[Rides per Customer] = 
DIVIDE(
    [Total Bookings],
    [Unique Customers],
    0
)
```
**Definition:** Average number of bookings per unique customer.  
**Format:** Decimal: `0.0 "rides/customer"`  
**Business Use:** Customer engagement and repeat behavior

---

### [Completed Rides per Customer]
```dax
[Completed Rides per Customer] = 
DIVIDE(
    [Completed Bookings],
    [Unique Customers],
    0
)
```
**Definition:** Average number of completed rides per customer.  
**Format:** Decimal: `0.0`

---

## 6. TIME-BASED MEASURES (OPTIONAL)

### [Previous Month Bookings]
```dax
[Previous Month Bookings] = 
CALCULATE(
    [Total Bookings],
    DATEADD(DimDate[FullDate], -1, MONTH)
)
```
**Definition:** Total bookings for same period in previous month.  
**Format:** Whole number: `#,##0`  
**Requirement:** Continuous date table required

---

### [Month-over-Month Growth %]
```dax
[Month-over-Month Growth %] = 
DIVIDE(
    [Total Bookings] - [Previous Month Bookings],
    [Previous Month Bookings],
    0
)
```
**Definition:** Percentage change from previous month.  
**Format:** Percentage: `+0.0%` or `-0.0%`  
**Interpretation:** Positive = growth, Negative = decline

---

### [YTD Bookings]
```dax
[YTD Bookings] = 
CALCULATE(
    [Total Bookings],
    DATESYTD(DimDate[FullDate])
)
```
**Definition:** Year-to-date total bookings.  
**Format:** Whole number: `#,##0`

---

### [YTD Revenue]
```dax
[YTD Revenue] = 
CALCULATE(
    [Completed Revenue],
    DATESYTD(DimDate[FullDate])
)
```
**Definition:** Year-to-date completed revenue.  
**Format:** Currency: `$#,##0`

---

## 7. ADVANCED CALCULATED MEASURES

### [Bookings with Ratings Count]
```dax
[Bookings with Ratings Count] = 
CALCULATE(
    COUNTA(FactBookings[DriverRating]),
    NOT(ISBLANK(FactBookings[DriverRating]))
)
```
**Definition:** Count of bookings that have driver ratings (non-null).  
**Format:** Whole number: `#,##0`  
**Use:** Denominator for rating penetration analysis

---

### [Rating Penetration %]
```dax
[Rating Penetration %] = 
DIVIDE(
    [Bookings with Ratings Count],
    [Total Bookings],
    0
)
```
**Definition:** % of bookings with ratings (proxy for completion + engagement).  
**Format:** Percentage: `0.0%`

---

### [Average Booking Value per Customer]
```dax
[Average Booking Value per Customer] = 
DIVIDE(
    [Total Booking Value],
    [Unique Customers],
    0
)
```
**Definition:** Total booking value divided by unique customer count.  
**Format:** Currency: `$#,##0`  
**Business Use:** Customer lifetime value proxy

---

## 8. PICKUP/DROP LOCATION MEASURES

**Note:** DimLocation has two relationships (Pickup/Drop). One is inactive. Use USERELATIONSHIP() to access Drop location.

### [Bookings by Pickup Location]
```dax
[Bookings by Pickup Location] = [Total Bookings]
```
**Definition:** Uses active Pickup relationship automatically.  
**Format:** Whole number: `#,##0`

---

### [Bookings by Drop Location]
```dax
[Bookings by Drop Location] = 
CALCULATE(
    [Total Bookings],
    USERELATIONSHIP(FactBookings[DropLocationID], DimLocation[LocationID])
)
```
**Definition:** Activates inactive Drop relationship to count by destination.  
**Format:** Whole number: `#,##0`

---

## MEASURE ORGANIZATION

### Recommended Folder Structure in Power BI

```
📁 Booking Volume
   ├─ [Total Bookings]
   ├─ [Completed Bookings]
   ├─ [Cancelled Bookings]
   ├─ [Cancelled by Customer]
   ├─ [Cancelled by Driver]
   ├─ [No Driver Found]
   ├─ [Incomplete Bookings]
   └─ [Unique Customers]

📁 Performance Rates
   ├─ [Completion Rate %]
   ├─ [Cancellation Rate %]
   ├─ [Customer Cancellation Rate %]
   ├─ [Driver Cancellation Rate %]
   ├─ [No Driver Rate %]
   └─ [Incomplete Rate %]

📁 Revenue
   ├─ [Total Booking Value]
   ├─ [Completed Revenue]
   ├─ [Average Booking Value]
   ├─ [Average Completed Value]
   └─ [Revenue per KM]

📁 Operations
   ├─ [Average VTAT]
   ├─ [Average CTAT]
   ├─ [Average Ride Distance]
   ├─ [Total Completed Distance]
   ├─ [Average Driver Rating]
   ├─ [Average Customer Rating]
   └─ [Rating Gap]

📁 Customer
   ├─ [Rides per Customer]
   ├─ [Completed Rides per Customer]
   └─ [Average Booking Value per Customer]

📁 Time Intelligence (Optional)
   ├─ [Previous Month Bookings]
   ├─ [Month-over-Month Growth %]
   ├─ [YTD Bookings]
   └─ [YTD Revenue]
```

---

## IMPLEMENTATION NOTES

### Null Handling Critical Rules
1. **DO NOT** use `IF(ISBLANK(...), 0, ...)` on ratings or VTAT/CTAT
2. **DO** let AVERAGE() naturally exclude blanks
3. **DO** use DIVIDE() with third parameter for division-by-zero protection
4. Cancelled rides have legitimate NULL ratings (not 0)

### Filter Context Best Practices
1. Use CALCULATE() to override slicer filters when needed
2. Test each measure with date/vehicle/location slicers active
3. Verify percentages always sum to 100% where expected
4. Confirm cancelled bookings don't show ratings in visuals

### Performance Optimization
1. Avoid complex iterators (SUMX, FILTER) where simple aggregations work
2. Use dimension flags (IsSuccess) instead of text comparisons
3. Prefer DIVIDE() over division operator for safety
4. Test calculated columns vs measures (prefer measures where possible)

### Number Formatting Standards
| Measure Type | Format String | Example |
|--------------|---------------|---------|
| Count | `#,##0` | 1,234 |
| Percentage | `0.0%` | 62.5% |
| Currency | `$#,##0` | $1,234 |
| Decimal (1 place) | `0.0` | 4.2 |
| Decimal (2 places) | `0.00` | 4.23 |
| With Unit | `#,##0.0 "km"` | 12.5 km |

---

## TESTING CHECKLIST

Before deploying measures to production dashboards:

✅ **Calculation Accuracy**
- [ ] Completion Rate = Completed / Total (test with sample)
- [ ] Sum of all rates ≈ 100% (Completed + Cancelled + No Driver + Incomplete)
- [ ] Revenue measures exclude cancelled rides correctly
- [ ] Rating averages are in 0-5 range

✅ **Null Handling**
- [ ] Cancelled bookings don't contribute to average ratings
- [ ] VTAT/CTAT only calculated for completed rides
- [ ] Division by zero handled with DIVIDE()
- [ ] No measures return ERROR or BLANK unexpectedly

✅ **Filter Context**
- [ ] Date slicer affects all time-dependent measures
- [ ] Vehicle type slicer correctly filters all measures
- [ ] Status-specific measures override status slicer appropriately
- [ ] Location measures use correct relationship (Pickup vs Drop)

✅ **Performance**
- [ ] No measures take >2 seconds to calculate
- [ ] Complex measures use appropriate filters
- [ ] No unnecessary calculated columns (use measures instead)

---

**Version:** 1.0  
**Last Updated:** October 8, 2024  
**Status:** Production-ready  
**Model Compatibility:** Star schema with DimBookingStatus.IsSuccess flag
