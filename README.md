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
```
- Load and prepare ticker data
```python
interval = '1d'
tickers = ['MSFT', 'GLD', 'TLT']
transaction_cost = 0.001
slippage = 0.0005
cost = transaction_cost + slippage
```

- Apply trading logic inside a backtest function
```python
def backtest(ticker):
    price = yf.download(tickers=ticker, start=start, end=end, interval=interval)
    price.columns = price.columns.get_level_values(0)
    price = price.reset_index()
    close = price['Close'].squeeze()
    
    price['Asset'] = ticker

    price['middle'] = close.rolling(window=20).mean()
    price['std']    = close.rolling(window=20).std()
    price['upper']  = price['middle'] + (2 * price['std'])
    price['lower']  = price['middle'] - (2 * price['std'])

    price = price.dropna(subset=['middle']).reset_index(drop=True)
    close = price['Close'].squeeze()

    long_signal  = close.shift(1) < price['lower'].shift(1)   
    short_signal = close.shift(1) > price['upper'].shift(1)
    exit_long  = close >= price['middle']
    exit_short = close <= price['middle']

    price['Position'] = 0
    price.loc[long_signal,  'Position'] = 1
    price.loc[short_signal, 'Position'] = -1
    price.loc[exit_long  & (price['Position'] == 1),  'Position'] = 0
    price.loc[exit_short & (price['Position'] == -1), 'Position'] = 0
```

- Calculate returns, positions, and trade outcomes
```python
   price['Returns'] = price['Close'] / price['Close'].shift(1) 
        
    price['Sys_Ret'] = np.where(
        price['Position'] == 1, price['Returns'],
        np.where(
            price['Position'] == -1, 
            2 - price['Returns'], 1
        )
    )

    n_years = len(price) / 252
    price['Sys_bal'] = starting_bal * price['Sys_Ret'].cumprod()
    CAGR = (price['Sys_bal'].iloc[-1] / starting_bal) ** (1 / n_years) - 1
    vol = (price['Sys_Ret'] - 1).std() * np.sqrt(252)
    daily_rf = (1 + 0.05) ** (1/252) - 1
    daily_excess = price['Sys_Ret'] - 1 - daily_rf
    sharpe = (daily_excess.mean() / daily_excess.std()) * np.sqrt(252)
    price['drawdown'] = (price['Sys_bal'] - price['Sys_bal'].cummax()) / price['Sys_bal'].cummax()
    max_dd = price['drawdown'].min()
```

- Store results in dataframes for comparison
```python
    result = {
        'Ticker'  : ticker,
        'CAGR'    : round(CAGR, 4),
        'Sharpe'  : round(sharpe, 4),
        'Vol'     : round(vol, 4),
        'Max_DD'  : round(max_dd, 4),
        'Final_Bal': round(price['Sys_bal'].iloc[-1], 2)
    }

    return price, result
    
  new_price = []
  new_result = []
```

A loop runs the backtest across multiple tickers to evaluate performance across different assets.
```python
for ticker in tickers:
    price, result = backtest(ticker)
    new_price.append(price) 
    new_result.append(result)
        
final = pd.concat(new_price, ignore_index=True)
summary_df = pd.DataFrame(new_result)
print(summary_df.to_string(index=False))
```
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

The final results were converted into CSV files for further analysis and comparison in Power BI

---

## Power BI Dashboard
![backtest 1](backtest1/1)
![backtest 2](backtest1/2)
