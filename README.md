# Quantitative Trading Backtest (Python)

## Project Overview

This project is a simple quantitative trading backtest built in Python. It tests different trading strategies across multiple assets and compares their performance over time.

The goal was to simulate how different strategies would perform in real market conditions while accounting for basic trading factors like transaction costs and slippage.

Two main strategies were tested:
- Bollinger Bands strategy
- Donchian Channel strategy

---

## Tools Used

- Python (Pandas, NumPy, Matplotlib)
- Jupyter Notebook
- Power BI

---

## How It Works

The project is built around a backtesting function that simulates trading decisions over a set period.

The flow:

- Define start and end dates for the test period
  
```python
starting_bal = 1000
start = datetime(2020,1,1)
end = datetime(2023,1,1)
interval = '1d'
tickers = ['MSFT', 'GLD', 'TLT']

transaction_cost = 0.001
slippage = 0.0005
cost = transaction_cost + slippage
- 
- Load and prepare ticker data
- Apply trading logic inside a backtest function
- Calculate returns, positions, and trade outcomes
- Include transaction costs and slippage
- Store results in dataframes for comparison

A loop runs the backtest across multiple tickers to evaluate performance across different assets.

---

## Strategy Summary

Two strategies were tested:

### Bollinger Bands Strategy
This strategy uses volatility-based bands to identify potential entry and exit points. It performed consistently well across most assets and delivered the strongest overall returns.

### Donchian Channel Strategy
This strategy focuses on breakout signals. It performed slightly weaker overall but showed competitive results toward the end of the testing period.

---

## Key Findings

- The Bollinger Bands strategy produced the highest final returns overall.
- It also showed more stable and consistent performance across different assets.
- The Donchian Channel strategy performed better in certain periods but was less consistent overall.
- When transaction costs and slippage were included, Bollinger Bands still maintained better risk-adjusted performance.
- Overall, Bollinger Bands proved to be the more reliable strategy in this test.

---

## Output

The final results were converted into CSV files for further analysis and comparison.

---
