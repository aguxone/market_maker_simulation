# Market Simulation and Market Making Program in Python

This program models a simulated financial market for futures contracts and calculates corrected bid and ask prices to ensure balanced market-making activities. It consists of three main components: `MarketSimulation`, `MarketDataManager`, and `MarketMaker`.

## Overview of Futures Contracts

A **futures contract** is a standardized agreement to buy or sell an asset at a predetermined price on a specified future date. The "future price" of a future is a useful parameter to estimate the fair price of the future, and is the tool for market makers to post ther bid and ask to the market. It is determined using the formula:

$$
\text{Future Price} = \text{Spot Price} \times (1 + r)^{T}
$$

Where:

- $r$ is the annualized capital cost.
- $T$ is the time to maturity in years.

## Components of the Program

### 1. `MarketSimulation`

The `MarketSimulation` class generates synthetic data following a normal distribution for a simulated market with futures contracts expiring in specified months (e.g., 3M, 6M, 9M). The generated data includes:

- Spot prices (following a normal distribution).
- Bid and ask prices (derived as random percentages above or below the spot price).
- Random quantities for bids and asks.
- Hourly timestamps over the duration of each contract.

This data is organized into Pandas DataFrames for further analysis, but the class outputs a dictionary of JSON objects as an attribute, simulating what an exchange API would tipically do in a real case scenario.

### 2. `MarketDataManager`

The `MarketDataManager` class converts JSON representations of the market data into Pandas DataFrames for easy manipulation and computation.

### 3. `MarketMaker`

The `MarketMaker` class processes the market data to:

1. **Calculate Future Prices**: Using the formula above, the future price for each timestamp is derived based on time to maturity.
2. **Correct Bid and Ask Prices for adequate quoting**:
   - The **spread** between the ask and bid price is calculated as:
     
     $$
     
     \text{Spread} = \text{Ask Price} - \text{Bid Price}
     
     $$
     
   - The corrected prices are the prices the market maker is gonna post, and are adjusted to maintain the spread within a proportion of the original spread:
     - Bid Price Corrected:
       $$
       \text{Bid Price Corrected} = \text{Bid Price} + (\text{Spread} \times \text{spread\_criteria\_proportion})
       $$
     - Ask Price Corrected:
       $$
       \text{Ask Price Corrected} = \text{Ask Price} - (\text{Spread} \times \text{spread\_criteria\_proportion})
       $$
3. **Determine Quantities to Post**: Allocates budgeted quantities equally across the duration of each contract.

### Program Flow

1. **Market Simulation**: Initializes a simulated market with synthetic spot prices, bid prices, ask prices, and quantities.
2. **Data Management**: Converts JSON data from the simulation into Pandas DataFrames.
3. **Market Making**: Calculates future prices, corrected bid and ask prices, and quantities to post. Generates a JSON representation of the data for API integration.

### Example Output

The processed data for a contract (e.g., 9M) includes:

- Corrected bid and ask prices.
- Quantities to post for each timestamp.

### Key Parameters

- `spread_criteria_proportion`: Proportion of the spread used to adjust corrected prices, e.g 0.25 would represent 25% of the spread.
- `capital_cost`: Annualized cost used in future price calculations, a risk-free interest would be a good starting estimation (e.g 0.05 for 5%).

### Example Usage

```python
# Initialize market simulation
market_simulation = MarketSimulation(spot_price=10, start_date="2024-12-01 00:00:00", contract_list=[3,6,9], variance=0.5)

# Convert JSON data to Pandas DataFrames
lifted_market_data_dict_of_dfs = MarketDataManager(json_data=market_simulation.order_book_json).df

# Perform market making calculations
market_maker = MarketMaker(lifted_market_data_dict_of_dfs=lifted_market_data_dict_of_dfs, budget=10000, spread_criteria_proportion=0.25, capital_cost=0.05)

# Print example DataFrame for a 9-month contract
print(market_maker.market_data_dict_of_dfs["9M"])
```
