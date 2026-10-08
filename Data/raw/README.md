# Dataset Information

## Source Data for Ride-Booking Analytics

### File Details
- **Filename:** `ncr_ride_bookings.csv`
- **Size:** ~150,000 rows, 21 columns
- **Format:** CSV with comma delimiter, UTF-8 encoding
- **Date Range:** January 1, 2024 - December 30, 2024
- **Geographic Scope:** NCR (National Capital Region) - India

### Source Location
The raw dataset should be placed in this directory before running the Power BI report.

**Path:** `Data/raw/ncr_ride_bookings.csv`

### Data Description
This dataset contains ride-booking transactions from a ride-sharing platform similar to Uber/Lyft. Each row represents one booking attempt with its outcome, customer/driver information, and transaction details.

### Important Notes
1. **Portfolio Project:** This is an independent educational/portfolio project and is not affiliated with Uber or any specific ride-sharing company.
2. **Privacy:** All customer and driver IDs are anonymized.
3. **Disclaimer:** Data is for analytical demonstration purposes only.

### How to Obtain
If you're viewing this repository and need the dataset:
1. The original dataset (`ncr_ride_bookings.csv`) is not included in this repository due to size constraints
2. Contact the repository owner for access to the dataset
3. Alternatively, place your own similarly-structured ride-booking dataset here

### Dataset Schema
See `Documentation/Data_Dictionary.md` for complete field definitions and data types.

### Data Quality
- **Completeness:** 100% complete for core transactional fields (Date, Time, Booking ID, Status, Vehicle Type, Locations)
- **Null Values:** Present in ratings/VTAT/CTAT for cancelled rides (expected behavior)
- **Validation:** See `Documentation/Data_Quality_Report.md` for detailed validation results

---

**Note:** The Power BI report expects the CSV file at the path above. Update the data source connection if placing the file elsewhere.
