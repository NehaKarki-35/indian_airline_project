# Indian Airline Flight Delay Analysis

## Introduction

Flight delays are one of the most persistent operational challenges in the aviation industry. They affect passenger experience, airline profitability, and airport efficiency. This project performs a structured exploratory data analysis on a domestic airline flight dataset to understand what causes delays, which airports and time periods are most affected, and how different types of delays relate to each other.

The analysis covers flight scheduling patterns, delay cause breakdown, departure period impact, cancellation behavior, and the propagation of delays from departure to arrival.

---

## Problem Statement

Airlines and airport operations teams need to understand the root causes of delays to reduce them. The core business questions this project investigates are:

- What are the main causes of flight delays?
- Which airports have the highest delays?
- Which airlines perform best and worst?
- Does departure delay affect arrival delay?
- Does weather increase delays and cancellations?
- Do flight timings affect delays?
- How can airlines reduce delays and cancellations?

Three hypotheses were defined before analysis:

- H1 (Weather Impact): Weather delay has a significant positive effect on arrival delay.
- H2 (Late Aircraft Effect): Late aircraft delay is the strongest predictor of arrival delay.
- H3 (Airline Performance): Different airlines show significant differences in their average delay performance.

---

## Project Objectives

- Identify the primary causes of flight delays across different delay categories.
- Analyze which origin airports contribute the most to total delay.
- Understand how departure period (Morning, Afternoon, Evening, Night) affects arrival delay.
- Examine the relationship between departure delay and arrival delay.
- Investigate the role of Late Aircraft Delay as a delay propagation mechanism.
- Produce a clean, analysis-ready dataset from the raw flight records.

---

## Dataset Information

**File:** airline_flight.csv

**Original shape:** 4,821 rows, 18 columns

**After cleaning:** 4,813 rows (8 duplicates removed, 2 invalid Departure_Time records dropped)

**18 origin airports** in the dataset

| Column | Type | Description |
|---|---|---|
| DayOfWeek | int64 | Day of week the flight operated (1–7) |
| Origin_Airport | object | Airport from which the flight departed |
| Scheduled_Departure | int64 | Planned departure time (converted to time format) |
| Departure_Time | int64 | Actual departure time (converted to time format) |
| Dep_Delay | int64 | Departure delay in minutes |
| Scheduled_Arrival | int64 | Planned arrival time |
| Arrival_Time | int64 | Actual arrival time |
| Arrival_Delay | int64 | Arrival delay in minutes |
| Arr_Del_morethan15 | int64 | Whether arrival delay exceeded 15 minutes |
| Cancelled | int64 | Whether the flight was cancelled |
| Diverted | int64 | Whether the flight was diverted |
| Distance | int64 | Total distance of the flight |
| Carrier_Delay | float64 | Delay caused by airline operational issues |
| WeatherDelay | float64 | Delay caused by weather conditions |
| NAS_Delay | float64 | Delay caused by National Airspace System (air traffic, congestion) |
| Security_Delay | float64 | Delay caused by security-related issues |
| Late_Aircraft_Delay | float64 | Delay caused by a late-arriving aircraft from a previous flight |
| Top_Carriers | int64 | Airline/carrier identifier |

**New feature created:**

| Feature | Description |
|---|---|
| Departure_Period | Categorized from Scheduled_Departure into Night (before 6 AM), Morning (6 AM–12 PM), Afternoon (12 PM–6 PM), Evening (after 6 PM) |

**Departure period distribution:**
- Morning: 1,984 flights
- Afternoon: 1,641 flights
- Evening: 1,093 flights
- Night: 101 flights

---

## Technologies Used

| Tool / Library | Purpose |
|---|---|
| Python | Core programming language |
| Jupyter Notebook | Development and analysis environment |
| Pandas | Data loading, cleaning, and manipulation |
| NumPy | Numerical operations |
| Matplotlib | Static charts and scatter plots |
| Seaborn | Statistical visualizations — boxplots, heatmaps, histplots, countplots |

