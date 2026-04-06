# Chiba Condo Appreciation Classifier
### Identifying Municipal-Level Investment Opportunities in the Japanese Real Estate Market

---

## Overview

This project uses Japanese real estate transaction data published by the 
Ministry of Land, Infrastructure, Transport and Tourism (MLIT) to build 
an investment screening tool for the Chiba condominium market.

Rather than predicting exact prices, the model asks a more useful question:

> *Which Chiba municipalities show structural conditions associated with 
> above-median price appreciation in the following year?*

The output is a ranked opportunity index assigning each of 28 municipalities 
a predicted probability of above-median appreciation — a shortlist for 
further due diligence rather than a precise price forecast.

---

## Notebook

### `chiba_mansion_prediction.ipynb`

An end-to-end pipeline covering:
- Data cleaning and feature engineering from raw MLIT transaction data
- Municipality-year panel aggregation
- XGBoost binary classification with time-series cross-validation
- Opportunity index generation and ranking
- Interactive choropleth map (Folium)
- Animated time-series map (Plotly)
- Multi-year appreciation signal heatmap

**Results:**
- Mean accuracy of 57.5% and AUC of 0.635 across five time-series folds
- Meaningfully above the 50% random baseline for a balanced classification task

**Key finding:**  
High-probability municipalities share a consistent profile — aging building 
stock, moderate transit access, and mid-range pricing. These are structural 
conditions associated with pre-gentrification markets where renovation-driven 
demand has not yet fully repriced the area.

Hanamigawa Ward and Kamagaya City show persistently strong signals across 
most years. Funabashi City and Kashiwa City consistently score low despite 
being the highest-volume markets, suggesting full pricing relative to 
fundamentals throughout the period.

---

## Data

## Data

**Source:** MLIT Land General Information System (土地総合情報システム)  
Available at: https://www.land.mlit.go.jp/webland/  
Kaggle mirror: https://www.kaggle.com/datasets/nishiodens/japan-real-estate-transaction-prices

**Coverage:** 2005–2019, Chiba Prefecture, ~180,000 transactions  
**License:** Japanese government open data — free for research and commercial use

The Chiba Prefecture transaction file (`trade_prices/12.csv`) is included 
in this repository. No additional download required to run the notebook.
```

---

## Geographic Data

Municipal boundary GeoJSON for Chiba Prefecture is required for the 
choropleth map visualizations.

**Source:** MLIT National Land Information Division (国土数値情報)  
Administrative boundary data (行政区域)  
Available at: https://nlftp.mlit.go.jp/ksj/gml/datalist/KsjTmplt-N03.html

Place the file in the root directory and update the filename in the 
geographic visualization cells if needed.

---

## Requirements
```bash
pip install pandas numpy matplotlib seaborn japanize-matplotlib \
            scikit-learn xgboost geopandas folium plotly
```

Python 3.10 or higher recommended.

---

## Repository Structure
```
chiba-condo-classifier/
│
├── trade_prices/
│   └── 12.csv    ← included
│
├── chiba_mansion_prediction.ipynb          # Main notebook
│
├── N03-21_12_210101.json                   # Chiba municipal boundary GeoJSON
├── chiba_appreciation_index_2018.csv       # 2018 opportunity index
├── chiba_appreciation_index_all_years.csv  # Full multi-year index
├── chiba_opportunity_map_2018.html         # Interactive Folium map
├── chiba_opportunity_animated.html         # Animated Plotly map
├── chiba_appreciation_heatmap.png          # Heatmap visualization
├── chiba_appreciation_importance.png       # Feature importance chart
│
└── README.md
```

---

## Key Technical Decisions

**Why binary classification instead of regression?**  
An initial regression model predicting exact year-over-year price change 
produced a mean R² of -0.05 — worse than predicting the mean for every 
observation. With only 290 municipality-year observations, regression on 
a noisy continuous target is unreliable. Binary classification asks a more 
honest and more useful question: which markets will outperform the median?

**Why TimeSeriesSplit instead of standard cross-validation?**  
Standard k-fold cross-validation randomly shuffles data, allowing the model 
to train on 2018 data to predict 2012 outcomes — data leakage that inflates 
performance metrics. TimeSeriesSplit enforces temporal order, matching real 
deployment conditions.

**Why municipality-level aggregation?**  
Individual transactions reflect property-specific characteristics as much 
as market conditions. Aggregating to municipality-year panels extracts the 
market-level signals relevant to investment screening: price momentum, 
building stock age, renovation activity, and transit access.

---

## Limitations

- Transaction data ends 2019 — does not capture post-COVID market dynamics
- No external economic signals (interest rates, population flows, 
  infrastructure announcements)
- Municipality-level resolution only — cannot identify opportunities 
  within a municipality
- Requires updated MLIT data (2020–present) to generate current predictions

---

## Author

**Background:** Anthony DiSorbo, MA TESOL, Google Data Analytics Certificate (Advanced)  
**Location:** Chiba Prefecture, Japan
**LinkedIn** https://www.linkedin.com/in/adisorbo/

---

*Built using publicly available Japanese government data. All findings 
are for research and portfolio purposes only and do not constitute 
investment advice.*