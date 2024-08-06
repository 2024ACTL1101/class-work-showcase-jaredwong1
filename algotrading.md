## Algorithmic Trading Strategy

## Introduction

In this assignment, you will develop an algorithmic trading strategy by incorporating financial metrics to evaluate its profitability. This exercise simulates a real-world scenario where you, as part of a financial technology team, need to present an improved version of a trading algorithm that not only executes trades but also calculates and reports on the financial performance of those trades.

## Background

Following a successful presentation to the Board of Directors, you have been tasked by the Trading Strategies Team to modify your trading algorithm. This modification should include tracking the costs and proceeds of trades to facilitate a deeper evaluation of the algorithm’s profitability, including calculating the Return on Investment (ROI).

After meeting with the Trading Strategies Team, you were asked to include costs, proceeds, and return on investments metrics to assess the profitability of your trading algorithm.

## Objectives

1. **Load and Prepare Data:** Open and run the starter code to create a DataFrame with stock closing data.

2. **Implement Trading Algorithm:** Create a simple trading algorithm based on daily price changes.

3. **Customize Trading Period:** Choose your entry and exit dates.

4. **Report Financial Performance:** Analyze and report the total profit or loss (P/L) and the ROI of the trading strategy.

5. **Implement a Trading Strategy:** Implement a trading strategy and analyze the total updated P/L and ROI. 

6. **Discussion:** Summarise your finding.


## Instructions

### Step 1: Data Loading

Start by running the provided code cells in the "Data Loading" section to generate a DataFrame containing AMD stock closing data. This will serve as the basis for your trading decisions. First, create a data frame named `amd_df` with the given closing prices and corresponding dates. 

```{r load-data}

# Load data from CSV file
amd_df <- read.csv("AMD.csv")

# Convert the date column to Date type and Adjusted Close as numeric
amd_df$date <- as.Date(amd_df$Date)
amd_df$close <- as.numeric(amd_df$Adj.Close)

amd_df <- amd_df[, c("date", "close")]
```


## Plotting the Data
Plot the closing prices over time to visualize the price movement.
```{r plot}
plot(amd_df$date, amd_df$close,'l')
```


## Step 2: Trading Algorithm
Implement the trading algorithm as per the instructions. You should initialize necessary variables, and loop through the dataframe to execute trades based on the set conditions.

- Initialize Columns: Start by ensuring dataframe has columns 'trade_type', 'costs_proceeds' and 'accumulated_shares'.
- Change the algorithm by modifying the loop to include the cost and proceeds metrics for buys of 100 shares. Make sure that the algorithm checks the following conditions and executes the strategy for each one:
  - If the previous price = 0, set 'trade_type' to 'buy', and set the 'costs_proceeds' column to the current share price multiplied by a `share_size` value of 100. Make sure to take the negative value of the expression so that the cost reflects money leaving an account. Finally, make sure to add the bought shares to an `accumulated_shares` variable.
  - Otherwise, if the price of the current day is less than that of the previous day, set the 'trade_type' to 'buy'. Set the 'costs_proceeds' to the current share price multiplied by a `share_size` value of 100.
  - You will not modify the algorithm for instances where the current day’s price is greater than the previous day’s price or when it is equal to the previous day’s price.
  - If this is the last day of trading, set the 'trade_type' to 'sell'. In this case, also set the 'costs_proceeds' column to the total number in the `accumulated_shares` variable multiplied by the price of the last day.



```{r trading}
# Initialize columns for trade type, cost/proceeds, and accumulated shares in amd_df
amd_df$trade_type <- NA
amd_df$costs_proceeds <- NA  
amd_df$accumulated_shares <- 0  

# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0

for (i in 1:nrow(amd_df)) {
  # If first trade, set to always buy
  if (previous_price == 0) {
    amd_df$trade_type[i] <- 'buy'
    amd_df$costs_proceeds[i] <- -share_size * amd_df$close[i]
    accumulated_shares <- accumulated_shares + share_size
    amd_df$accumulated_shares[i] <- accumulated_shares
  }
  # If last trade, sell all shares
  else if (i == nrow(amd_df)){
    amd_df$trade_type[i] <- 'sell'
    amd_df$costs_proceeds[i] <- 1 * accumulated_shares * amd_df$close[i]
  }
  # If price is less than previous price, buy 100 shares
  else if (amd_df$close[i] < previous_price){
    amd_df$trade_type[i] <- 'buy'
    amd_df$costs_proceeds[i] <- -share_size * amd_df$close[i]
    accumulated_shares <- accumulated_shares + share_size
    amd_df$accumulated_shares[i] <- accumulated_shares
  }
  
  # At end of every loop, sets accumulated shares column to accumulated shares variable
  amd_df$accumulated_shares[i] = accumulated_shares
  previous_price <- amd_df$close[i]
}
```