---

## Project Structure

    indian_airline_project/
    │
    ├── data/
    │   └── airline_flight.csv
    │
    ├── notebooks/
    │   ├── airline analysis.ipynb       # Main EDA notebook (68 cells)
    │   ├── litrecture review.ipynb      # Literature review summary
    │   └── report.ipynb                 # Final insights report
    │
    ├── LICENSE
    └── README.md

---

## Data Cleaning

**1. Duplicate removal**
df.duplicated().sum() returned 8 duplicate records. These were removed using df.drop_duplicates().

**2. Time format conversion**
Departure_Time and Scheduled_Departure were stored as integers in HHMM format. Both were converted to proper time objects using pd.to_datetime with format='%H%M'. This conversion identified 2 invalid records in Departure_Time which were removed using df.dropna(subset=['Departure_Time']).

**3. Missing values**
No missing values were found in the original dataset. The only nulls introduced were from the time conversion step, which were handled immediately.

**4. Outlier treatment**
Weather delay outliers above the 99th percentile were removed. Arrival delay outliers were removed per departure period group using the IQR method (1.5 x IQR bounds).

**5. Feature engineering**
A new categorical column Departure_Period was created from Scheduled_Departure by binning hours into four categories: Night, Morning, Afternoon, and Evening.

**6. Type correction**
Cancelled column contained null values and was cast to float first, filled with 0, then converted to integer for analysis.

---

## Exploratory Data Analysis

### Univariate Analysis

**DayOfWeek:** Day 7 (Sunday) has the highest number of flights (789), followed by Day 5 (Friday, 779). Day 1 (Monday) has the fewest (626). Day of week has minimal impact on delays overall.

**Departure Period:** Most flights operate in the Morning period (1,984), followed by Afternoon (1,641), Evening (1,093), and Night (101).

**Departure Delay:** Ranges from -20 minutes (early) to +328 minutes. Most flights have small negative delays, indicating many depart slightly ahead of schedule.

**Top Origin Airports:** LAX records the highest number of departing flights, followed by DFW and JFK.

**Scheduled Departure:** Most common departure times are around 6:00 AM (174 flights) and 8:15 AM (170 flights). Peak scheduling is concentrated between 7 AM and 10 AM.

### Bivariate Analysis

**Departure Delay vs Arrival Delay:** Strong positive correlation confirmed by scatter plot. Flights that depart late almost always arrive late. Small delays can sometimes be recovered in flight, but large delays are difficult to compensate.

**Departure Period vs Arrival Delay:** Evening flights have the highest median arrival delay. Morning flights show the lowest delays. Night flights have the least delay overall.

**Delay Cause Breakdown:** Carrier Delay and NAS Delay are the primary contributors to total delays. Weather Delay has a comparatively lower impact. Security Delay contributes the least.

**Delay by Airport:** CLT records the highest total delays. JFK, LAX, and DFW also show high delay volumes driven primarily by Carrier Delay and NAS Delay.

### Correlation Analysis

Key confirmed correlations from the heatmap across 15 numerical features:

- Dep_Delay and Arrival_Delay show strong positive correlation.
- Late_Aircraft_Delay correlates with both departure and arrival delays.
- Scheduled_Arrival and Arrival_Time are strongly correlated.
- DayOfWeek shows very low correlation with all delay variables.
- NAS_Delay shows moderate correlation with arrival delay.

---

## Key Findings

- LAX, DFW, and JFK are the busiest origin airports by departure volume.
- CLT airport records the highest total delays across the dataset.
- Carrier Delay and NAS Delay are the two biggest contributors to total flight delay. Weather Delay has a lower impact than commonly assumed.
- Late Aircraft Delay is identified as the biggest single average delay cause — a delayed incoming aircraft directly causes the next departure to be late.
- Evening flights have the highest arrival delays. Morning flights are the most punctual. Night flights have the least delay.
- Departure delay has a strong direct impact on arrival delay. Large delays are rarely recovered during flight.
- Day of the week has little effect on delays.
- Security Delay is the smallest contributing factor across all delay categories.
- Most flights operate in the Morning period; Night operations are minimal.

