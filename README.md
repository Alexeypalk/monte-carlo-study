# S&P 100 Mean-Variance & Portfolio Sensitivity Analysis

A quantitative finance project analyzing S&P 100 equities using **Mean-Variance Portfolio Theory**, **parameter estimation uncertainty**, and **tangency portfolio sensitivity analysis**.

---

## 📌 Project Overview

Modern Portfolio Theory (MPT) relies heavily on estimates of expected returns ($\mu$) and the covariance matrix ($\Sigma$). However, expected return estimates based on historical data contain substantial statistical noise, which can lead to significant changes in optimal portfolio weights.

This project uses approximately 15 years of daily market data for S&P 100 constituents and Treasury bill yields to:

- Estimate daily expected excess returns and their statistical uncertainty.
- Construct an unconstrained tangency portfolio using the inverse covariance matrix.
- Scale the portfolio to a target annualized volatility of **10%**.
- Construct confidence intervals around individual expected return estimates.
- Analyze how changes in a single asset's estimated expected return affect the resulting portfolio allocation.

The analysis focuses on **AAPL** as a case study for parameter sensitivity.

---

## ⚙️ Methodology

### 1. Data Ingestion & Cleaning

- Downloads daily adjusted closing prices for S&P 100 constituents using `yfinance`.
- Uses data from **January 1, 2010 through December 31, 2024**.
- Downloads the 13-week U.S. Treasury bill yield (`^IRX`) as the risk-free rate.
- Converts the annualized Treasury bill yield from percentage terms into a daily decimal rate:

$$
R_{f,t} = \frac{\text{IRX}_t}{100 \times 252}
$$

- Calculates daily excess returns:

$$
R_{e,i,t} = R_{i,t} - R_{f,t}
$$

- Removes securities with incomplete histories in order to maintain a balanced panel.
- The final dataset contains **92 securities over approximately 3,770 trading days**.

---

### 2. Expected Excess Returns & Standard Errors

For each security, the sample mean daily excess return is calculated as:

$$
\bar{r}_i = \frac{1}{T}\sum_{t=1}^{T} r_{i,t}
$$

The standard error of the sample mean is estimated as:

$$
SE(\bar{r}_i) = \frac{\sigma(r_i)}{\sqrt{T}}
$$

where:

- $\sigma(r_i)$ is the sample standard deviation of daily excess returns.
- $T$ is the number of observations.

Two-sided 95% confidence intervals are then constructed using the standard normal critical value:

$$
CI_{95\%} =
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

- $w$ = vector of portfolio weights.
- $\Sigma^{-1}$ = inverse covariance matrix.
- $\mu$ = vector of expected excess returns.

The raw weights are then scaled to target **10% annualized portfolio volatility**.

For daily returns, portfolio variance is:

$$
\sigma_p^2 = w^\top \Sigma w
$$

Annualized volatility is calculated as:

$$
\sigma_{p,\text{annual}}
=
\sqrt{252}
\sqrt{w^\top \Sigma w}
$$

The weights are scaled so that:

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
E[R_p] = w^\top \mu
$$

and:

$$
\sigma_p^2 = w^\top \Sigma w
$$

The covariance matrix is particularly important because portfolio risk depends not only on individual asset volatility but also on the relationships between assets.

---

### Tangency Portfolio

When a risk-free asset is available, the tangency portfolio is the risky portfolio that maximizes the Sharpe ratio.

Its unconstrained weights are proportional to:

$$
w \propto \Sigma^{-1}(\mu-r_f\mathbf{1})
$$

where:

- $\mu$ = vector of expected asset returns.
- $r_f$ = risk-free rate.
- $\mathbf{1}$ = vector of ones.
- $\Sigma$ = covariance matrix.

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

- **Python:** 3.10+
- **Data Retrieval:** `yfinance`
- **Data Manipulation:** `pandas`
- **Numerical Computing:** `numpy`
- **Statistical Analysis:** `scipy`
- **Visualization:** `matplotlib`

### Installation

Install the required Python packages with:

```bash
pip install yfinance pandas numpy scipy matplotlib
