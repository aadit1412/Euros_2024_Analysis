# Progressive Passers & Carriers — UEFA EURO 2024 (Event-level Analysis)

This repository contains an event-level football analytics project that identifies and visualizes **progressive passers and progressive carriers** at **UEFA EURO 2024** using StatsBomb event data. The goal is to (1) count progressive actions, (2) map where they originate on the pitch, and (3) compare players using per-90 metrics and spatial visualizations.

---

## Project Context

The analysis was built to answer practical scouting and tactical questions: who in EURO 2024 consistently moves play forward (by pass or carry), where on the pitch they do it, and whether progression is concentrated in a few players or distributed across a team.

Rather than relying on aggregated provider stats alone, this project uses **event-level data** (StatsBomb via `Sbopen`) and implements clear, reproducible definitions for progressive passes and carries. The workflow covers data extraction, tagging of progressive events, minute estimation from event timestamps, aggregation, normalization (per-90), and publication-quality visualizations (heatmaps and scatterplots).

Key aspects:

- Defining and tagging progressive passes and progressive carries from raw coordinates  
- Estimating minutes played per player from event timestamps (first / last event per match)  
- Producing leaderboards (raw and per-90), player heatmaps, and a per-90 scatter comparing passers vs carriers

---

## What's Included

- `euros2024.ipynb` — Full Jupyter Notebook with analysis, visualizations, and commentary.
- `euros2024.py` — Clean Python script version of the notebook workflow. Runs the full pipeline: data load → tagging → aggregation → plots.  
- `euro_plots/` — Exported figures (heatmaps and scatterplots).  
- `README.md` — Project overview and documentation.

> Note: the script expects access to StatsBomb open event data via the `Sbopen()` helper from `mplsoccer`.

---

## Analysis Overview

This is an exploratory / descriptive analysis (no supervised ML). The major steps:

1. **Data load**  
   - Used `Sbopen()` to pull EURO 2024 matches and event data.  
   - StatsBomb coordinates are used (120 × 80 pitch; goal at `(120, 40)`).

2. **Definitions — progressive events**  
   - **Progressive Pass** (completed): a pass is tagged progressive if *any* of these conditions is met:
     - It reduces Euclidean distance to the opponent’s goal by **≥10 units** when starting in own half, or
     - It reduces distance to goal by **≥5 units** when starting in opponent half, or
     - It **ends in the final third** (`end_x >= 80`).
   - **Progressive Carry**: a carry is tagged progressive if *any* of these conditions is met:
     - The carry reduces distance to goal by **≥5 units** and occurs in the opponent’s half, or
     - The carry moves the ball **≥30 units** forward (long progressive carry).

   *These thresholds are configurable in the script if you want to tighten/loosen the definition.*

3. **Minutes estimation**  
   - Converted each event to an **absolute minute** using `period`, `minute`, `second` (1H = minute, 2H = 45 + minute, etc.).  
   - For each player in each match, estimated minutes as `last_event_abs_min - first_event_abs_min`.  
   - Summed across matches to get **total minutes** per player (used for per-90 normalization).  
   - ⚠️ Note: this approximates minutes (may slightly undercount time if a player remained on pitch after their last recorded action).

4. **Aggregation & normalization**  
   - Created leaderboards: raw totals (progressive passes / carries) and per-90 rates.  
   - Applied a **150-minute minimum** when showing per-90 leaderboards to avoid small-sample exaggeration.

5. **Visualizations**  
   - **Heatmaps** of progressive-pass (and progressive-carry) origins for top players (raw counts).  
   - **Scatter plot** (per-90): `progressive passes /90` (x) vs `progressive carries /90` (y) with team coloring — useful to classify pass-dominant vs carry-dominant players.

---

## Metrics & Insights

The code produces the following outputs for EURO 2024:

- **Per-player totals**: total progressive passes and total progressive carries.  
- **Per-90 metrics**: progressive passes/90 and progressive carries/90 (with a 150-minute filter for per-90 displays).  
- **Leaderboards**: top progressive passers and carriers (raw and per-90).  
- **Spatial outputs**:
  - Heatmaps of action origins (12 × 8 bins) — raw counts. 
- **Comparison charts**:
  - Passers-vs-carriers scatter (per-90), colored by team, with outliers labelled.

Example insights you can extract:
- Which players are primary architects of forward progression for their team (total contribution).  
- Which players are most intense/effective in progression when on the pitch (per-90).  
- Spatial tendencies — e.g., fullbacks carrying down the flank vs central midfielders threading progressive passes through the middle.

---

## Visualizations

- **Pitch heatmaps**: `mplsoccer.Pitch` heatmap of progressive pass/carry origins (raw counts).  
- **Scatter (per-90)**: seaborn scatter for each player's passes vs carries.

---
## Contact

If you want to discuss the analysis, suggest improvements, or collaborate on football analytics projects:
- [LinkedIn](https://www.linkedin.com/in/aaditpahuja)  
- 📧 aaditpahuja@gmail.com
