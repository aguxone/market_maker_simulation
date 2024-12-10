# Market Simulation and Market Making Program in Python

This program models a simulated financial market for futures contracts and calculates corrected bid and ask prices to ensure balanced market-making activities. It consists of three main components: `MarketSimulation`, `MarketDataManager`, and `MarketMaker`.

## Overview of Futures Contracts

A **futures contract** is a standardized agreement to buy or sell an asset at a predetermined price on a specified future date. The "fair price" of a future (which is a typical "future price") is a useful parameter to estimate the fair price of the future, and is the tool for market makers to quote their bids and asks on the market, that can help them balance it's profit, market liquidity and avoid arbitrage. It is determined using the formula:

$$
\text{Fair Price} = \text{Spot Price} \times (1 + r)^{T}
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

1. **Calculate Fair Prices**: Using the formula above, the fair price for each timestamp is derived based on time to maturity.
2. **Correct Bid and Ask Prices for adequate quoting**:

   - The **spread** between the ask and bid price is calculated as:

     $\text{Spread} = \text{Ask Price} - \text{Bid Price}$
   - The corrected prices are the prices the market maker is gonna post, and are adjusted to maintain the spread within a proportion of the original spread, e.g:

     - Bid Price Corrected:

        $\text{Bid Price Corrected} = \text{Bid Price} + (\text{Spread} \times (Spot price/ Fair price))$
     
     - Ask Price Corrected:
       
        $\text{Ask Price Corrected} = \text{Ask Price} + (\text{Spread} \times (Spot price/ Fair price) )$

3. **Determine Quantities to Post**: Allocates budgeted quantities equally across the duration of each contract, budget is only "eyeballed" on this program.

### Observations on the used Criteria:

- **Bid Corrected Prices**:

  - Can never exceed the current market ask prices (by percentage of the spread as reference).
  - Are always higher than the market bid prices, meaning the market maker offers more money than the current bids.
- **Ask Corrected Prices**:

  - Always higher than the current market ask prices, ensuring that the market maker asks for more than the current ask.

---

### Objectives of the Strategy:

- **Unified and Universal Criteria**:

  - The strategy uses a single, consistent approach for both bid and ask prices, applicable across different market conditions.
- **Increased Trading Volume and Profit from Bids**:

  - By offering bids that are higher than the market, the market maker increases the likelihood of executing trades and capturing profits.
- **Ask Prices**:

  - Ask prices are adjusted to encourage a balance where the market maker earns lower profits from asks while still contributing to market dynamics.
- **Push Prices Toward Fair Value**:

  - The adjusted bid and ask prices help move the market prices closer to the fair price, which prevents opportunities for risk-free arbitrage. This ensures the market remains more efficient and aligned with the fair price.

This is a fixed (but dynamic) criteria. It should not replace a more flexible one based on experience and proper analysis of the market, maybe better results might be obtained by adjusting the mentioned percentages differently when having tighter spreads for scenarios of lower volatility, and wider spread when it's higher.

### Program Flow

1. **Market Simulation**: Initializes a simulated market with synthetic spot prices, bid prices, ask prices, and quantities.
2. **Data Management**: Converts JSON data from the simulation into Pandas DataFrames.
3. **Market Making**: Calculates fair prices, corrected bid and ask prices, and quantities to post. Generates a JSON representation of the data for API integration.

### Example Output

The processed data for a contract (e.g., 9M) includes:

