## Aviation Accident Safety Analysis (1983-2023)
This project analyzes U.S. and international aviation accident records to identify aircraft makes and models associated with lower safety risk for an airline/insurer client.
The analysis focuses on two primary outcomes:
1. **Passenger harm risk**: fraction of occupants who were fatally or seriously injured.
2. **Aircraft loss risk**: whether the aircraft was destroyed.
It also investigates how operational factors (specifically **Engine Type** and **Purpose of Flight**) relate to those outcomes.
---
### Business Objective
The client wants recommendations for aircraft that are safer in accidents, with separate recommendations for:
- **Small aircraft** (<=20 occupants)
- **Large aircraft** (>20 occupants)
---
### Repository Contents
- `Aviation_Accidents_Cleaning.ipynb`  
  Data loading, quality checks, filtering, feature engineering, and export of cleaned data.
- `Aviation_Accidents_Data_Analysis.ipynb`  
  Exploratory analysis, make/model comparisons, factor analysis, charts, and recommendations.
- `data/AviationData.csv`  
  Raw accident/incident dataset.
- `data/AviationData_cleaned.csv`  
  Cleaned analysis-ready dataset generated from the cleaning notebook.
---
### Environment and Dependencies
Python 3.10 with:
- `pandas`
- `numpy`
- `matplotlib`
- `seaborn`
- `Jupyter`

#### Install example:
#### `pip install pandas numpy matplotlib seaborn jupyter`
---
### Reproducibility: Run Order
1. Open and run Aviation_Accidents_Cleaning.ipynb from top to bottom.
2. Confirm data/AviationData_cleaned.csv is produced.
3. Open and run Aviation_Accidents_Data_Analysis.ipynb.
---
### Data Preparation Summary
Raw data scope
- 88,889 records
- 31 original columns
Primary client filter
Kept records meeting all of:
- Year >= 1983 (Event.Date)
- Investigation.Type == "Accident"
- Amateur.Built == "No" (professional builds)
- Aircraft.Category == "Airplane"
Rows after primary filter: 19,937
Additional cleaning and feature engineering
- Standardized key text fields (Make, Model, factor columns)
- Converted injury columns to numeric and imputed missing injury counts as 0 for row-level totals
- Created:
  - Estimated.Total.Occupants
  - Fatal.Serious.Count
  - Fatal.Serious.Rate = (Fatal + Serious) / Estimated.Total.Occupants
  - Was.Destroyed (1 if Aircraft.damage == "Destroyed", else 0)
  - Make.Clean, Model.Clean, Plane.Type
- Kept makes with at least 50 events for make-level stability in cleaning phase
Final cleaned dataset: 16,539 rows, 36 columns  
Rows with valid injury-rate denominator used in EDA: 16,337  
- Small group (<=20 occupants): 15,875
- Large group (>20 occupants): 462
---
### Metrics Used
1) Fatal/Serious Injury Fraction
2) Destruction Rate
3) Combined Safety Score
Equal-weight average of injury and destruction rates:
Lower is better.
---
### Key Findings
A) Baseline by aircraft size
  - Large (>20): mean injury rate 0.1019, destruction rate 0.0996
  - Small (<=20): mean injury rate 0.2927, destruction rate 0.1102
#### Interpretation: large-aircraft accidents in this filtered sample show much lower mean fatal/serious injury fraction.
B) Make-level results
Small aircraft (<=20)
Top combined-risk makes (n >=20):
1. AVIAT AIRCRAFT INC (n=76, injury=0.1645, destroy=0.0395)
2. MAULE (n=215, injury=0.1648, destroy=0.0419)
3. GRUMMAN ACFT ENG COR-SCHWEIZER (n=58, injury=0.2069, destroy=0.0172)
4. STINSON (n=129, injury=0.2164, destroy=0.0233)
5. AERONCA (n=200, injury=0.2258, destroy=0.0350)
#### Additional note: CESSNA has a larger risk estimate than the top small-make group, but very high sample support (n~6,959), which improves estimate stability.
Large aircraft (>20)
Top combined-risk makes (n >=20):
1. MCDONNELL DOUGLAS (n=38, injury=0.0109, destroy=0.1053)
2. EMBRAER (n=51, injury=0.0995, destroy=0.0784)
3. BOEING (n=297, injury=0.0993, destroy=0.0909)
4. AIRBUS (n=68, injury=0.1439, destroy=0.1324)
Interpretation: BOEING and EMBRAER are strongest large-segment candidates when balancing risk and sample size.
#### C) Plane-type highlights (n >=10)
Large plane types
- BOEING | 757: injury 0.0027, destroy 0.0000 (n=10)
- EMBRAER | EMB-145LR: injury 0.0150, destroy 0.0000 (n=10)
- BOEING | 777: injury 0.0023, destroy 0.0833 (n=12)
- BOEING | 737: injury 0.1731, destroy 0.1447 (n=76)
Interpretation: some very low-risk large models have small n; BOEING | 737 offers higher statistical stability due to much larger n.
Small plane types
Examples with low combined risk include:
- MAULE | M-5-210C (n=11)
- CESSNA | 172SP (n=12)
- DIAMOND AIRCRAFT IND INC | DA 20 C1 (n=11)
Interpretation: many low-risk small-type estimates have modest sample sizes; use with caution.
---
### Factor Analysis
1) Engine Type (robust groups n >=30)
    - Turbo Fan: lowest injury risk (0.1347), destruction 0.0975
    - Reciprocating: lower destruction (0.0789) but higher injury (0.2560)
    - Unknown: worst outcomes (injury 0.4648, destruction 0.2474)
Interpretation: complete and specific engine labeling is important; unknown classifications are strongly associated with poorer safety outcomes.
2) Purpose of Flight (robust groups n >=30)
    - Instructional: best profile (injury 0.1709, destruction 0.0420, n=2,382)
    - Personal: injury 0.2813, destruction 0.0939, very large n (9,765)
    - Unknown: poor profile (injury 0.4137, destruction 0.2446)
    - Banner Tow: low destruction but high injury fraction, suggesting risk tradeoff by mission type
Interpretation: mission context materially affects risk and should be included in underwriting and operational policy.
---
### Recommendations
#### For small-aircraft segment (<=20 occupants)
Prioritize makes with favorable combined risk and reasonable sample size:
- AVIAT AIRCRAFT INC
- MAULE
- GRUMMAN ACFT ENG COR-SCHWEIZER
- STINSON
- AERONCA
#### For large-aircraft segment (>20 occupants)
Primary candidates:
- BOEING (strong mix of performance and sample size)
- EMBRAER
- MCDONNELL DOUGLAS (good injury rate but smaller sample than Boeing)
- AIRBUS (included but with weaker risk profile than Boeing/Embraer in this sample)
Operational risk controls
- Treat Engine.Type = Unknown and Purpose.of.flight = Unknown as elevated-risk flags.
- Incorporate purpose-of-flight risk adjustment into insurance pricing and safety policy.
---
### Limitations
1. Accident-only dataset (not all flights): cannot estimate true per-flight incident probability.
2. Occupant-based size split (<=20 vs >20) uses observed accident occupancy, not certified design capacity.
3. Some category groupings have small sample sizes; low averages may be unstable.
4. Make/model naming inconsistencies may remain (DEHAVILLAND vs DE HAVILLAND, AVIAT vs AVIAT AIRCRAFT INC).
5. Missing/unknown values can be informative but also introduce measurement uncertainty.
---
### License
This project is licensed under Apache 2.0 (see LICENSE).
