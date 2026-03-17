# Premier League 2025/2026 Live Analytics Pipeline

This project presents my complete live-season Premier League analytics workflow: from raw match data, through ETL and SQL, to scouting-oriented insights.

Data source: https://www.football-data.co.uk/

## What I Chose and Why

I chose a notebook + SQL approach because:
- it clearly shows the full analytical workflow step by step,
- transformations are transparent and auditable,
- output tables can be reused directly in downstream analysis.

I chose a live season (2025/2026) because it is both more challenging and more realistic.
In a live season, teams may have played a different number of matches, so per-match metrics and a fair ranking approach are essential.

## How It Works

1. Load data from `E0-2.csv`.
2. Clean and standardize columns (goals, shots, corners, cards, etc.).
3. Create key features:
- `HomePoints`, `AwayPoints`
- `HalfTimeResult`
4. Map team names to `TeamID`.
5. Aggregate home and away statistics into the `Standings` table.
6. Persist results to SQLite (`Matches`, `Teams`, `Standings`).
7. Run SQL and visual analysis across four analytical pillars.

## Why These 4 Analyses

### 1) Offensive Profiling (Quadrant)
What it measures:
- `AvgShots` (volume),
- `ConversionRate` (finishing efficiency),
- `AvgCorners` (territorial pressure).

Why I chose it:
- combines attacking quantity and quality,
- helps compare offensive team profiles.

How it works:
- unions home and away records,
- calculates metrics per team,
- reference lines weighted by matches played.

### 2) Pythagorean Expectation (xPts vs Actual)
What it measures:
- whether points are aligned with process quality (GF/GA),
- `Luck = ActualPoints - xPts`.

Why I chose it:
- the table alone can be misleading during a live season,
- this model helps separate outcomes from underlying performance.

How it works:
- base model uses `GoalsFor` vs `GoalsAgainst` (exponent 1.3),
- team draw rate is shrunk toward the league average:

$$
p_{draw,team} = \frac{Draws + k\cdot p_{draw,league}}{Played + k}
$$

- in this project, `k = 6`.
- rationale: with roughly 30+ matches played per team, `k = 6` provides moderate shrinkage (stability against noise) without overpowering each team's observed draw profile.

### 3) Halftime -> Fulltime Flow (Sankey)
What it measures:
- transitions from halftime state to fulltime result.

Why I chose it:
- gives a fast view of when matches are most volatile,
- supports conclusions about game management.

How it works:
- groups by `HalfTimeResult` and `HomePoints`,
- maps states into Sankey nodes.

### 4) Locker Room Impact (2nd-half net goals)
What it measures:
- average team net goal difference after halftime.

Why I chose it:
- strong proxy for coaching adaptation and second-half management.

How it works:
- calculates home and away perspectives separately,
- combines both perspectives,
- computes average per team.

## Why Two Table Rankings

I intentionally keep two rankings:
- `Position`: official league logic (Points, GD, GF),
- `PositionPPG`: fairness ranking for a live season (unequal matches played).

This preserves football realism while maintaining analytical comparability.

## Stack

- Python: Pandas, NumPy
- SQLAlchemy + SQLite
- Matplotlib, Seaborn, Plotly
- adjustText

## How to Run

1. Activate a Python environment.
2. Install dependencies:

```bash
pip install pandas sqlalchemy matplotlib seaborn numpy adjustText plotly
```

3. Run `pipeline.ipynb` from the first cell to the last.
4. The `premier_league.db` database will be created locally.

## Author

Tymoteusz Drewniak
