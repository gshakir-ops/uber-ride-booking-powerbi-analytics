# Data Dictionary

> **Validation status:** This is a schema reference inherited from project documentation. The source CSV is not included, so the stated row counts, distinct counts, completeness rates, examples, and null patterns have not been independently verified. Confirm every field name, type, category, unit, and data-quality statement against the actual source before treating it as authoritative.

## Overview
This document describes the expected source fields and proposed model. Validate the definitions and examples against the actual CSV before use.

---

## Source CSV Schema

### Raw File: `ncr_ride_bookings.csv`
- **Records:** Approximately 150,000 in prior project notes; not verified in this repository
- **Date Range:** 2024-01-01 to 2024-12-30 in prior notes; not verified in this repository
- **Format:** CSV with header row

---

## Field Definitions

The examples and category lists below are illustrative descriptions from earlier project notes, not a verified inventory of the source file. Confirm all 21 headers and actual values against the CSV before using this dictionary for transformations.

| Column | Data Type | Business Meaning | Example | Data Quality Notes |
|--------|-----------|------------------|---------|-------------------|
| **Date** | Date | Booking date | 2024-03-23 | Completeness not validated; standardized YYYY-MM-DD |
| **Time** | Time | Booking time | 12:29:38 | Completeness not validated; HH:MM:SS format |
| **Booking ID** | String | Unique booking identifier | CNR5884300 | Completeness not validated; triple quotes removed during ETL |
| **Booking Status** | String | Final booking outcome | "Completed", "Cancelled by Driver", "Cancelled by Customer", "No Driver Found", "Incomplete" | Completeness and distinct count not validated; prior notes list 5 labels |
| **Customer ID** | String | Unique customer identifier | CID1982111 | Completeness not validated; triple quotes removed during ETL |
| **Vehicle Type** | String | Vehicle category | "Auto", "Go Mini", "Go Sedan", "Bike", "eBike", "Premier Sedan", "Uber XL" | Completeness and distinct count not validated; prior notes list 7 labels |
| **Pickup Location** | String | Ride origin location | "Palam Vihar" | Completeness and distinct count not validated; prior notes report 176 locations |
| **Drop Location** | String | Ride destination location | "Jhilmil" | Completeness not validated; 176 unique locations |
| **Avg VTAT** | Decimal | Average Vehicle Time at Arrival (seconds) | 4.9 | Null pattern and meaning must be verified from source documentation |
| **Avg CTAT** | Decimal | Average Customer Time at Arrival / Ride Duration (seconds) | 14.0 | Null pattern and meaning must be verified from source documentation |
| **Cancelled Rides by Customer** | Boolean | Flag: Customer cancelled | 0 or 1 | Binary flag; 0 = no, 1 = yes |
| **Reason for cancelling by Customer** | String | Why customer cancelled | "Driver is not moving towards pickup location" | Expected null pattern; verify against source |
| **Cancelled Rides by Driver** | Boolean | Flag: Driver cancelled | 0 or 1 | Binary flag; 0 = no, 1 = yes |
| **Driver Cancellation Reason** | String | Why driver cancelled | "Personal & Car related issues" | Expected null pattern; verify against source |
| **Incomplete Rides** | Boolean | Flag: Ride started but not finished | 0 or 1 | Binary flag; 0 = no, 1 = yes |
| **Incomplete Rides Reason** | String | Why ride incomplete | "Vehicle Breakdown" | Expected null pattern; verify against source |
| **Booking Value** | Decimal | Transaction amount (currency) | 237 | Null for some cancelled/no-driver bookings |
| **Ride Distance** | Decimal | Distance traveled (kilometers) | 5.73 | Null for cancelled/no-driver bookings |
| **Driver Ratings** | Decimal | Customer's rating of driver (0-5 scale) | 4.9 | Null for incomplete/cancelled bookings (no rating given) |
| **Customer Rating** | Decimal | Driver's rating of customer (0-5 scale) | 4.9 | Null for incomplete/cancelled bookings (no rating given) |
| **Payment Method** | String | How customer paid | "UPI", "Cash", "Debit Card", "Credit Card", "Uber Wallet" | Some nulls for cancelled/no-driver bookings |

---

## Data Model Tables

### FactBookings (150,000 rows)
**Grain:** One row per booking transaction  
**Purpose:** Central fact table containing transactional metrics

