# SupeRR Selector Season 2: Most Reliable Players Under Pressure

**Developed by: Pranav Rai**

## Project Overview

The **SupeRR Selector Season 2** project aims to identify and rank the most reliable IPL and T20I batters and bowlers based on their ability to perform under high-pressure situations.

Developed for **Round 1 of the Rajasthan Royals SupeRR Selector Season 2 challenge**, this project introduces a data-driven methodology to evaluate player performance beyond conventional batting and bowling statistics.

The analysis focuses on three major aspects: performance under pressure, resilience compared to normal playing conditions, and consistency across seasons.

Using ball-by-ball match data, statistical modelling, and multiple robustness checks, the project identifies players who consistently deliver when match situations become challenging.

## Final Results

The following tables present the top 10 batters and bowlers ranked according to their overall performance under pressure.

| Rank | Batter | Bowler |
|:---:|---|---|
| 1 | Tim David | Wanindu Hasaranga |
| 2 | Suryakumar Yadav | Josh Hazlewood |
| 3 | Rajat Patidar | Sunil Narine |
| 4 | Sai Sudharsan | Harshit Rana |
| 5 | Shreyas Iyer | Rashid Khan |
| 6 | Heinrich Klaasen | Varun Chakravarthy |
| 7 | Mitchell Marsh | Jasprit Bumrah |
| 8 | Glenn Phillips | Kagiso Rabada |
| 9 | Daryl Mitchell | Kuldeep Yadav |
| 10 | Dinesh Karthik | Noor Ahmad |

### Dataset Summary

| Metric | Value |
|---|---:|
| Total matches analysed | 769 |
| IPL matches | 352 |
| T20I matches | 417 |
| Total deliveries | 183,736 |
| Data period | 2022 onwards |

## Methodology

The ranking framework consists of four major stages designed to identify players who perform effectively under pressure.

### 1. Identifying High-Pressure Situations

Every delivery is classified as high-pressure, ordinary, or excluded based on three key factors, evaluated from the perspective of the player under pressure.

**Match Leverage**

Measures how significantly a delivery can influence the final match outcome. This is estimated using the expected change in win probability through a logistic regression model.

**Recent Match Conditions**

Evaluates whether the batting side has recently experienced pressure through wickets falling and consecutive dot balls, or whether bowlers have been conceding a high number of runs.

**Performance Relative to Expectations**

Determines whether the batting side is performing ahead of or behind the expected position of a typical team at that stage of the match.

These signals are ranked against deliveries from the same stage of the innings and combined into a single Pressure Index.

- Top 20% of deliveries: High-pressure situations.
- Bottom 50% of deliveries: Ordinary situations.
- Deliveries with either team's win probability above 95%: Excluded.

### 2. Measuring Ball-by-Ball Performance

Each delivery is assigned a performance value based on the difference between the player's actual contribution and the expected contribution of an average player in a comparable situation.

The calculation considers:

- Competition and innings.
- Over and stage of the match.
- Number of balls faced by the batter.
- Opposition strength.

Wickets are converted into their equivalent run value based on the match situation in which they occurred.

This approach allows player performances to be compared fairly across different match conditions.

### 3. Evaluating Player Performance

Each player is evaluated using three key performance indicators.

**Pressure Performance**

Measures the player's average performance value during high-pressure deliveries.

**Resilience**

Measures how much better or worse a player performs under pressure compared to what their ordinary performance would predict.

**Consistency**

Measures the proportion of seasons in which the player performed above the overall player average in high-pressure situations.

To avoid overrating players with limited appearances, empirical Bayes shrinkage is applied to adjust smaller samples towards the overall average. Each performance indicator is weighted according to its measured reliability.

### 4. Combining Multiple Pressure Definitions

To ensure that the rankings are not dependent on a single definition of pressure, the analysis evaluates 15 different configurations.

These configurations combine:

- Five alternative weightings of the three pressure signals.
- Three high-pressure thresholds: the top 15%, 20%, and 30% of deliveries.

The final ranking is calculated by averaging player scores across all 15 configurations.

Additional robustness checks include:

- Changing individual modelling assumptions.
- Bootstrap analysis using 200 match-level samples.
- Split-half reliability testing to evaluate ranking stability.

## Repository Structure

The project is organised into separate scripts, with each script responsible for a specific stage of the analysis.

