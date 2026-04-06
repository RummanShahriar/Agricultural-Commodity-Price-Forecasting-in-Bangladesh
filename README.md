```markdown
# Agricultural Commodity Price Forecasting in Bangladesh

## Overview

This repository provides the official implementation and replication package for the paper:

**"Agricultural Commodity Price Forecasting in Bangladesh: A Comparative Multi-Model Study with Statistical Significance Testing"**  
*Accepted at ICEFONT 2026.*

The study benchmarks eight forecasting models: ARIMA, Support Vector Regression (SVR), Multi-Layer Perceptron (MLP), Simple Recurrent Neural Network (RNN), Long Short-Term Memory (LSTM), Gated Recurrent Unit (GRU), XGBoost, and Echo State Network (ESN). It uses weekly wholesale price data of six agricultural commodities in Bangladesh collected from the Directorate of Agricultural Marketing (DAM) covering January 2022 to February 2026 across all divisions, districts, and market locations.

Key results show that GRU achieves the lowest MAPE for three commodities (Onion, Gram, Egg Duck-Local), while RNN provides the best overall average performance (12.94%). Deep learning models significantly outperform ARIMA and SVR on high-volatility series (p < 0.05).

---

## Repository Structure

```

Agricultural-Commodity-Price-Forecasting-in-Bangladesh/

├── Dataset/
│   ├── Onion_local.csv
│   ├── Tomato.csv
│   ├── Rice_Boro_Hybrid_Medium.csv
│   ├── Dal_Gram_broken.csv
│   ├── Egg_Farm_Red.csv
│   └── Egg_Duck_Local.csv
│
├── codes/
│   ├── Each_commodity_EDA_Model_Analysis.ipynb
│   └── Final_Pipeline_Conference.ipynb
│
├── paper.pdf
└── README.md

````

---

## Dataset Description

Each dataset contains weekly wholesale prices for a single commodity.

### Preprocessing Steps

- Removed duplicate records  
- Computed midpoint price:  
  `Price = (Wholesale Min + Wholesale Max) / 2`  
- Created datetime index from Year, Month, Week  
- Applied Min-Max normalization on training data only  

Stationarity is tested using ADF and KPSS.

### Commodity Characteristics

| Commodity        | Category   | Volatility |
|------------------|------------|------------|
| Onion (local)    | Spice      | High       |
| Tomato           | Vegetable  | High       |
| Rice (Hybrid)    | Cereal     | Low        |
| Gram (broken)    | Pulse      | Medium     |
| Egg Farm-Red     | Protein    | Medium     |
| Egg Duck-Local   | Protein    | Low        |

---

## Models Implemented

All models use lag-based supervised learning (T = 10 weeks).

| Model   | Type        | Key Details |
|--------|-------------|------------|
| ARIMA  | Statistical | Auto-selected (p,d,q) |
| SVR    | ML          | RBF kernel |
| MLP    | Neural Net  | Dense layers (64,32) |
| RNN    | Deep Learning | 128 units |
| LSTM   | Deep Learning | 128 units |
| GRU    | Deep Learning | 128 units |
| XGBoost| Boosting    | 500 trees |
| ESN    | Reservoir   | Size 500 |

---

## Evaluation Metrics

- RMSE  
- RNMSE  
- MAE  
- MAPE  

Statistical validation is done using the Diebold-Mariano test.

---

## Key Findings

| Commodity      | Best Model | MAPE |
|---------------|------------|------|
| Onion         | GRU        | 17.59 |
| Tomato        | RNN        | 20.20 |
| Rice          | GRU        | 1.30  |
| Gram          | GRU        | 10.50 |
| Egg Farm      | MLP        | 11.16 |
| Egg Duck      | GRU        | 1.87  |

### Insights

- GRU performs best on most commodities  
- RNN gives best average performance  
- ARIMA works well for low volatility data  
- Deep learning models dominate high volatility series  

---

## How to Run

### 1. Clone Repository

```bash
git clone https://github.com/RummanShahriar/Agricultural-Commodity-Price-Forecasting-in-Bangladesh.git
cd Agricultural-Commodity-Price-Forecasting-in-Bangladesh
````

### 2. Install Requirements

```bash
pip install numpy pandas matplotlib seaborn scikit-learn statsmodels pmdarima tensorflow xgboost
```

### 3. Run Notebooks

* `Each_commodity_EDA_Model_Analysis.ipynb`
* `Final_Pipeline_Conference.ipynb`

---

## Dependencies

* Python 3.8+
* pandas
* numpy
* scikit-learn
* statsmodels
* pmdarima
* tensorflow
* xgboost
* matplotlib
* seaborn

---

## Citation

```bibtex
@inproceedings{rumman2026agricultural,
  title     = {Agricultural Commodity Price Forecasting in Bangladesh: A Comparative Multi-Model Study with Statistical Significance Testing},
  author    = {Rumman Shahriar and others},
  booktitle = {ICEFONT},
  year      = {2026}
}
```

---

## Acknowledgments

Supported by ICSETEP (UGC, Bangladesh) and DAM for data access.

---

## License

MIT License

---

## Contact

Rumman Shahriar
GitHub: [https://github.com/RummanShahriar](https://github.com/RummanShahriar)

```
```
