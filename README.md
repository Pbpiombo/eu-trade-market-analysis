# EU Trade and Labour Market Analysis

An end-to-end data analysis project exploring the relationship between international trade and unemployment across the 27 EU countries, using raw Eurostat data (2002–2025). The project covers the full pipeline: raw data cleaning, loading into a SQLite database, SQL-based exploration, and analysis answering three research questions.

## Data Source
All raw economic datasets (Trade, GDP, and Unemployment) used in this repository were retrieved from the official Eurostat Data Browser.

## Research Questions

1. **Trade openness** — Do countries with greater trade openness (exports + imports as a share of GDP) show different unemployment levels?
2. **Export composition** — Is the share of manufactured goods in a country's exports associated with different unemployment levels?
3. **Economic shocks** — How did trade and unemployment react to the 2008 and 2020 crises, and which countries recovered fastest?

## Key Findings

**Trade volume matters weakly.** Trade openness shows a moderate negative correlation with unemployment (Pearson r = -0.42): more open economies tend to have lower unemployment, but the relationship is weak and dispersed, and likely confounded by country size and geography.

**Composition matters more than volume.** The share of manufactured goods correlates more strongly with unemployment (r = -0.60, robust under Spearman ρ = -0.48) than trade openness does — suggesting that *what* a country exports predicts employment better than *how much* it trades. The two metrics are largely independent (r = +0.22).

<img src="data/charts/manufactured_goods_unemployment.png" width="800">

**Crises differ in nature.** The 2008 financial crisis was deeper (average export shock -19.11%, imports -24.40%) with slow, multi-speed recovery, while the 2020 pandemic was milder (-6.98%) with rapid recovery — most countries returned to pre-crisis levels by 2021. In 2020, trade and unemployment were largely disconnected: the shock hit services and lockdowns rather than merchandise trade, so the countries with the sharpest export collapse (e.g. Cyprus) were not those with the largest unemployment rise.

<img src="data/charts/comparison_export_2008_2020.png" width="800">

## Methodological Choices

- **Trade openness** was computed as (exports + imports) / GDP using *nominal* values for both, ensuring price-basis consistency. The result was verified to hold under real GDP as well (r = -0.42 nominal vs -0.45 real), confirming robustness to the definition.
- **Outlier handling.** Greece and Spain are structural outliers with high unemployment. To avoid distortion, both mean and median were reported, and **Pearson and Spearman** correlations were compared as a robustness check.
- **Shock measurement.** A *peak-to-trough* method was used, measuring the drop from the pre-crisis peak to the post-crisis minimum, with recovery defined as the return to pre-crisis levels. This method's limitations on series with strong pre-existing trends (e.g. Italian and French unemployment, already declining before 2020) were identified through rolling-window analysis and explicitly documented.
- **Data constraint.** Unemployment and GDP data begin in 2016, while trade data begins in 2002. The 2008 crisis was therefore analysed on trade only, and cross-dataset analyses were aligned to the common 2016–2025 window.

## Tech Stack

Python · pandas · SQLite · SQL · matplotlib / seaborn · scipy

### 1. Dependencies 

Ensure you have all the required libraries installed. Since the repository includes a `requirements.txt` file, you can install everything automatically via pip:

```bash
pip install -r requirements.txt
```

### 2. Execution Order

To replicate the full analysis pipeline, execute the notebooks sequentially across their respective directories:

1. **`01-pre_exploration.ipynb`** — Run this first at the root folder to process and pre-clean the raw Eurostat text data.
2. **`02-diagnostic_exploration/`** — Run all three diagnostic notebooks inside this folder to complete data verification and build/populate the SQLite database (`ue_market.db`).
3. **`03-descriptive_analysis/`** — Execute the exploration scripts sequentially (`01`, `02`, `03`) to run baseline database schema checks and aggregate dimensions.
4. **`04-research_questions/`** — Run these final notebooks to generate analytical tables, evaluate the core hypotheses, and render the output charts.

## Repository Structure

- **`01-pre_exploration.ipynb`**  — initial exploration and pre-cleaning of the raw dataframes.
- **`02-diagnostic_exploration/`** — full diagnostic and cleaning phase, saving outputs to both `data/clean_data/` and the database:
  - `disoccupation_diagnostic_exploration.ipynb`
  - `gdp_diagnostic_exploration.ipynb`
  - `InEx_Eu_trade_diagnostic_exploration.ipynb`
- **`03-descriptive_analysis/`** — pre-question database exploration: load checks, dataframe dimensions, temporal limits, and measures by category and dimension:
  - `01_database_exploration.ipynb`
  - `02_dimension_exploration.ipynb`
  - `03_measure_exploration.ipynb`
- **`04-research_questions/`** — one notebook per research question. The first two are primarily SQL-based; the third uses pandas across a broader range of analyses:
  - `01_trade_openness.ipynb`
  - `02_trade_composition.ipynb`
  - `03_shocks.ipynb`
- **`data/`** — all data, from raw to clean, plus charts:
  - `raw_data/` — original Eurostat files (.csv, .tsv)
  - `semi-processed_data/` — intermediate melted files (not yet SQL-ready)
  - `clean_data/` — cleaned, database-ready datasets
  - `charts/` — all saved visualisations
- **`database/`** — the SQLite database (`ue_market.db`).
- **`questions.md`** — the research questions.
- **`requirements.txt`** - core Python libraries and package dependencies.

---

## Conclusion

**Thesis Statement:** The analysis demonstrates that Eurozone employment dynamics are shaped more by the structural composition of exports (the type of goods traded) than by overall trade volumes, resulting in a distinct decoupling between international trade shocks and labor market resilience during localized supply-chain and public-health crises like COVID-19.

*Matteo Papucci — 07/2026*
