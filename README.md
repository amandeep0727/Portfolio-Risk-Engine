# Portfolio Optimization & Tail Risk on the Magnificent 7

## Executive summary

The Magnificent 7 (AAPL, AMZN, GOOG, NVDA, TSLA, MSFT, META) now carry enough index weight that broad equity exposure is, in practice, a concentrated position. This notebook isolates that concentration and answers three questions:

1. What portfolio would you actually hold if you only used these seven stocks?
2. How efficient is that portfolio on a return/volatility basis under realistic constraints?
3. What does the downside tail look like once you move beyond variance (99% VaR and ES)?

The analysis is intentionally simple, transparent, and decision-oriented: build an efficient portfolio, validate it, and then stress its tails.

---

## Motivation and hypothesis

* **Context:** Index concentration has turned a “diversified” benchmark into a narrow growth bet.
* **Hypothesis:** Even with smart allocation, concentration risk persists; mean–variance alone won’t tell the full story.
* **Test:** Construct max-Sharpe and min-vol portfolios with allocation caps; quantify both average and tail risk.

---

## Approach (what runs under the hood)

* **Data**
  Daily adjusted close prices (yfinance). Log returns. Annualized mean/covariance with 252 trading days.

* **Optimization**
  Markowitz mean–variance with realistic constraints:

  * weights sum to 1
  * max 40% in any single name
  * risk-free rate = 4.5%
    Solver: `scipy.optimize.SLSQP` to obtain **Maximum Sharpe** and **Global Minimum Variance** solutions.

* **Validation**
  Monte Carlo draw of 10,000 random portfolios to visualize the feasible frontier and confirm that the optimizer sits where it should relative to the cloud.

* **Tail risk**
  Historical **99% Value-at-Risk (VaR)** and **99% Expected Shortfall (ES)** computed on the optimized portfolio’s daily P&L. These complement variance by quantifying loss in the left tail.

* **Presentation**
  Price chart for context, styled tables for annualized returns and optimal weights, efficient-frontier plot with the optimum highlighted, and a one-row summary table covering return, volatility, Sharpe, VaR, ES.

---

## What to look at in the outputs

1. **Stock-level annualized returns**
   Quickly reveals which names drive averages.

2. **Efficient frontier (10,000 portfolios)**
   Shows the attainable risk/return set; the optimized point should sit on the upper boundary.

3. **Optimized weights table**
   Enforces diversification via caps; if one or two names still dominate, that’s information.

4. **Final summary table (single row)**
   Expected annual return, annual volatility, Sharpe, 99% daily VaR, 99% daily ES.
   This is the “can we live with the left tail?” decision point.

---

## How to interpret the results

* **High average, meaningful tails:** Tech concentration can be efficient on mean/variance, but VaR/ES remind you that stress days are large.
* **Constraints matter:** Even with a 40% cap, the optimizer may still cluster in a few names—diversification within this universe is structurally limited.
* **Suitability:**

  * Appropriate for investors who *intentionally* want concentrated growth and accept tail risk.
  * Not suitable for mandates requiring sector diversification or tight drawdown controls.

---

## Method notes

* Returns modeled with historical mean/covariance; no forecasting model (ARIMA/GARCH) is used here by design.
* VaR/ES are **historical** (non-parametric) on daily P&L; they are not scaled to longer horizons.
* This is a static, transparent snapshot meant for clarity and speed, not a regime-switching backtest.

---

## How to run

```bash
pip install -r requirements.txt
jupyter notebook Portfolio_Var_ES_MV.ipynb
```

Run top-to-bottom. You’ll see: price context → annualized returns table → frontier + optimal point → optimal weights table → final summary table (return, vol, Sharpe, 99% VaR, 99% ES).

---

## Files

* `Portfolio_Var_ES_MV.ipynb` — full workflow: data → simulation → optimization → risk → summary
* `requirements.txt` — minimal dependencies
* `README.md` — this document

---

### Requirements (place in `requirements.txt`)

```
pandas
numpy
matplotlib
yfinance
scipy
ipython
jupyter
```