---

## Visualizations

**Top 15 Origin Airports:** Horizontal bar chart showing LAX, DFW, and JFK as the top three departure airports by flight volume.

**Scheduled Departure Hour Distribution:** Histplot showing peak scheduling at 8 AM with secondary concentration at 5–6 PM.

**Total Delay by Reason:** Bar chart showing Carrier Delay leads, followed by NAS Delay. Weather and Security Delays are significantly lower.

**Top 10 Airports by Delay Reason:** Stacked horizontal bar chart — CLT leads in total delay, followed by JFK, LAX, and DFW.

**Departure Delay vs Arrival Delay:** Hexbin scatter plot confirming strong positive linear relationship.

**Arrival Delay by Departure Period:** Boxplot showing evening flights have highest median delay and morning flights have lowest.

**Average Delay Causes:** Horizontal bar chart showing Late Aircraft Delay as the tallest bar — the primary average delay driver.

**Flight Delay Correlation Heatmap:** Full annotated heatmap across 15 numerical features.

---

## Flight Delay Patterns

**Pattern 1 — Delay Propagation:** If a flight departs late, it almost always arrives late. Large delays are difficult to recover during flight time.

**Pattern 2 — Time-of-Day Effect:** Evening flights accumulate the highest delays due to congestion building through the day. Morning flights are most punctual. Night flights benefit from low air traffic.

**Pattern 3 — Late Aircraft Chain Reaction:** A delayed incoming aircraft causes the next scheduled departure to be late as well, creating a cascading effect across multiple flights on the same aircraft.

**Pattern 4 — Airport Congestion Bottlenecks:** CLT, JFK, LAX, and DFW concentrate the most total delay minutes. NAS Delay confirms that airport-level congestion worsens delays beyond what airlines alone can control.

**Pattern 5 — Carrier Operations as the Primary Controllable Delay:** Carrier Delay is the largest total delay contributor and is within the airline's control to reduce through crew scheduling, maintenance, and turnaround time improvements.

---

## Business Recommendations

**1. Prioritize reducing Carrier Delay.**
Carrier Delay is the single largest contributor to total delays. Airlines should focus on crew scheduling optimization, preventive maintenance, and turnaround time management.

**2. Schedule buffer time between flights on the same aircraft.**
Late Aircraft Delay is the biggest average delay cause. Building buffer time into schedules for high-traffic routes would reduce chain-reaction delays.

**3. Focus improvement efforts at CLT, JFK, LAX, and DFW.**
These four airports account for the highest total delay volumes. Targeted ground crew efficiency and gate coordination at these hubs would have the highest impact.

**4. Reduce evening flight congestion.**
Evening flights consistently show the highest arrival delays. Redistributing some evening departures to morning or adjusting scheduling density during peak evening hours could reduce overall delay.

**5. Coordinate with air traffic control on NAS Delay reduction.**
NAS Delay involves airport congestion and airspace management. Airlines should work with ATC on slot optimization at high-volume airports.

---

## Future Improvements

- Add airline-level performance comparison using the Top_Carriers column to rank individual carriers by average delay and on-time performance.
- Build a regression or classification model to predict arrival delay or cancellation probability based on departure period, carrier, origin airport, and delay cause features.
- Replace the hardcoded local file path with a relative path so the notebook runs without modification after cloning.
- Add a requirements.txt file for reproducibility.
- Translate all cell commentary currently written in Hindi/Urdu into English for broader accessibility.
- Expand the dataset with destination airport and route-level data for deeper analysis.

---

## Author

**Neha Karki**

GitHub: [https://github.com/NehaKarki-35](https://github.com/NehaKarki-35)

---

## License

This project is licensed under the MIT License.