## Step 3: Customize Trading Period
- Define a trading period you wanted in the past five years 
```{r period}
# Changes trading period to begin from 2023-01-01 and end at present date
start_date <- as.Date("2023-01-01")
end_date <- as.Date("2024-05-17")

# Filter the trading period
amd_df$date <- as.Date(amd_df$date)
amd_df <- amd_df[amd_df$date >= start_date & amd_df$date <= end_date, ]

```


## Step 4: Run Your Algorithm and Analyze Results
After running your algorithm, check if the trades were executed as expected. Calculate the total profit or loss and ROI from the trades.

- Total Profit/Loss Calculation: Calculate the total profit or loss from your trades. This should be the sum of all entries in the 'costs_proceeds' column of your dataframe. This column records the financial impact of each trade, reflecting money spent on buys as negative values and money gained from sells as positive values.
- Invested Capital: Calculate the total capital invested. This is equal to the sum of the 'costs_proceeds' values for all 'buy' transactions. Since these entries are negative (representing money spent), you should take the negative sum of these values to reflect the total amount invested.
- ROI Formula: $$\text{ROI} = \left( \frac{\text{Total Profit or Loss}}{\text{Total Capital Invested}} \right) \times 100$$

```{r}
# Initialize columns for trade type, cost/proceeds, and accumulated shares in amd_df
amd_df$trade_type <- NA
amd_df$costs_proceeds <- NA 
amd_df$accumulated_shares <- 0  

# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0

for (i in 1:nrow(amd_df)) {
  # If first trade, set to always buy
  if (previous_price == 0) {
    amd_df$trade_type[i] <- 'buy'
    amd_df$costs_proceeds[i] <- -share_size * amd_df$close[i]
    accumulated_shares <- accumulated_shares + share_size  # Accumulates 100 shares
    amd_df$accumulated_shares[i] <- accumulated_shares
  }
  # If last trade, sell all shares
  else if (i == nrow(amd_df)){
    amd_df$trade_type[i] <- 'sell'
    amd_df$costs_proceeds[i] <- 1 * accumulated_shares * amd_df$close[i]
  }
  # If price is less than previous price, buy 100 shares 
  else if (amd_df$close[i] < previous_price){
    amd_df$trade_type[i] <- 'buy'
    amd_df$costs_proceeds[i] <- -share_size * amd_df$close[i]
    accumulated_shares <- accumulated_shares + share_size # Accumulates 100 shares
    amd_df$accumulated_shares[i] <- accumulated_shares
  }
  
  # At end of every loop, sets accumulated shares column to accumulated shares variable
  amd_df$accumulated_shares[i] = accumulated_shares
  previous_price <- amd_df$close[i]
}

# Converts any N/A entries to 0 and sums cost_proceeds column for Total Profit
if (any(is.na(amd_df$costs_proceeds))) {
  amd_df$costs_proceeds[is.na(amd_df$costs_proceeds)] <- 0
}
total_profit <- sum(amd_df$costs_proceeds, na.rm = TRUE)

# Calculate Invested Capital and Disregard N/A
invested_capital <- -sum(amd_df$costs_proceeds[amd_df$trade_type == 'buy'], na.rm = TRUE)

# Calculate ROI 
return_on_investment <- total_profit / invested_capital * 100

```

## Step 5: Profit-Taking Strategy or Stop-Loss Mechanisum (Choose 1)
- **Option 1: Implement a profit-taking strategy that you sell half of your holdings if the price has increased by a certain percentage (e.g., 20%) from the average purchase price.**
- Option 2: Implement a stop-loss mechanism in the trading strategy that you sell half of your holdings if the stock falls by a certain percentage (e.g., 20%) from the average purchase price. You don't need to buy 100 stocks on the days that the stop-loss mechanism is triggered.