```text
SupeRR_Selector/
│
├── data/                           # Competition datasets (not included)
│
├── outputs/                        # Generated results and reports
│
├── scripts/
│   ├── build_state.py              # Data cleaning and match-state construction
│   ├── step2_winprob_leverage.py   # Win-probability models and match leverage
│   ├── step2b_try_the_model.py     # Optional chase model testing
│   ├── step3_pressure_signals.py   # Evaluation of candidate pressure signals
│   ├── step4_pressure_index.py     # Expected runs, match strain and Pressure Index
│   ├── step5_ratings.py            # Ball-level performance values and player ratings
│   ├── step6_robustness.py         # Optional sensitivity analysis
│   ├── step7_ensemble.py           # Player scoring across 15 configurations
│   ├── step8_stability.py          # Robustness and ranking stability checks
│   ├── step9_final_tables.py       # Final rankings and stability results
│   └── run_all.py                  # Executes the complete analysis pipeline
│
├── requirements.txt
└── README.md
```

## Dataset Requirements

The competition-provided datasets are not included in this repository.

Before running the analysis, place the following six CSV files inside the `data/` directory:

1. `ball_by_ball.csv`
2. `matches.csv`
3. `competitions.csv`
4. `teams.csv`
5. `playing_eleven.csv`
6. `competition_team_player_mapping.csv`

Ensure that all filenames match exactly so the scripts can locate and process the datasets correctly.

## Installation and Execution

### Prerequisites

- Python 3.10 or later.
- Pip package manager.
- Competition-provided CSV datasets.

### Step 1: Clone the Repository

```bash
git clone <repository-url>
cd SupeRR_Selector
```

### Step 2: Create a Virtual Environment

```bash
python -m venv .venv
```

### Step 3: Activate the Environment

**Linux / macOS:**

```bash
source .venv/bin/activate
```

**Windows:**

```bash
.venv\Scripts\activate
```

### Step 4: Install Dependencies

```bash
pip install -r requirements.txt
```

### Step 5: Execute the Complete Pipeline

```bash
python scripts/run_all.py
```

The complete pipeline typically takes approximately 10–15 minutes, depending on the system configuration.

For a quicker execution with 30 bootstrap samples:

```bash
python scripts/run_all.py 30
```

### Step 6: Access the Results

Once execution is complete, the final rankings and supporting analysis will be available at:

```text
outputs/final_rankings.csv
```

All randomised operations use fixed seeds to ensure reproducibility and consistent results across multiple executions.

## Data Cleaning and Filtering

The original dataset contained 883 matches. Several filtering stages were applied to improve data quality and ensure consistency across the analysis.

| Processing Stage | Matches Remaining | Matches Removed |
|---|---:|---:|
| All supplied matches | 883 | — |
| Retained IPL and full T20I matches; excluded A-team events | 853 | 30 |
| Removed rain-affected matches with revised targets or overs | 819 | 34 |
| Removed matches without a declared winner | 788 | 31 |
| Excluded super-over ties and incomplete run chases | 769 | 19 |

**Final dataset: 769 matches retained for analysis.**

## Limitations and Considerations

Although the methodology incorporates multiple performance indicators and robustness checks, certain limitations should be considered when interpreting the rankings.

**1. Limited Sample Size**

Each player has only a few hundred high-pressure deliveries available for evaluation. Actual pressure-related ability can be difficult to distinguish from random variation.

**2. Ranking Stability**

Split-half agreement is relatively weak, with reliability scores of:

- Batters: 0.10
- Bowlers: 0.22

Consequently, rankings, particularly positions 7–10, should be interpreted cautiously, as relatively small differences in performance may change their positions.

**3. Unmodelled Match Factors**

The current framework does not explicitly account for:

- Rain-affected playing conditions.
- Venue and pitch characteristics.
- Batter-versus-bowler matchups.
- Individual batting roles.

**4. Contextual Differences**

Players operate in different team environments and match situations, which may influence their opportunities and overall performance.

## Conclusion

The SupeRR Selector Season 2 analysis uses ball-by-ball match data, statistical modelling, and multiple robustness checks to identify players who deliver strong performances in high-pressure situations.

Rather than relying solely on conventional batting and bowling statistics, the framework evaluates how effectively players perform when match conditions become challenging, how much they improve or decline relative to their usual performance, and how consistently they deliver across seasons.

The resulting rankings provide a data-driven perspective on pressure performance while acknowledging the uncertainty associated with limited samples and contextual match factors.

## Author

**Pranav Rai**

Developed as part of the Rajasthan Royals SupeRR Selector Season 2 – Round 1 challenge.

---

*This project is based on competition-provided cricket datasets and an independently developed statistical framework for evaluating player performance under pressure.*