| Field | Type | Source | Notes |
|-------|------|--------|-------|
| BookingID | String (PK) | Booking ID | Primary key |
| DateID | Integer (FK) | Date | Foreign key to DimDate |
| TimeOfDay | Time | Time | Original time component |
| HourOfDay | Integer | Derived from Time | 0-23 for time-of-day analysis |
| TimeBucket | String | Derived from Hour | "Early Morning", "Morning", "Afternoon", "Evening", "Night" |
| CustomerID | String (FK) | Customer ID | Foreign key to DimCustomer |
| VehicleTypeID | Integer (FK) | Vehicle Type | Foreign key to DimVehicle |
| PickupLocationID | Integer (FK) | Pickup Location | Foreign key to DimLocation |
| DropLocationID | Integer (FK) | Drop Location | Foreign key to DimLocation (inactive relationship) |
| BookingStatusID | Integer (FK) | Booking Status | Foreign key to DimBookingStatus |
| PaymentMethodID | Integer (FK) | Payment Method | Foreign key to DimPaymentMethod |
| BookingValue | Decimal | Booking Value | Measure: revenue/amount |
| RideDistance | Decimal | Ride Distance | Measure: kilometers |
| AvgVTAT | Decimal | Avg VTAT | Measure: seconds, null-safe |
| AvgCTAT | Decimal | Avg CTAT | Measure: seconds, null-safe |
| DriverRating | Decimal | Driver Ratings | Measure: 0-5 scale, null for cancelled |
| CustomerRating | Decimal | Customer Rating | Measure: 0-5 scale, null for cancelled |
| CancelledByCustomer | Boolean | Cancelled Rides by Customer | Flag |
| CancelledByDriver | Boolean | Cancelled Rides by Driver | Flag |
| IncompleteRide | Boolean | Incomplete Rides | Flag |

---

### DimDate (366 rows)
**Purpose:** Date dimension for time-based analysis

| Field | Type | Notes |
|-------|------|-------|
| DateID | Integer (PK) | YYYYMMDD format (e.g., 20240101) |
| FullDate | Date | 2024-01-01 |
| Year | Integer | 2024 |
| Quarter | Integer | 1-4 |
| Month | Integer | 1-12 |
| MonthName | String | "January", "February", etc. |
| Day | Integer | 1-31 |
| DayOfWeek | Integer | 1=Monday, 7=Sunday |
| DayName | String | "Monday", "Tuesday", etc. |
| WeekNumber | Integer | ISO week number 1-53 |
| IsWeekend | Boolean | TRUE for Saturday/Sunday |

---

### DimVehicle (7 rows)
**Purpose:** Vehicle type reference dimension

| Field | Type | Notes |
|-------|------|-------|
| VehicleTypeID | Integer (PK) | 1-7 surrogate key |
| VehicleType | String | "Auto", "Bike", "eBike", "Go Mini", "Go Sedan", "Premier Sedan", "Uber XL" |
| IsShared | Boolean | TRUE for shared rides (Go Mini/Go Sedan) |

---

### DimPaymentMethod (6 rows)
**Purpose:** Payment method reference dimension

| Field | Type | Notes |
|-------|------|-------|
| PaymentMethodID | Integer (PK) | 1-6 surrogate key |
| PaymentMethod | String | "UPI", "Cash", "Debit Card", "Credit Card", "Uber Wallet", "Other" |
| IsDigital | Boolean | TRUE for non-cash methods |

---

### DimBookingStatus (5 rows)
**Purpose:** Booking status with business logic flags

| Field | Type | Notes |
|-------|------|-------|
| BookingStatusID | Integer (PK) | 1-5 surrogate key |
| BookingStatus | String | "Completed", "Cancelled by Customer", "Cancelled by Driver", "No Driver Found", "Incomplete" |
| IsSuccess | Boolean | TRUE for Completed only |
| CancellationType | String | "Customer", "Driver", "System", "None" |
| FailureCategory | String | "Success", "Customer Cancellation", "Driver Cancellation", "No Driver", "Incomplete" |

**Mapping:**
- Completed → IsSuccess=TRUE, CancellationType="None"
- Cancelled by Customer → IsSuccess=FALSE, CancellationType="Customer"
- Cancelled by Driver → IsSuccess=FALSE, CancellationType="Driver"
- No Driver Found → IsSuccess=FALSE, CancellationType="System"
- Incomplete → IsSuccess=FALSE, CancellationType="None"

---

### DimLocation (176 rows)
**Purpose:** Pickup and drop location reference (single dimension, dual-referenced)

