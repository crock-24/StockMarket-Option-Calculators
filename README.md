# Options Spread & Covered Call Analyzer

This Python project analyzes:

1. **Bull Call Spreads** – for bullish outlooks  
2. **Bear Put Spreads** – for bearish outlooks  
3. **Covered Calls** – to generate income by selling calls against held stock  

It uses **real-time options data from Yahoo Finance**, calculates **returns, losses, breakeven prices**, and visualizes **risk/reward profiles**.

---

## Purpose
- Evaluate **risk/reward** for leveraged spreads and covered calls.
- Customize analysis using **price targets** and **risk adjustment factors**.
- Generate **bar plots** to visualize potential profits and losses.

---

## Workflow

### 1. Fetch Options Data
```python
callData = getData(stock_symbol)  # for bull call spreads
putData = getData(stock_symbol)   # for bear put spreads
options = getData(stock_symbol)   # for covered calls
```
- Retrieves **available expiration dates**.
- Computes **mid-price (mark)** and **breakeven** for each strike.

### 2. Filter Spreads by Risk

#### Bull Call Spreads:
```python
callData = callFinder(callData, cushionPercent)
bullSpread = spreadFinder(callData)
strikeBarPlotter(bullSpread)
```

#### Bear Put Spreads:
```python
putData = putFinder(putData, cushionPercent)
bearSpread = spreadFinder(putData)
strikeBarPlotter(bearSpread)
```

- Filters strikes based on **target price** and **risk adjustment factor**.
- Selects **first leg** and appropriate **OTM legs** to sell.

### 3. Analyze Covered Calls
```python
optionsData = strikeFinder(options, [0, 5, 10])  # % OTM
optionsData['%_return'] = (optionsData['mark'] / optionsData['current price']) * 100
strikeBarPlotter(optionsData)
```
- Finds **nearest strike** for a set of % OTM calls.
- Calculates **% return** for each covered call.
- Generates **bar plot by expiration date and % OTM**.

---

## Inputs
- **Stock ticker** (e.g., `MSFT`, `AAPL`, `PYPL`)  
- **Expiration date** (choose from available options)  
- **Price target** (for spreads)  
- **Risk adjustment factor** (-0.1 aggressive → 0.1 conservative, spreads only)  
- **Covered call % OTM** (distance above current stock price)

---

## Requirements
```text
pandas
numpy
yfinance
matplotlib
```

---

## Example Usage

### Bull Call Spread
```python
stock = input('Which stock do you want to see bull spreads?: ')
riskAdjustment = float(input('Risk adjustment factor (-0.1 to 0.1): '))

callData = callFinder(getData(stock), riskAdjustment)
callData = callData.reset_index(drop=True)

bullSpread = spreadFinder(callData)
strikeBarPlotter(bullSpread)
```

### Bear Put Spread
```python
stock = input('Which stock do you want to see bear spreads?: ')
riskAdjustment = float(input('Risk adjustment factor (-0.1 to 0.1): '))

putData = putFinder(getData(stock), riskAdjustment)
putData = putData.reset_index(drop=True)

bearSpread = spreadFinder(putData)
strikeBarPlotter(bearSpread)
```

### Covered Calls
```python
symbol = input('Which stock are you interested in?: ')
options = strikeFinder(getData(symbol), [0, 5, 10])
options['%_return'] = (options['mark'] / options['current price']) * 100
strikeBarPlotter(options)
```

---

## Key Insights
- **Bull call spreads:** Reduce losses but limit upside. Wider strikes increase both potential profit and risk.
- **Bear put spreads:** Reduce risk but cap gains. Wider strikes increase potential profit and risk.
- **Covered calls:** Generate income from held stock with defined risk; % OTM determines potential returns.
- **Risk adjustment:** Customize aggressiveness or conservatism for spreads.
- **Visualizations:** Bar charts provide clear, immediate insight into risk/reward.

---

## Notes
- Assumes **100 shares per options contract**.
- Calculations **exclude commissions, fees, and margin requirements**.
- Supports **U.S. stock options** only.