```{r option}
# Option 1.

# Initialises Column Values
amd_df$trade_type <- NA
amd_df$costs_proceeds <- NA 
amd_df$accumulated_shares <- 0
amd_df$average_price <- 0
amd_df$total_investment <- 0

# Initialising Variables
previous_price <- 0
share_size <- 100
accumulated_shares <- 0
average_price <- amd_df$close[1]
total_investment <- 0
total_shares <- 0

for (i in 1:nrow(amd_df)) {
  amd_df$average_price[i] <- average_price
  # If first day, buy
  if (previous_price == 0) {
    amd_df$trade_type[i] <- 'buy'
    amd_df$costs_proceeds[i] <- -share_size * amd_df$close[i]
    accumulated_shares <- accumulated_shares + share_size  # Accumulates 100 shares
    amd_df$accumulated_shares[i] <- accumulated_shares   
    total_investment <- total_investment + amd_df$costs_proceeds[i]
    total_shares <- total_shares + share_size
  }
  # If at end of period, sell all shares
  else if (i == nrow(amd_df)){
    amd_df$trade_type[i] <- 'sell'
    amd_df$costs_proceeds[i] <- 1 * accumulated_shares * amd_df$close[i]
  }
  # Sells if closing price is 20% greater than the average_price 
  else if (amd_df$close[i] > 1.2 * average_price) {
    amd_df$trade_type[i] <- 'sell'
    accumulated_shares <- accumulated_shares / 2    # Halves accumulated shares
    amd_df$accumulated_shares[i] <- accumulated_shares 
    amd_df$costs_proceeds[i] <- accumulated_shares * amd_df$close[i]
  }
  # Buys if closing price is less than the previous price 
  else if (amd_df$close[i] < previous_price){
    amd_df$trade_type[i] <- 'buy'
    amd_df$costs_proceeds[i] <- -share_size * amd_df$close[i] 
    accumulated_shares <- accumulated_shares + share_size
    amd_df$accumulated_shares[i] <- accumulated_shares
    total_investment <- total_investment + amd_df$costs_proceeds[i] # Adjusts total investment
    total_shares <- total_shares + share_size  # Adjusts total_shares
  }
  
  # Sets accumulated_shares & total_investment column to be the values at time
  amd_df$accumulated_shares[i] = accumulated_shares
  amd_df$total_investment[i] = total_investment
  
  # Adjust average_price at the end of every loop
  if (accumulated_shares > 0) {
    average_price <- -total_investment / total_shares
  }
  
  previous_price <- amd_df$close[i]
}

```


## Step 6: Summarize Your Findings
- Did your P/L and ROI improve over your chosen period?
- Relate your results to a relevant market event and explain why these outcomes may have occurred.


```{r}
# Calculate New Total Profit by summing all cost_proceeds and removing all NA values
if (any(is.na(amd_df$costs_proceeds))) {
  amd_df$costs_proceeds[is.na(amd_df$costs_proceeds)] <- 0
}
new_total_profit <- sum(amd_df$costs_proceeds, na.rm = TRUE)

# Calculate New Invested Capital by summing all 'buy' cost_proceeds and removing all NA values
new_invested_capital <- -sum(amd_df$costs_proceeds[amd_df$trade_type == 'buy'], na.rm = TRUE)

# Calculate New ROI
new_return_on_investment <- new_total_profit / new_invested_capital * 100 

```

Discussion:
Using the method in step 3 and 4, we can observe P/L closed at $714288.02 and ROI at 35.72%. Conversely, the method used in step 5 generated a P/L of $35688.99 and ROI of $17.98%. The lower P/L was due to an overall smaller capital investment, due to the algorithm choosing to repeatedly sell instead of buy for the majority of the model. 

We can relate this result to AMD's competitor, NVIDIA, whereby CEO Jensen Huang suggested the graphics card industry is shifting to incorporate greater artificial intelligence, "The computer industry is going through two simultaneous transitions—accelerated computing and generative AI." This statement surged interest in the computing industry as a whole, causing AMD's stock price to spike in May 2023. As a result, the closing prices after May 2023 increased past the average purchasing price, causing the repeated pattern of selling AMD stocks. This explains the relatively small overall capital investment, leading to the lower total profit. 






