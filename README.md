# S&P 100 Mean-Variance & Portfolio Sensitivity Analysis

A quantitative finance project analyzing S&P 100 equities using **Mean-Variance Portfolio Theory**, **parameter estimation uncertainty**, and **tangency portfolio sensitivity analysis**.

---

## 📌 Project Overview

Modern Portfolio Theory (MPT) relies heavily on estimates of expected returns ($\mu$) and the covariance matrix ($\Sigma$). However, expected return estimates based on historical data contain substantial statistical noise, which can lead to significant changes in optimal portfolio weights.

This project uses approximately 15 years of daily market data for S&P 100 constituents and Treasury bill yields to:

* Estimate daily expected excess returns and their statistical uncertainty.
* Construct an unconstrained tangency portfolio using the inverse covariance matrix.
* Scale the portfolio to a target annualized volatility of **10%**.
* Construct confidence intervals around individual expected return estimates.
* Analyze how changes in a single asset's estimated expected return affect the resulting portfolio allocation.

The analysis focuses on **AAPL** as a case study for parameter sensitivity.

---

## ⚙️ Methodology

### 1. Data Ingestion & Cleaning

* Downloads daily adjusted closing prices for S&P 100 constituents using [`yfinance`](https://github.com/ranaroussi/yfinance).
* Uses data from **January 1, 2010 through December 31, 2024**.
* Downloads the 13-week U.S. Treasury bill yield (`^IRX`) as the risk-free rate.
* Converts the annualized Treasury bill yield from percentage terms into a daily decimal rate:

$$
R_{f,t} = \frac{\text{IRX}_t}{100 \times 252}
$$

* Calculates daily excess returns:

$$
R_{e,i,t} = R_{i,t} - R_{f,t}
$$

* Removes securities with incomplete histories in order to maintain a balanced panel.
* The final dataset contains **92 securities over approximately 3,770 trading days**.

---

### 2. Expected Excess Returns & Standard Errors

For each security, the sample mean daily excess return is calculated as:

$$
\bar{r}_i = \frac{1}{T}\sum_{t=1}^{T} r_{i,t}
$$

The standard error of the sample mean is estimated as:

$$
SE(\bar{r}_i) =
\frac{\sigma(r_i)}{\sqrt{T}}
$$

where:

* $\sigma(r_i)$ is the sample standard deviation of daily excess returns.
* $T$ is the number of observations.

Two-sided 95% confidence intervals are then constructed using the standard normal critical value:

$$
CI_{95\%}
=
\left[
\bar{r}_i - 1.96 \cdot SE(\bar{r}_i),
\;
\bar{r}_i + 1.96 \cdot SE(\bar{r}_i)
\right]
$$

These intervals illustrate the statistical uncertainty surrounding historical expected-return estimates.

---

### 3. Tangency Portfolio Optimization

The project estimates the covariance matrix of daily excess returns:

$$
\Sigma = \operatorname{Cov}(R_e)
$$

The unconstrained tangency portfolio is proportional to:

$$
w \propto \Sigma^{-1}\mu
$$

where:

* $w$ = vector of portfolio weights.
* $\Sigma^{-1}$ = inverse covariance matrix.
* $\mu$ = vector of expected excess returns.

The raw weights are then scaled to target **10% annualized portfolio volatility**.

For daily returns, portfolio variance is:

$$
\sigma_p^2 = w^\top \Sigma w
$$

Annualized volatility is calculated as:

$$
\sigma_{p,\text{annual}}
=
\sqrt{252}\sqrt{w^\top\Sigma w}
$$

The weights are therefore scaled so that:

$$
\sigma_{p,\text{annual}} = 10\%
$$

### Important Assumption

The optimization is **unconstrained**, meaning the portfolio may contain both long and short positions and individual portfolio weights are not subject to maximum-weight constraints.

---

### 4. Parameter Sensitivity Analysis

The project examines how uncertainty in expected-return estimates affects tangency portfolio allocations.

The expected excess return of **AAPL** is varied across its estimated 95% confidence interval while keeping the remaining expected-return estimates unchanged.

For each AAPL return estimate:

1. Replace the original AAPL expected excess return.
2. Recalculate the tangency portfolio:

$$
w(\mu) \propto \Sigma^{-1}\mu
$$

3. Rescale the portfolio to 10% annualized volatility.
4. Record the resulting portfolio weights.
5. Compare the resulting allocation against the baseline portfolio.

This demonstrates how estimation uncertainty in a **single expected-return parameter** can propagate through the portfolio optimization problem and affect allocations across many securities.

---

## 📊 Key Concepts

### Mean-Variance Optimization

Mean-Variance Optimization balances expected portfolio return against portfolio risk.

For a portfolio with weights $w$:

$$
E[R_p] = w^\top\mu
$$

and

$$
\sigma_p^2 = w^\top\Sigma w
$$

The covariance matrix is particularly important because portfolio risk depends not only on individual asset volatility but also on the relationships between assets.

---

### Tangency Portfolio

When a risk-free asset is available, the tangency portfolio is the risky portfolio that maximizes the Sharpe ratio.

Its unconstrained weights are proportional to:

$$
\Sigma^{-1}(\mu-r_f\mathbf{1})
$$

where:

* $\mu$ = vector of expected asset returns.
* $r_f$ = risk-free rate.
* $\mathbf{1}$ = vector of ones.
* $\Sigma$ = covariance matrix.

In this project, expected **excess returns** are used directly, so the portfolio solution can be written as:

$$
w \propto \Sigma^{-1}\mu_e
$$

where $\mu_e$ represents expected excess returns.

---

### Parameter Estimation Risk

The optimal portfolio depends on estimated parameters rather than known population parameters.

In particular:

$$
w \propto \Sigma^{-1}\mu
$$

means that errors in $\mu$ can directly affect portfolio weights.

Because the covariance matrix also influences the optimization, relatively small changes in expected returns can sometimes produce large changes in portfolio allocations.

This project isolates this effect by changing one expected-return estimate at a time.

---

## 🛠️ Tech Stack & Dependencies

* **Python:** 3.10+
* **Data Retrieval:** `yfinance`
* **Data Manipulation:** `pandas`
* **Numerical Computing:** `numpy`
* **Statistical Analysis:** `scipy`
* **Visualization:** `matplotlib`

### Installation

Install the required Python packages with:

```bash
pip install yfinance pandas numpy scipy matplotlib
```

---

## 🚀 Quickstart

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/sp100-monte-carlo-mve.git
cd sp100-monte-carlo-mve
```

### 2. Install Dependencies

```bash
pip install -r requirements.txt
```

If a `requirements.txt` file is not included, install the dependencies manually:

```bash
pip install yfinance pandas numpy scipy matplotlib
```

### 3. Run the Analysis

Open the Jupyter notebook:

```bash
jupyter notebook
```

Then open the project notebook and execute the cells sequentially.

---

## 📁 Project Structure

```text
sp100-monte-carlo-mve/
│
├── README.md
├── requirements.txt
│
├── notebooks/
│   └── sp100_mean_variance_analysis.ipynb
│
├── data/
│   └── README.md
│
└── figures/
    └── ...
```

---

## 📈 Analysis Outputs

The analysis produces several outputs, including:

* Historical expected excess returns.
* Standard errors and 95% confidence intervals.
* The estimated covariance matrix.
* Unconstrained tangency portfolio weights.
* Portfolio volatility and expected excess return.
* Portfolio weights scaled to 10% annualized volatility.
* Sensitivity of portfolio weights to changes in AAPL's expected return.
* Visualizations showing how parameter uncertainty affects portfolio allocation.

---

## 🔬 Research Question

The central research question is:

> **How sensitive are optimal Mean-Variance portfolio allocations to statistical uncertainty in expected return estimates?**

The analysis explores whether uncertainty in a single estimated expected return can meaningfully change the resulting portfolio allocation when the portfolio is constructed using the inverse covariance matrix.

---

## ⚠️ Limitations

Several assumptions and limitations should be considered when interpreting the results:

1. **Historical estimates are used as proxies for expected returns.**
   Past average returns may not accurately represent future expected returns.

2. **Expected returns are estimated independently for each asset.**
   The model does not incorporate a factor model or other structural approach to estimating expected returns.

3. **The covariance matrix is estimated from historical returns.**
   The estimated covariance matrix is also subject to sampling error.

4. **The portfolio is unconstrained.**
   The optimization permits short positions and does not impose position limits.

5. **Transaction costs are ignored.**
   The analysis does not account for commissions, bid-ask spreads, market impact, or other trading costs.

6. **The risk-free-rate conversion assumes 252 trading days.**

7. **The analysis is historical rather than predictive.**
   The results should not be interpreted as evidence that the estimated portfolio will generate superior future returns.

---

## 📚 Theoretical Framework

The analysis is based primarily on the classical framework introduced by:

* Markowitz, H. (1952). *Portfolio Selection*. The Journal of Finance, 7(1), 77–91.
* Sharpe, W. F. (1964). *Capital Asset Prices: A Theory of Market Equilibrium under Conditions of Risk*. The Journal of Finance, 19(3), 425–442.

---

## 👤 Author

**Alexy Palk**

Quantitative Finance / Financial Economics

Interested in quantitative investing, portfolio construction, risk management, and financial modeling.