| Field | Type | Notes |
|-------|------|-------|
| LocationID | Integer (PK) | 1-176 surrogate key |
| LocationName | String | "Palam Vihar", "Khan Market", "Connaught Place", etc. |
| IsMajor | Boolean | TRUE for top 20 locations by booking volume |

**Note:** This dimension is referenced twice in FactBookings (Pickup and Drop). One relationship is active, the other inactive.

---

### DimCustomer (148,788 rows)
**Purpose:** Customer reference dimension (minimal - only ID available)

| Field | Type | Notes |
|-------|------|-------|
| CustomerID | String (PK) | Unique customer identifier from source |

**Note:** No additional customer attributes (name, email, segment, demographics) available in source data.

---

## Derived Fields

### Created During Power Query Transformation

| Field | Logic | Purpose |
|-------|-------|---------|
| **HourOfDay** | EXTRACT(HOUR FROM Time) | Time-of-day analysis (0-23) |
| **TimeBucket** | CASE logic on HourOfDay | Categorical time grouping |
| **IsCompleted** | BookingStatus = "Completed" | Quick filter flag |
| **IsCancelled** | CancelledByCustomer OR CancelledByDriver | Quick filter flag |
| **DateID** | CONVERT(Date, YYYYMMDD) | Relationship key to DimDate |

### TimeBucket Logic
- **Early Morning:** 00:00 - 05:59 (Hours 0-5)
- **Morning:** 06:00 - 11:59 (Hours 6-11)
- **Afternoon:** 12:00 - 17:59 (Hours 12-17)
- **Evening:** 18:00 - 21:59 (Hours 18-21)
- **Night:** 22:00 - 23:59 (Hours 22-23)

---

## Null Value Handling

### Expected Nulls (Legitimate Business State)

| Field | When NULL is Expected | Interpretation |
|-------|----------------------|----------------|
| **AvgVTAT** | Cancelled by Customer, Cancelled by Driver, No Driver Found | Ride never started, no vehicle arrival to measure |
| **AvgCTAT** | Cancelled by Customer, Cancelled by Driver, No Driver Found | Ride never started, no ride duration |
| **DriverRating** | Incomplete, Cancelled by Customer, Cancelled by Driver, No Driver Found | No rating given (ride didn't complete) |
| **CustomerRating** | Incomplete, Cancelled by Customer, Cancelled by Driver, No Driver Found | No rating given (ride didn't complete) |
| **BookingValue** | No Driver Found (some cases) | No charge when driver never assigned |
| **RideDistance** | No Driver Found, Cancelled before pickup | No ride occurred, no distance traveled |

### Critical Rule
**DO NOT convert null ratings/VTAT/CTAT to 0 (zero).**  
Null = "not applicable" (ride didn't happen).  
Zero = "explicitly rated zero" or "zero seconds wait" (very different meanings).

---

## Data Relationships

```
DimDate (1) ────────── (∞) FactBookings
DimVehicle (1) ──────── (∞) FactBookings
DimPaymentMethod (1) ── (∞) FactBookings
DimBookingStatus (1) ── (∞) FactBookings
DimLocation (1) ──────── (∞) FactBookings [Pickup - Active]
DimLocation (1) ──────── (∞) FactBookings [Drop - Inactive]
DimCustomer (1) ──────── (∞) FactBookings
```

**Note:** DimLocation has two relationships to FactBookings. Power BI requires one to be inactive. DAX measures use USERELATIONSHIP() to activate the Drop relationship when needed.

---

## Data Volume Summary

| Table | Row Count | Purpose |
|-------|-----------|---------|
| FactBookings | 150,000 | Transactions |
| DimDate | 366 | 2024 calendar year |
| DimVehicle | 7 | Vehicle types |
| DimPaymentMethod | 6 | Payment methods |
| DimBookingStatus | 5 | Status values |
| DimLocation | 176 | Pickup/drop locations |
| DimCustomer | 148,788 | Unique customers |
| **Total** | **~299,342** | Complete model |

---

## Field Naming Conventions

### Power BI Implementation
- **Tables:** PascalCase with prefix (FactBookings, DimDate, DimVehicle)
- **Columns:** PascalCase, no spaces (BookingValue, RideDistance, CustomerID)
- **Measures:** Square brackets with spaces ([Total Bookings], [Completion Rate %])
- **Flags:** Prefix "Is" for boolean (IsCompleted, IsWeekend, IsDigital)

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2024-10-08 | Initial data dictionary for ride-booking analytics project |

---

**Last Updated:** October 8, 2024  
**Author:** Portfolio Project Documentation  
**Status:** Production-ready
