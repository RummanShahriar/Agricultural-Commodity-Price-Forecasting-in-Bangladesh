# Agricultural Commodity Price Forecasting in Bangladesh

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Python 3.8+](https://img.shields.io/badge/Python-3.8%2B-blue.svg)](https://www.python.org/)
[![TensorFlow](https://img.shields.io/badge/TensorFlow-2.12+-orange.svg)](https://tensorflow.org)

## Overview

This repository provides the official implementation and replication package for the paper:

> **"Agricultural Commodity Price Forecasting in Bangladesh: A Comparative Multi-Model Study with Statistical Significance Testing"**  
> *Accepted at ICEFONT 2026.*

The study benchmarks eight forecasting models—**ARIMA, SVR, MLP, RNN, LSTM, GRU, XGBoost, and Echo State Network (ESN)**—using weekly wholesale price data of six agricultural commodities in Bangladesh. Data are sourced from the **Bangladesh Directorate of Agricultural Marketing (DAM)** covering January 2022 to February 2026 across all eight administrative divisions, 64 districts, and more than 400 market locations. All model comparisons are validated with **Diebold-Mariano statistical significance tests**.

**Key outcomes:**  
- GRU achieves the lowest MAPE for three commodities (Onion, Gram, Egg Duck-Local).  
- RNN records the lowest average MAPE (12.94%) across all series.  
- Deep learning models demonstrate statistically significant superiority (p < 0.05) over ARIMA and SVR on high-volatility series (CV > 0.4).

## Repository Structure

```
Agricultural-Commodity-Price-Forecasting-in-Bangladesh/
│
├── Dataset/                              # Raw weekly price data (CSV)
│   ├── Onion (local).csv
│   ├── Tomato.csv
│   ├── Rice - Boro - Hybrid - Medium.csv
│   ├── Dal Gram (broken).csv
│   ├── Egg Farm-Red.csv
│   └── Egg Duck-Local.csv
│
├── codes/                                # Jupyter notebooks
│   ├── Each_commodity_EDA_+_Each_Model_Result_Analysis.ipynb
│   └── Final_Pipeline_(Conference_Paper).ipynb
│
├── ICEFONT_2026_Rumman_paper_v1_2.pdf    # Full conference paper
└── README.md                             # This file
```

## Dataset Description

Each CSV file contains weekly wholesale price records for a single commodity. Preprocessing steps (detailed in the paper) include:

- Removal of duplicate records.
- Parsing of weekly minimum and maximum wholesale prices.
- Computation of the midpoint wholesale price:  
  `Price = (Wholesale Min + Wholesale Max) / 2`.
- Construction of a datetime index from Year, Month, and Week fields.
- Min-Max normalization to [0,1] fitted exclusively on the training set to prevent data leakage.

Stationarity is assessed using both **Augmented Dickey-Fuller (ADF)** and **KPSS** tests. The dataset spans a four-year period (2022–2026) capturing post-COVID supply chain recovery, the 2022–2023 energy and import price shock, and the 2024–2025 price normalisation phase.

### Commodity Volatility Characteristics

| Commodity            | Category   | CV Range | Volatility Level |
|----------------------|------------|----------|------------------|
| Onion (local)        | Spice      | > 0.4    | High             |
| Tomato               | Vegetable  | > 0.4    | High             |
| Rice (Boro Hybrid)   | Cereal     | ~ 0.095  | Low              |
| Gram (broken)        | Pulse      | ~ 0.30   | Medium           |
| Egg Farm-Red         | Protein    | ~ 0.25   | Medium           |
| Egg Duck-Local       | Protein    | ~ 0.10   | Low              |

## Models Implemented

All models follow a supervised time-series formulation: given the last **T = 10** lagged price observations, predict the next price (one-step-ahead forecasting). Only lagged prices serve as input features (no external covariates).

| Model      | Type                 | Key Configuration / Hyperparameters                                                                 |
|------------|----------------------|-------------------------------------------------------------------------------------------------------|
| ARIMA      | Statistical          | Order (p,d,q) via AIC (`pmdarima.auto_arima`); differencing order d from ADF test.                   |
| SVR        | Kernel-based         | RBF kernel; C ∈ {0.1,1,10,100}, ε ∈ {0.01,0.1,0.5}; 5-fold time-series CV.                           |
| MLP        | Feed-forward NN      | Two hidden layers (64,32), ReLU; Adam (lr=0.001); early stopping (patience=10).                       |
| RNN        | Simple recurrent NN  | One hidden layer (128 units), dropout 0.2; Adam (lr=0.001); early stopping.                           |
| LSTM       | Long Short-Term Memory| One hidden layer (128 units), dropout 0.2; Adam (lr=0.001); early stopping.                          |
| GRU        | Gated Recurrent Unit | One hidden layer (128 units), dropout 0.2; Adam (lr=0.001); early stopping.                          |
| XGBoost    | Tree boosting        | 500 trees, lr=0.1, max depth=6, L1=0.1, L2=1.0, subsample=0.8; early stopping after 100 rounds.      |
| ESN        | Reservoir computing  | Reservoir size 500, spectral radius 0.9, connectivity 0.1; ridge regression readout (λ = 1e-6).       |

**Training details for recurrent models:** batch size 64, up to 50 epochs with early stopping on a 20% validation split. ARIMA uses a fixed-origin forecast (trained once on first 80% of data).

## Evaluation Metrics

Four metrics are reported for each model-commodity pair:

1. **RMSE** – sensitive to large errors.  
2. **RNMSE** (RMSE / mean) – enables cross-commodity comparison.  
3. **MAE** – average absolute deviation.  
4. **MAPE (%)** – intuitive percentage error.

All pairwise model comparisons are subjected to the **Diebold-Mariano test** (p < 0.05) to assess statistical significance of performance differences.

## Key Findings (Summary)

| Commodity            | Best Model | MAPE (%) |
|----------------------|------------|----------|
| Onion (local)        | GRU        | 17.59    |
| Tomato               | RNN        | 20.20    |
| Rice (Boro Hybrid)   | GRU        | 1.30     |
| Gram (broken)        | GRU        | 10.50    |
| Egg Farm-Red         | MLP        | 11.16    |
| Egg Duck-Local       | GRU        | 1.87     |

**Additional observations:**  
- GRU achieves the lowest MAPE on three commodities.  
- RNN yields the lowest average MAPE (12.94%) across all six commodities.  
- ARIMA performs competitively on low-volatility commodities (Rice MAPE: 1.91%; Duck Egg: 3.41%) but fails on high-volatility vegetables.  
- Diebold-Mariano tests confirm deep learning superiority (p < 0.05) over ARIMA and SVR for high-volatility series.

Complete RMSE, RNMSE, MAE, and MAPE tables (Tables III–VI in the paper) are reproduced by running the provided notebooks.

## Reproducibility Instructions

### Prerequisites

- Python 3.8 or higher  
- Required packages: `numpy`, `pandas`, `matplotlib`, `seaborn`, `scikit-learn`, `statsmodels`, `pmdarima`, `tensorflow`, `xgboost`

### Steps

1. **Clone the repository**  
   ```bash
   git clone https://github.com/RummanShahriar/Agricultural-Commodity-Price-Forecasting-in-Bangladesh.git
   cd Agricultural-Commodity-Price-Forecasting-in-Bangladesh
   ```

2. **Install dependencies**  
   ```bash
   pip install numpy pandas matplotlib seaborn scikit-learn statsmodels pmdarima tensorflow xgboost
   ```

3. **Run the notebooks**  
   - `Each_commodity_EDA_+_Each_Model_Result_Analysis.ipynb` – EDA, stationarity tests (ADF/KPSS), per-commodity model evaluation.  
   - `Final_Pipeline_(Conference_Paper).ipynb` – end-to-end pipeline: data loading, preprocessing, model training, evaluation, Diebold-Mariano tests, and generation of result tables/figures (MAPE heatmap, GRU prediction plots).

All output tables and figures will match those presented in the paper.

## Dependencies

| Package       | Minimum Version |
|---------------|----------------|
| Python        | 3.8             |
| pandas        | 1.5             |
| numpy         | 1.23            |
| scikit-learn  | 1.2             |
| statsmodels   | 0.14            |
| pmdarima      | 2.0             |
| tensorflow    | 2.12            |
| xgboost       | 1.7             |
| matplotlib    | 3.6             |
| seaborn       | 0.12            |

## Citation

If you use this code, data, or findings in your research, please cite:

```bibtex
@inproceedings{rumman2026agricultural,
  title     = {Agricultural Commodity Price Forecasting in Bangladesh: A Comparative Multi-Model Study with Statistical Significance Testing},
  author    = {Rumman Shahriar and others},
  booktitle = {International Conference on Emerging Frontiers in Networking and Telecommunications (ICEFONT)},
  year      = {2026}
}
```

## Acknowledgments

This work was supported by the **Improving Computer and Software Engineering Tertiary Education Project (ICSETEP)** under the University Grants Commission (UGC) and the Secondary and Higher Education Division (SHED) of the Ministry of Education, Dhaka, Bangladesh (Sub-project PIN: C-53). The authors thank the Bangladesh Directorate of Agricultural Marketing (DAM) for providing the wholesale price data.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Contact

**Rumman Shahriar**  
GitHub: [RummanShahriar](https://github.com/RummanShahriar)  
For academic inquiries, please refer to the contact information in the paper.
```
