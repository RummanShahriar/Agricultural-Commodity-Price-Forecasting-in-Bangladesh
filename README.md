Here is the revised `README.md` without emojis, more detailed, professional, crisp, and robust.

```markdown
# Agricultural Commodity Price Forecasting in Bangladesh

## Overview

This repository provides the official implementation and replication package for the paper:

**"Agricultural Commodity Price Forecasting in Bangladesh: A Comparative Multi-Model Study with Statistical Significance Testing"**  
*Accepted at ICEFONT 2026.*

The study benchmarks eight forecasting models—ARIMA, Support Vector Regression (SVR), Multi-Layer Perceptron (MLP), Simple Recurrent Neural Network (RNN), Long Short-Term Memory (LSTM), Gated Recurrent Unit (GRU), XGBoost, and Echo State Network (ESN)—using weekly wholesale price data of six agricultural commodities in Bangladesh. Data are sourced from the Bangladesh Directorate of Agricultural Marketing (DAM) covering January 2022 to February 2026 across all eight administrative divisions, 64 districts, and more than 400 market locations. All model comparisons are validated with Diebold-Mariano statistical significance tests.

The key outcomes indicate that GRU achieves the lowest MAPE for three commodities (Onion, Gram, Egg Duck-Local), while RNN records the lowest average MAPE (12.94%) across all series. Deep learning models demonstrate statistically significant superiority (p < 0.05) over ARIMA and SVR on high-volatility series (CV > 0.4).

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

Each CSV file contains weekly wholesale price records for a single commodity. The raw data are obtained from the Bangladesh Directorate of Agricultural Marketing (DAM). Preprocessing steps (as detailed in the paper) include:

- Removal of duplicate records.
- Parsing of weekly minimum and maximum wholesale prices.
- Computation of the midpoint wholesale price:  
  `Price = (Wholesale Min + Wholesale Max) / 2`.
- Construction of a datetime index from Year, Month, and Week fields.
- Min-Max normalization to [0,1] fitted exclusively on the training set to prevent data leakage.

Stationarity is assessed using both Augmented Dickey-Fuller (ADF) and KPSS tests. The dataset spans a four-year period (2022–2026) that captures post-COVID supply chain recovery, the 2022–2023 energy and import price shock, and the 2024–2025 price normalisation phase.

The six commodities and their volatility characteristics (coefficient of variation, CV) are:

| Commodity            | Category   | CV Range  | Volatility Level |
|----------------------|------------|-----------|------------------|
| Onion (local)        | Spice      | > 0.4     | High             |
| Tomato               | Vegetable  | > 0.4     | High             |
| Rice (Boro Hybrid)   | Cereal     | ~ 0.095   | Low              |
| Gram (broken)        | Pulse      | ~ 0.30    | Medium           |
| Egg Farm-Red         | Protein    | ~ 0.25    | Medium           |
| Egg Duck-Local       | Protein    | ~ 0.10    | Low              |

## Models Implemented

All models follow a supervised time-series formulation: given the last T lagged price observations (T = 10 weeks), predict the next price (one-step-ahead forecasting). No external covariates (weather, trade volumes, policy signals) are used; only lagged prices serve as input features.

| Model      | Type                 | Key Configuration / Hyperparameters                                                                 |
|------------|----------------------|-------------------------------------------------------------------------------------------------------|
| ARIMA      | Statistical          | Order (p,d,q) selected via AIC using `pmdarima.auto_arima`; differencing order d from ADF test.      |
| SVR        | Kernel-based         | RBF kernel; C ∈ {0.1,1,10,100}, ε ∈ {0.01,0.1,0.5}; 5-fold time-series cross-validation.            |
| MLP        | Feed-forward NN      | Two hidden layers (64,32), ReLU activation; Adam (lr=0.001); early stopping (patience=10).           |
| RNN        | Simple recurrent NN  | One hidden layer (128 units), dropout 0.2, linear output; Adam (lr=0.001); early stopping.           |
| LSTM       | Long Short-Term Memory| One hidden layer (128 units), dropout 0.2; Adam (lr=0.001); early stopping.                          |
| GRU        | Gated Recurrent Unit | One hidden layer (128 units), dropout 0.2; Adam (lr=0.001); early stopping.                          |
| XGBoost    | Tree boosting        | 500 trees, learning rate 0.1, max depth 6, L1=0.1, L2=1.0, subsample 0.8; early stopping after 100 rounds. |
| ESN        | Reservoir computing  | Reservoir size 500, spectral radius 0.9, connectivity 0.1; ridge regression readout (λ = 1e-6).       |

For recurrent models (RNN, LSTM, GRU), batch size is 64, and training runs for up to 50 epochs with early stopping on a 20% validation split. ARIMA uses a fixed-origin forecast: the model is trained once on the first 80% of the data and then applied to the entire test set.

## Evaluation Metrics

Four complementary metrics are reported for each model-commodity pair:

1. **RMSE** (Root Mean Squared Error) – sensitive to large errors.
2. **RNMSE** (RMSE normalized by the series mean) – enables cross-commodity comparison.
3. **MAE** (Mean Absolute Error) – average absolute deviation.
4. **MAPE** (Mean Absolute Percentage Error, %) – intuitive percentage error.

All pairwise model comparisons are subjected to the **Diebold-Mariano test** (p < 0.05) to determine whether observed differences in predictive accuracy are statistically significant rather than due to sampling variation.

## Key Findings (Summary)

| Commodity        | Best Performing Model | MAPE (%) |
|------------------|-----------------------|----------|
| Onion (local)    | GRU                   | 17.59    |
| Tomato           | RNN                   | 20.20    |
| Rice (Boro Hybrid)| GRU                  | 1.30     |
| Gram (broken)    | GRU                   | 10.50    |
| Egg Farm-Red     | MLP                   | 11.16    |
| Egg Duck-Local   | GRU                   | 1.87     |

Additional observations:

- GRU achieves the lowest MAPE on three commodities (Onion, Gram, Egg Duck-Local).
- RNN yields the lowest average MAPE (12.94%) across all six commodities.
- ARIMA performs competitively on low-volatility commodities (Rice MAPE: 1.91%; Duck Egg: 3.41%) but fails on high-volatility vegetables.
- Diebold-Mariano tests confirm that deep learning models (GRU, RNN, LSTM) significantly outperform ARIMA and SVR on high-volatility series (p < 0.05).

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
   - `Each_commodity_EDA_+_Each_Model_Result_Analysis.ipynb`: Performs exploratory data analysis, stationarity tests (ADF/KPSS), and per-commodity model evaluation.
   - `Final_Pipeline_(Conference_Paper).ipynb`: Executes the complete end-to-end pipeline including data loading, preprocessing, model training, evaluation, Diebold-Mariano tests, and generation of result tables and figures (MAPE heatmap, GRU prediction plots).

All output tables and figures will match those presented in the paper.

## Dependencies

The environment requires the following core libraries (versions used in the original experiments are noted in parentheses, but later versions are expected to work):

- Python 3.8+
- pandas (1.5+)
- numpy (1.23+)
- scikit-learn (1.2+)
- statsmodels (0.14+)
- pmdarima (2.0+)
- tensorflow (2.12+)
- xgboost (1.7+)
- matplotlib (3.6+)
- seaborn (0.12+)

## Citation

If you use this code, data, or findings in your research, please cite the original paper:

```bibtex
@inproceedings{rumman2026agricultural,
  title     = {Agricultural Commodity Price Forecasting in Bangladesh: A Comparative Multi-Model Study with Statistical Significance Testing},
  author    = {Rumman Shahriar and [Full author names as per final paper]},
  booktitle = {International Conference on Emerging Frontiers in Networking and Telecommunications (ICEFONT)},
  year      = {2026}
}
```

## Acknowledgments

This work was supported by the Improving Computer and Software Engineering Tertiary Education Project (ICSETEP) under the University Grants Commission (UGC) and the Secondary and Higher Education Division (SHED) of the Ministry of Education, Dhaka, Bangladesh (Sub-project PIN: C-53). The authors thank the Bangladesh Directorate of Agricultural Marketing (DAM) for providing the wholesale price data.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.

## Contact

**Rumman Shahriar**  
GitHub: [RummanShahriar](https://github.com/RummanShahriar)  
For academic inquiries, please refer to the contact information in the paper.
```

This version removes all emojis, expands details on dataset preprocessing, model configurations, evaluation protocol, and reproducibility steps, while maintaining a crisp and professional tone.
