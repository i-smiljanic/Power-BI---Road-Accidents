# Road Accidents Analysis – Power BI

A one-page Power BI dashboard that analyzes road accidents in Great Britain in 2021 and 2022: how many accidents and casualties there are, how they changed from 2021 to 2022, and which conditions (vehicle, area, light, road type) they happen in.

**Tools:** Power BI · DAX · Power Query · Time intelligence (year to date, same period last year)
**Data:** 307,973 accidents with 417,883 casualties, 1 Jan 2021 – 31 Dec 2022 (25 columns, one row per accident) – [TODO: add data source / link]

## Business questions

1. How many accidents and casualties happened in the latest year, and how does that compare with the year before?
2. How do casualties split by severity (fatal, serious, slight)?
3. Which vehicle types, areas, light conditions and road types are involved?
4. How do casualties change through the year, and where do accidents happen?

## Dashboard

KPI cards (casualties, accidents, and casualties by severity) with year-over-year change, casualties by vehicle type, a monthly comparison of the two years, urban vs. rural, light conditions and road type, plus slicers for weather conditions and road surface. "CY" (current year) is the latest year in the selection (2022) and "PY" (previous year) is 2021.

![Dashboard](images/01_dashboard.png)

The location map is a **static image** generated from the latitude and longitude columns (density of all 307,973 accidents, fatal accidents highlighted), so it does not react to the slicers.

## Key findings

All figures were recomputed from the data and match the dashboard.

- **2022 vs. 2021:** accidents fell **11.7%** (163,554 → 144,419) and casualties **11.9%** (222,146 → 195,737).
- **Severity:** about **85.5%** of accidents are slight, **13.2%** serious and **1.3%** fatal. Casualties in fatal accidents fell the most, **−33.3%** (4,280 → 2,855), compared with −16.2% for serious and −10.6% for slight accidents.
- **Urban vs. rural:** urban areas account for **62%** of 2022 casualties, but a rural accident is about **2.6 times more likely to be fatal** (2.15% of accidents vs. 0.81%).
- **Darkness:** 26% of 2022 casualties happened in the dark, and accidents in the dark are fatal **1.8 times** more often than in daylight (1.93% vs. 1.06%).
- **Speed and road type:** roads with a 60 mph limit have the highest share of serious or fatal accidents (**21.6%**) and the highest fatal rate (2.80%), while 30 mph roads see 65% of all accidents but only 0.77% are fatal. Single carriageways account for **74%** of 2022 casualties; roundabouts have the lowest fatal rate (0.37%).
- **Vehicles:** cars account for **79.6%** of 2022 casualties, followed by vans (8.1%) and bikes (8.0%).
- **Timing:** accidents peak between 3 and 6 p.m. (the busiest hour is 4–5 p.m.), and Friday is the busiest day (50,529 accidents) while Sunday is the quietest (33,544).

## Data notes and limitations

- **"Fatal casualties" means all casualties in fatal accidents,** not only the people who died (2022: 2,855 casualties in 1,549 fatal accidents).
- The data covers two full years (2021–2022), so the year-over-year view compares just two points; the 2021 cards have no prior year and their change is blank.
- The vehicle groups combine categories: "Van" includes heavy goods vehicles of 7.5 tonnes and over, and "Bike" includes motorcycles and pedal cycles.
- The monthly chart's value axis does not start at zero, which exaggerates the difference between the two years.
- The map is a static picture and does not respond to filters.

## Data model and key measures

**Model:** a fact table `Data` (307,973 rows, one row per accident) related to a `Calendar` date table through `Accident Date`, plus a `_Measures` table. Power Query cleans the severity labels, and grouped columns (vehicle type, light conditions, weather and road surface) simplify the detailed categories.

**Key measures (DAX):**

```DAX
CY Casulties = TOTALYTD(SUM(Data[Number_of_Casualties]), 'Calendar'[Date])

PY Casulties =
CALCULATE(SUM(Data[Number_of_Casualties]), SAMEPERIODLASTYEAR('Calendar'[Date]))

YOY Casulties =
DIVIDE([CY Casulties] - [PY Casulties], [PY Casulties])

CY Accidents Count = TOTALYTD(COUNT(Data[Accident_Index]), 'Calendar'[Date])

PY Accidents =
CALCULATE(COUNT(Data[Accident_Index]), SAMEPERIODLASTYEAR('Calendar'[Date]))

YOY Accidents =
DIVIDE([CY Accidents Count] - [PY Accidents], [PY Accidents])
```

(Rename the measures once the "Casulties" spelling is corrected in the report.)

## How to open

Download `Road Accidents.pbix` and open it in Power BI Desktop; the data is stored inside the report file. [TODO: the source Excel file is not included in this repository because of its size; add a link or a compressed copy.]