- Corrected bid and ask prices.
- Quantities to quote for each timestamp.
- More data on the dataframe type

 Dataframe output:

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
      <th>Fair Price</th>
      <th>Spread</th>
      <th>Spot/Fairprice</th>
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
      <td>10.022340</td>
      <td>9.956228</td>
      <td>7</td>
      <td>10.083144</td>
      <td>3</td>
      <td>270 days 00:00:00</td>
      <td>0.739726</td>
      <td>10.390666</td>
      <td>0.126917</td>
      <td>0.964552</td>
      <td>10.078645</td>
      <td>10.205562</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2024-12-01 01:00:00</td>
      <td>9.560807</td>
      <td>9.497740</td>
      <td>5</td>
      <td>9.618812</td>
      <td>8</td>
      <td>269 days 23:00:00</td>
      <td>0.739612</td>
      <td>9.912117</td>
      <td>0.121072</td>
      <td>0.964558</td>
      <td>9.614521</td>
      <td>9.735593</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2024-12-01 02:00:00</td>
      <td>10.109203</td>
      <td>10.042518</td>
      <td>3</td>
      <td>10.170534</td>
      <td>6</td>
      <td>269 days 22:00:00</td>
      <td>0.739498</td>
      <td>10.480605</td>
      <td>0.128017</td>
      <td>0.964563</td>
      <td>10.165998</td>
      <td>10.294014</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2024-12-01 03:00:00</td>
      <td>9.998430</td>
      <td>9.932475</td>
      <td>2</td>
      <td>10.059089</td>
      <td>8</td>
      <td>269 days 21:00:00</td>
      <td>0.739384</td>
      <td>10.365704</td>
      <td>0.126614</td>
      <td>0.964568</td>
      <td>10.054603</td>
      <td>10.181217</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2024-12-01 04:00:00</td>
      <td>9.686114</td>
      <td>9.622220</td>
      <td>3</td>
      <td>9.744879</td>
      <td>7</td>
      <td>269 days 20:00:00</td>
      <td>0.739269</td>
      <td>10.041861</td>
      <td>0.122659</td>
      <td>0.964574</td>
      <td>9.740534</td>
      <td>9.863193</td>
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
      <td>...</td>
    </tr>
    <tr>
      <th>6476</th>
      <td>2025-08-27 20:00:00</td>
      <td>10.843891</td>
      <td>10.772360</td>
      <td>6</td>
      <td>10.909680</td>
      <td>2</td>
      <td>0 days 04:00:00</td>
      <td>0.000457</td>
      <td>10.844133</td>
      <td>0.137320</td>
      <td>0.999978</td>
      <td>10.909677</td>
      <td>11.046997</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>6477</th>
      <td>2025-08-27 21:00:00</td>
      <td>10.057756</td>
      <td>9.991410</td>
      <td>7</td>
      <td>10.118775</td>
      <td>4</td>
      <td>0 days 03:00:00</td>
      <td>0.000342</td>
      <td>10.057924</td>
      <td>0.127365</td>
      <td>0.999983</td>
      <td>10.118773</td>
      <td>10.246138</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>6478</th>
      <td>2025-08-27 22:00:00</td>
      <td>10.174632</td>
      <td>10.107516</td>
      <td>9</td>
      <td>10.236361</td>
      <td>3</td>
      <td>0 days 02:00:00</td>
      <td>0.000228</td>
      <td>10.174745</td>
      <td>0.128845</td>
      <td>0.999989</td>
      <td>10.236359</td>
      <td>10.365204</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>6479</th>
      <td>2025-08-27 23:00:00</td>
      <td>10.036212</td>
      <td>9.970008</td>
      <td>6</td>
      <td>10.097101</td>
      <td>8</td>
      <td>0 days 01:00:00</td>
      <td>0.000114</td>
      <td>10.036268</td>
      <td>0.127092</td>
      <td>0.999994</td>
      <td>10.097100</td>
      <td>10.224192</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>6480</th>
      <td>2025-08-28 00:00:00</td>
      <td>10.861189</td>
      <td>10.789543</td>
      <td>9</td>
      <td>10.927082</td>
      <td>6</td>
      <td>0 days 00:00:00</td>
      <td>0.000000</td>
      <td>10.861189</td>
      <td>0.137539</td>
      <td>1.000000</td>
      <td>10.927082</td>
      <td>11.064622</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>6481 rows × 15 columns</p>
</div>

JSON output:

'[{"Date":1733011200000,"Bid Price corrected":9.7399233987,"Ask Price corrected":9.7971092582,"Bids to post":1.0,"Asks to post":1.0},{"Date":1733014800000,"Bid Price corrected":9.382626611,"Ask Price corrected":9.4377146795,"Bids to post":1.0,"Asks to post":1.0},...

### Key Parameters

- `spread_criteria_proportion`: Proportion of the spread used to adjust corrected prices, e.g 0.25 would represent 25% of the spread.
- `capital_cost`: Annualized cost used in fair price calculations, a risk-free interest rate would be a good starting estimation (e.g 0.05 for 5%), though many factor can be added such as potential returns of assets, or cost of maintenance of physical assets if owned.

### Example Usage

```python
# Initialize market simulation
market_simulation = MarketSimulation(spot_price=10, start_date="2024-12-01 00:00:00", contract_list=[3,6,9], variance=0.5)

# Convert JSON data to Pandas DataFrames
lifted_market_data_dict_of_dfs = MarketDataManager(json_data=market_simulation.order_book_json).df

# Perform market making calculations
market_maker = MarketMaker(lifted_market_data_dict_of_dfs=lifted_market_data_dict_of_dfs, budget=10000, capital_cost=0.05)

# Print example DataFrame for a 9-month contract
print(market_maker.market_data_dict_of_dfs["9M"])
print(market_maker.market_data_dict_of_jsons["9M"])
```
