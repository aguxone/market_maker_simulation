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
     
     $\text{Spread} = \text{Ask Price} - \text{Bid Price}$
     
   - The corrected prices are the prices the market maker is gonna post, and are adjusted to maintain the spread within a proportion of the original spread, e.g:
     - Bid Price Corrected:
       
       $$\text{Bid Price Corrected} = \text{Bid Price} + (\text{Spread} \times 0.25)$$
       
     - Ask Price Corrected:
     - 
       $$\text{Ask Price Corrected} = \text{Ask Price} - (\text{Spread} \times 0.25)$$
       
3. **Determine Quantities to Post**: Allocates budgeted quantities equally across the duration of each contract.

### Program Flow

1. **Market Simulation**: Initializes a simulated market with synthetic spot prices, bid prices, ask prices, and quantities.
2. **Data Management**: Converts JSON data from the simulation into Pandas DataFrames.
3. **Market Making**: Calculates future prices, corrected bid and ask prices, and quantities to post. Generates a JSON representation of the data for API integration.

### Example Output

The processed data for a contract (e.g., 9M) includes:

- Corrected bid and ask prices.
- Quantities to quote for each timestamp.
- More data on the dataframe type


 Dataframe output:
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Spot Price</th>
      <th>Bid Price</th>
      <th>Bid Quantity</th>
      <th>Ask Price</th>
      <th>Ask Quantity</th>
      <th>Time to maturity full</th>
      <th>Time to maturity years</th>
      <th>Future Price</th>
      <th>Spread</th>
      <th>Bid Price corrected</th>
      <th>Ask Price corrected</th>
      <th>Bids to post</th>
      <th>Asks to post</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2024-12-01 00:00:00</td>
      <td>9.952873</td>
      <td>9.890845</td>
      <td>4</td>
      <td>10.035788</td>
      <td>4</td>
      <td>270 days 00:00:00</td>
      <td>0.739726</td>
      <td>10.318646</td>
      <td>0.144943</td>
      <td>9.927080</td>
      <td>9.999552</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2024-12-01 01:00:00</td>
      <td>10.497999</td>
      <td>10.432573</td>
      <td>9</td>
      <td>10.585455</td>
      <td>6</td>
      <td>269 days 23:00:00</td>
      <td>0.739612</td>
      <td>10.883745</td>
      <td>0.152882</td>
      <td>10.470793</td>
      <td>10.547234</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2024-12-01 02:00:00</td>
      <td>9.980335</td>
      <td>9.918136</td>
      <td>6</td>
      <td>10.063479</td>
      <td>3</td>
      <td>269 days 22:00:00</td>
      <td>0.739498</td>
      <td>10.347003</td>
      <td>0.145343</td>
      <td>9.954472</td>
      <td>10.027143</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2024-12-01 03:00:00</td>
      <td>9.955430</td>
      <td>9.893385</td>
      <td>8</td>
      <td>10.038366</td>
      <td>5</td>
      <td>269 days 21:00:00</td>
      <td>0.739384</td>
      <td>10.321125</td>
      <td>0.144980</td>
      <td>9.929631</td>
      <td>10.002121</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2024-12-01 04:00:00</td>
      <td>9.949400</td>
      <td>9.887394</td>
      <td>5</td>
      <td>10.032286</td>
      <td>4</td>
      <td>269 days 20:00:00</td>
      <td>0.739269</td>
      <td>10.314817</td>
      <td>0.144892</td>
      <td>9.923617</td>
      <td>9.996063</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>6476</th>
      <td>2025-08-27 20:00:00</td>
      <td>10.320334</td>
      <td>10.256016</td>
      <td>8</td>
      <td>10.406310</td>
      <td>2</td>
      <td>0 days 04:00:00</td>
      <td>0.000457</td>
      <td>10.320564</td>
      <td>0.150294</td>
      <td>10.293589</td>
      <td>10.368737</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>6477</th>
      <td>2025-08-27 21:00:00</td>
      <td>9.286088</td>
      <td>9.228216</td>
      <td>3</td>
      <td>9.363448</td>
      <td>7</td>
      <td>0 days 03:00:00</td>
      <td>0.000342</td>
      <td>9.286244</td>
      <td>0.135233</td>
      <td>9.262024</td>
      <td>9.329640</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>6478</th>
      <td>2025-08-27 22:00:00</td>
      <td>9.908305</td>
      <td>9.846555</td>
      <td>9</td>
      <td>9.990849</td>
      <td>8</td>
      <td>0 days 02:00:00</td>
      <td>0.000228</td>
      <td>9.908416</td>
      <td>0.144294</td>
      <td>9.882628</td>
      <td>9.954775</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>6479</th>
      <td>2025-08-27 23:00:00</td>
      <td>10.672938</td>
      <td>10.606422</td>
      <td>4</td>
      <td>10.761851</td>
      <td>8</td>
      <td>0 days 01:00:00</td>
      <td>0.000114</td>
      <td>10.672997</td>
      <td>0.155429</td>
      <td>10.645279</td>
      <td>10.722994</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>6480</th>
      <td>2025-08-28 00:00:00</td>
      <td>9.732181</td>
      <td>9.671529</td>
      <td>9</td>
      <td>9.813258</td>
      <td>7</td>
      <td>0 days 00:00:00</td>
      <td>0.000000</td>
      <td>9.732181</td>
      <td>0.141729</td>
      <td>9.706961</td>
      <td>9.777825</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>6481 rows × 14 columns</p>
</div>

JSON output:

'[{"Date":1733011200000,"Bid Price corrected":9.7399233987,"Ask Price corrected":9.7971092582,"Bids to post":1.0,"Asks to post":1.0},{"Date":1733014800000,"Bid Price corrected":9.382626611,"Ask Price corrected":9.4377146795,"Bids to post":1.0,"Asks to post":1.0},...

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
print(market_maker.market_data_dict_of_jsons["9M"])
```
