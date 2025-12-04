# Shell Performance Dashboard (Nov 2025)

**Project type:** End-to-end data analytics & dashboarding (synthetic dataset, Power BI)  
**Tools used:** Python (data generation & cleaning), Jupyter Notebook, Power BI Desktop, Excel  
**Dataset size:** 100,000 rows — Synthetic Shell pump transactions (Nov 1–30, 2025)  
**Project goal:** Build an executive & operations dashboard for Shell India retail analytics (sales, volume, pump/attendant operations, loyalty & fleet).

---

## Files included in this repository
- `data/shell_nov2025_1lakh_cleaned.xlsx` — Cleaned transaction dataset (100k rows).  
- `notebooks/snitch_data_cleaning.ipynb` — (Optional) original data cleaning notebook used for the Snitch dataset (kept for reference).  
- `powerbi/Shell_Performance_Dashboard.pbix` — (optional) Power BI file (if attached).  
- `docs/screenshots/` — Dashboard screenshots exported (Overview & Operational pages).  
- `README.md` — This file.

> NOTE: The dataset used in this demo is synthetic for portfolio purposes. The dashboard screenshots and exported pages are included for reference. See references below. :contentReference[oaicite:2]{index=2}

---

## Project Summary

This project demonstrates a retail analytics workflow focused on Shell petrol pumps across 10 Indian cities for November 2025. The primary focus areas:

1. **Executive overview (Page 1)** — High level KPIs, daily sales trend, fuel-type & vehicle-type breakdown, Top-10 station list, loyalty metrics. (Screenshot: Overview page, shows KPIs such as Total Sales = ₹36.81M, Total Volume = 266.03K L, Total Transactions = 10K.) :contentReference[oaicite:3]{index=3}

2. **Operational analysis (Page 2)** — Pump-level volume trends, attendant performance (top attendants, transactions, sales), shift performance (Morning/Afternoon/Night) and attendant-level tables. (Screenshot: Operational page, shows Total Attendants = 6K, Top Attendant by Sales ≈ ₹17.89K, example top attendants & totals.) 

---

## Data (fields & contents)

The dataset contains the following columns (all cleaned, no blanks, no duplicates):

- TransactionID  
- TransactionDateTime (Nov 2025)  
- StationID, StationName, City, State  
- PumpNumber, Shift  
- AttendantID, AttendantName  
- TerminalID  
- FuelType (Petrol / Diesel / Premium)  
- UnitPrice(INR), Volume(Liters)  
- GrossAmount(INR), DiscountRate, DiscountAmount(INR), TaxAmount(INR), NetAmount(INR)  
- PaymentMethod, TransactionChannel, Segment (Retail/Fleet/Corporate)  
- VehicleType, OdometerKM  
- LoyaltyMember, LoyaltyPointsEarned, FleetCompany, PromoCode

(These columns map 1:1 to the visuals and measures used in the report; you can find them in the Excel file inside `data/`.)

---

## How the project was executed — step-by-step

> This section documents the exact steps performed so someone can reproduce the work.

### 1. Data generation (synthetic)
- Created a synthetic transaction dataset (100k rows) to replicate November 1–30, 2025, covering 100 stations across 10 cities.
- Each transaction had realistic attributes (fuel type, price perturbation, liters, discount rules, taxes, loyalty flags, segments).
- Attendant names were localized to India (Faker `en_IN`) so the dataset matches Indian context.
- Output file: `shell_nov2025_1lakh_cleaned.xlsx`.

### 2. Data validation & finalization
- Ensured uniqueness of `TransactionID` and `no nulls` across all columns.
- Pre-calculated monetary fields (Gross, DiscountAmount, Tax, Net) so Power BI requires minimal calculations and is performant.
- Exported a CSV/XLSX for Power BI consumption.

### 3. Load into Power BI
- Power BI Desktop → **Get Data → Excel** → selected `shell_nov2025_1lakh_cleaned.xlsx`.
- **Transform Data** in Power Query:
  - Set types (Date/Time, Decimal, Whole Number, Text).
  - Created a `DateOnly` column (DATEVALUE(TransactionDateTime)) for date slicers and trend axis.
  - Ensured no extra rounding errors.

### 4. Model & DAX measures
- Created measures used across pages (examples below). These measures power the KPI cards and visuals.

**Core KPIs**
```DAX
Total Sales = SUM('shell_nov2025'[NetAmount(INR)])
Total Volume (L) = SUM('shell_nov2025'[Volume(Liters)])
Total Transactions = DISTINCTCOUNT('shell_nov2025'[TransactionID])
Average Bill Value = DIVIDE([Total Sales], [Total Transactions], 0)
Total Discount = SUM('shell_nov2025'[DiscountAmount(INR)])
Total Tax = SUM('shell_nov2025'[TaxAmount(INR)])
