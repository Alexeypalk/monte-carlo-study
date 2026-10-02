# S&P 100 Mean-Variance & Portfolio Sensitivity Analysis

A quantitative finance project analyzing S&P 100 equities through **Mean-Variance Portfolio Theory**, **Parameter Estimation Uncertainty**, and **Tangency Portfolio Sensitivity Analysis**.

---

## 📌 Project Overview

Modern Portfolio Theory (MPT) relies heavily on expected return estimates ($\mu$) and covariance matrices ($\Sigma$). However, sample mean estimators carry statistical noise, which can severely distort optimal portfolio weights. 

This notebook pulls 15 years (2010–2024) of daily market data for S&P 100 constituents and Treasury bill yields, constructs an unconstrained Mean-Variance Efficient (MVE) tangency portfolio targeting 10% annualized volatility, and quantifies portfolio weight sensitivity to parameter estimation error.

---

## ⚙️ Methodology & Analytical Steps

1. **Data Ingestion & Cleaning**
   * Downloads daily adjusted closing prices for S&P 100 stocks via `yfinance` from `2010-01-01` to `2024-12-31`.
   * Pulls the 13-week Treasury Bill yield (`^IRX`) as the risk-free rate and scales it from an annualized percentage to a daily decimal rate:
$$R_f = \frac{\text{IRX}}{100 \times 252}$$
   * Computes daily excess returns ($R_e = R - R_f$) and drops incomplete ticker histories (retaining a balanced panel of 92 tickers over 3,770 trading days).

2. **Expected Excess Return & Standard Errors**
   * Calculates the sample mean expected excess return ($\bar{r}_i$) for each asset.
   * Computes the standard error of the sample mean:
$$\sigma(\bar{r}_i) = \frac{\sigma(r_{i,t})}{\sqrt{T}}$$
   * Constructs two-sided 95% confidence intervals using standard normal critical values ($z \approx 1.96$):
$$[\bar{r}_i - z \cdot \sigma(\bar{r}_i), \; \bar{r}_i + z \cdot \sigma(\bar{r}_i)]$$

3. **Tangency / MVE Portfolio Optimization**
   * Computes the excess return covariance matrix ($\Sigma$) and its inverse ($\Sigma^{-1}$).
   * Solves for unconstrained tangency portfolio weights proportional to $\Sigma^{-1} \mathbb{E}[R^e]$.
   * Scales portfolio weights to achieve a target annualized volatility of **10.0%**.

4. **Parameter Sensitivity Analysis**
   * Re-evaluates tangency portfolio weights by varying individual mean return estimates within their 95% confidence intervals (tested on AAPL).
   * Demonstrates how small statistical variation in single-asset return estimates causes significant reallocation shifts across the broader portfolio.

---

## 🛠️ Tech Stack & Dependencies

* **Language:** Python 3.10+
* **Data Retrieval:** `yfinance`
* **Data Manipulation & Analysis:** `pandas`, `numpy`
* **Statistical Modeling:** `scipy`
* **Visualization:** `matplotlib`

---

## 🚀 Quickstart & Usage

### 1. Clone the repository
```bash
git clone [https://github.com/your-username/sp100-monte-carlo-mve.git](https://github.com/your-username/sp100-monte-carlo-mve.git)
cd sp100-monte-carlo-mve
