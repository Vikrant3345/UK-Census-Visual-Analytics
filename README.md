<div align="center">

# 📊 UK Census Visual Analytics (2011–2021)

### Exploring socio-economic patterns and changes in self-reported health across England and Wales

**MSc Data Science · Visual Analytics (EMATM0066) · University of Bristol**

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![Tableau](https://img.shields.io/badge/Tableau-1F4E79?style=flat-square)
![Scikit--learn](https://img.shields.io/badge/Scikit--learn-F7931E?style=flat-square&logo=scikitlearn&logoColor=white)
![UK Census](https://img.shields.io/badge/Data-2011%20%26%202021%20Census-316B80?style=flat-square)

</div>

## Overview

Census tables contain detailed information about health, employment, education and housing, but their size and structure can make comparisons difficult. This project combines **data preparation, exploratory statistical analysis and interactive Tableau dashboards** to investigate how socio-economic and health patterns differ across local authorities in **England and Wales**.

The analysis uses **2011 Census data** for its socio-economic baseline and **2021 Census general-health data** for cross-year comparisons and uncertainty-aware estimates.

> **Research question:** How can visual analytics help reveal regional socio-economic patterns, health inequalities and changes in self-reported health across England and Wales between the 2011 and 2021 Census?

**At a glance**

| Area | Approach |
| --- | --- |
| Data preparation | Standardise geography keys; merge datasets; engineer comparable rate-based features |
| Exploratory analysis | Principal Component Analysis (PCA), t-SNE and K-Means clustering |
| Model-based estimation | Bayesian Ridge Regression with approximate uncertainty bounds |
| Dashboard | Tableau KPIs, interactive filters, comparison charts, projections and choropleth maps |
| Geographic scope | England and Wales, primarily at local-authority level |

## Dashboard Gallery

The repository includes a Tableau packaged workbook (`.twbx`) and screenshots of its main analytical views. Screenshots are static previews; the packaged workbook contains the interactive dashboard.

### 1. Socio-Economic Overview · 2011 Census

![Socio-Economic Overview dashboard](tableau_viz/socioeconomic_overview.png)

An introduction to the 2011 baseline using employment, education, health and housing KPIs, distribution charts, regional comparisons and geographic filters.

### 2. PCA: Socio-Economic Pattern Analysis

![PCA projection dashboard](tableau_viz/pca_analysis.png)

A two-dimensional linear projection of standardised socio-economic indicators, with exploratory groupings and comparisons to employment and health measures.

### 3. t-SNE: Local Similarity Exploration

![t-SNE projection dashboard](tableau_viz/tsne_analysis.png)

A non-linear view of similarities among local authorities, supported by cluster labels and socio-economic comparisons. t-SNE positions are **exploratory**: plotted distances and groups should not be interpreted as precise geographic or causal relationships.

### 4. Health Change & Uncertainty · 2011 vs 2021

![2011 and 2021 health comparison dashboard](tableau_viz/health_comparison.png)

A comparison of self-reported health categories across census years, using regional charts, geographic patterns and an uncertainty view for model-estimated 2021 unhealthy-health proportions.

## Data Sources & Preparation

The project uses five source datasets from **[Nomis](https://www.nomisweb.co.uk/)**, drawing on Office for National Statistics Census releases:

| Census year | Dataset | Role |
| --- | --- | --- |
| 2011 | General health | Health categories and derived health measures |
| 2011 | Economic activity | Employment and economic inactivity indicators |
| 2011 | Housing tenure | Home ownership and private renting indicators |
| 2011 | Qualifications | Education and qualification indicators |
| 2021 | General health | Follow-up health proportions for cross-year analysis |

The Python notebook:

1. Harmonises geographic column names and joins the four 2011 tables using shared geographic identifiers.
2. Reshapes the 2021 health categories into a local-authority-level table.
3. Constructs comparable ratios, including healthy/unhealthy proportions, employment rate, ownership rate and higher-qualification rate, alongside a weighted health index.
4. Joins 2011 features to 2021 health outcomes by area code and prepares the modelling data.
5. Exports the projection and master analysis tables for Tableau.

The merged 2011 analysis contains **348 local-authority records**. Of these, **323** have the 2021 outcome available for Bayesian model training; **25** have a missing matched 2021 outcome in the notebook's combined data. The project report identifies local-authority boundary changes as one reason to treat cross-year comparisons carefully.

**Two types of CSV data are included intentionally:** `analysis_folder/` contains the source tables used by the Python pipeline; `tableau_viz/files_used_in_viz/` contains separately pivoted Tableau inputs and the prepared output tables. Some Tableau-specific reshaping was carried out outside the main notebook, so not every workbook source is recreated by simply running the notebook.

## Methods

### Feature Engineering

Population counts were converted into proportions to reduce distortions from different local-authority population sizes. Variables include health, employment and inactivity, housing tenure and educational attainment indicators.

### PCA and t-SNE

The selected 2011 features were standardised with `StandardScaler` before dimensionality reduction:

- **PCA:** Two components explain approximately **77.77%** of variance in the selected features (PC1: **58.67%**, PC2: **19.10%**).
- **t-SNE:** A two-dimensional non-linear projection (`perplexity=30`, `random_state=42`) was used to explore local similarity patterns.
- **K-Means:** Three clusters were applied to the PCA coordinates and four to the t-SNE coordinates for exploratory visual grouping. These labels are descriptive, not verified natural categories.

### Bayesian Health Estimation

`BayesianRidge` was fitted using the available 2021 unhealthy-health proportions as the target and selected 2011 socio-economic indicators as predictors. It generated predictions for **348** local-authority records; the notebook also calculates approximate bounds as:

`prediction ± 1.96 × predicted_standard_deviation`

These model outputs support uncertainty visualisation. **They are not official Census measurements**, and the coursework did not establish predictive accuracy on a separate held-out test set.

## Findings & Evaluation

- The dashboards reveal **regional variation** across employment, education, housing and self-reported health indicators.
- The PCA projection offers an interpretable summary of broad variation in selected socio-economic features; the t-SNE projection presents a complementary view of local similarities.
- The Bayesian estimates show relatively limited variation across local authorities, which the assessment discusses as a limitation of the uncertainty view.
- A **formative classroom evaluation with five peers** found the overview, KPI cards and maps comparatively easy to understand. Two peers directly interacted with the dashboard. PCA/t-SNE and uncertainty views needed more explanation.

These findings come from exploratory analysis and formative feedback, not from causal inference or a controlled usability study.

## Repository Structure

```text
UK-Census-Visual-Analytics/
├── README.md
├── .gitignore
├── requirements.txt                      # Add alongside README
├── analysis_folder/
│   ├── [Jupyter analysis notebook].ipynb
│   ├── 2021_UK_health_Census_Data.csv
│   └── [four 2011 Census input CSV files]
├── tableau_viz/
│   ├── [Tableau packaged workbook].twbx
│   └── files_used_in_viz/
│       ├── final_master_tableau.csv
│       ├── final_projection.csv
│       └── [supporting Tableau-ready CSV files]
├── report/
│   └── uk_census_analytics_report.pdf
└── visualizations/
    ├── 01_socioeconomic_overview.png
    ├── 02_pca_analysis.png
    ├── 03_tsne_analysis.png
    └── 04_health_comparison.png
```

*The notebook and workbook filenames are abbreviated in this overview; see their respective folders for the files.*

## How to Explore the Project

**View the results:** Browse the [dashboard screenshots](visualizations/) above, or open the `.twbx` file from [`tableau_viz/`](tableau_viz/) in a compatible version of Tableau Desktop.

**Run the Python preparation notebook:**

```bash
# From the repository root
python -m pip install -r requirements.txt
cd analysis_folder
jupyter notebook
```

Open the notebook in the browser and run its cells in order. The input CSV filenames are referenced relatively, so start Jupyter **from `analysis_folder/`**. The notebook writes `final_master_tableau.csv` and `final_projection.csv` to its working directory. Those generated files can be compared with the Tableau-ready copies in `tableau_viz/files_used_in_viz/`.

**Reproducibility note:** The repository preserves coursework analysis and visualisation inputs, but the full Tableau source preparation includes separately pivoted files. The saved notebook also contains an earlier file-loading error output. Re-execution and Tableau datasource connections should be checked before treating the entire workflow as fully reproducible.

## Limitations

- **Census comparability:** Changes to local-authority boundaries and available categories can affect 2011–2021 matching and comparison.
- **Observational analysis:** Relationships between indicators do not establish causal effects.
- **Projection interpretation:** PCA and t-SNE reduce information; cluster counts are exploratory choices rather than externally validated classifications.
- **Model validation:** The Bayesian model illustrates uncertainty, but separate held-out predictive testing and calibration were not demonstrated in the submitted analysis.
- **Dashboard evaluation:** The peer feedback is formative and based on a small classroom group, not a controlled usability study.
- **Reproducibility:** The Tableau workbook includes prepared sources that are not all regenerated by the notebook alone.

## Project Context & Acknowledgements

Prepared as part of **EMATM0066 Visual Analytics**, MSc Data Science, **University of Bristol**.

- **Census data:** Office for National Statistics, accessed via Nomis. Review the relevant data attribution and reuse terms before redistributing source tables.
- **Visualisation and methods:** Tableau; Munzner's *Visualization Analysis and Design*; Shneiderman's visual information-seeking framework; PCA, t-SNE and Bayesian modelling references discussed in the [coursework report](report/).
- **Maps:** Mapbox / OpenStreetMap attribution is visible in the dashboard screenshots.

**Author:** Vikrant Deshmukh

> **Academic project notice:** This repository documents an MSc assessment. It is intended to demonstrate the analytical workflow and its limitations, not to serve as an official statistical release or production-ready prediction service.
