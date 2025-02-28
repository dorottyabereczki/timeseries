<h1>Time series analysis and forecasting</h1>

Electricity Spot Price Forecasting using SARIMA

This project forecasts electricity spot prices using SARIMA (Seasonal ARIMA) in Python. The model accounts for daily seasonality and provides short-term predictions.

This is a very simple model, only focusing on the prices. External variables should be included to improve the model such as weather, demand, gas prices, and renewable energy production, moreover the addition of factors like wind and solar forecasts could improve accuracy.

<h3>Project Overview</h3>

- Objective: Predict hourly electricity spot prices using historical data.

- Methodology: Uses SARIMA (Seasonal ARIMA) for forecasting.

- Dataset: Hourly electricity prices for Denmark.(from Kaggle)

- Outcome: Forecasts electricity prices for the next 24 hours.
  
<h3>Walk-through of the code and results:</h3>

<img src="https://imgur.com/kHYIkGV.png" height="80%" width="80%" alt="Importing libraries"/>
<br />
<h4>Data Preprocessing</h4>

Before diving into the modeling, I had to make sure the dataset was clean and properly formatted.

- Datetime Handling: The dataset contained timestamps in the HourDK column, which were converted to datetime format to allow for time-based indexing.

- Fixing Numerical Data: The SpotPriceDKK column was originally stored as a string with commas as decimal separators (a classic issue with European data). I replaced the commas with dots and converted it into a float.

- Indexing & Sorting: HourDK was set as the index, ensuring chronological order for accurate time series modeling.

- Handling Missing Data: Instead of dropping missing values, I used forward-fill interpolation (df.ffill()). This method works well for time series data where missing values are often due to short-term reporting gaps.

- Setting Frequency: The dataset was explicitly set to an hourly frequency (df.asfreq('h')). This is crucial for any time series model to correctly interpret the temporal structure.
<img src="https://imgur.com/OhEn7Hj.png" height="80%" width="80%" alt="Load and convert the dataset"/>
<br />
<br />
<h4>Exploratory Analysis</h4>

1. Visualizing the Time Series

Plotting the spot prices over time immediately showed clear daily seasonality—prices tend to peak during certain hours of the day, reflecting changes in electricity demand.

2. Seasonal Decomposition

I used statsmodels.tsa.seasonal_decompose to break down the time series into:

- Trend: The long-term movement of spot prices.

- Seasonality: Recurring daily patterns.

- Residuals: The noise that isn't explained by the trend or seasonality.

The seasonal component confirmed that prices fluctuate on a 24-hour cycle, with demand-driven spikes during mornings and evenings.

<img src="https://imgur.com/h3OYOLn.png" height="80%" width="80%" alt="Plot the time series"/>
<br />
<br />
<img src="https://imgur.com/pwQxNUU.png" height="80%" width="80%" alt="Plot"/>
<br />
<br />
<img src="https://imgur.com/3pt74IF.png" height="80%" width="80%" alt="Time series decomposition"/>
<br />
<br />
<img src="https://imgur.com/3KvYrKK.png" height="80%" width="80%" alt="Decomposition"/>
<br />
<br />
<h4>Stationarity Check</h4>

Before fitting a SARIMA model, I ran the Augmented Dickey-Fuller (ADF) test to check for stationarity.

ADF Test Results:

- ADF Statistic: -8.27

- p-value: 4.9e-13

Critical Values:

- 1%: -3.43

- 5%: -2.86

- 10%: -2.57

Interpretation: Since the ADF statistic is much lower than the critical values, and the p-value is close to zero, we reject the null hypothesis. The series is stationary, meaning it doesn’t have a persistent trend that needs further differencing.

<img src="https://imgur.com/JhaK47O.png" height="80%" width="80%" alt="Stationarity test"/>
<br />
<br />
<img src="https://imgur.com/qEZ7Sxp.png" height="50%" width="50%" alt="Result"/>
<br />
<br />
<h4>SARIMA Model for Forecasting</h4>

Given the strong 24-hour seasonality, I opted for a Seasonal ARIMA (SARIMA) model with below parameters:

order=(1,1,0):

- 1 autoregressive (AR) term to capture past dependencies.

- 1 differencing step to stabilize the series.

- No moving average (MA) terms since the ACF plot suggested it wasn’t necessary.

seasonal_order=(1,0,1,24):

- 1 seasonal AR term to handle daily seasonality.

- 0 seasonal differencing since the data was already stationary.

- 1 seasonal MA term to account for noise in the seasonal component.

- A 24-hour seasonal period to match the daily cycle of electricity prices.

The model was fitted using the L-BFGS optimizer with 50 iterations for efficiency.

<img src="https://imgur.com/Svhog1X.png" height="80%" width="80%" alt="SARIMA model"/>
<br />
<br />
<img src="https://imgur.com/WrD2wac.png" height="80%" width="80%" alt="SARIMA results"/>
<br />
<br />
<h4>Forecasting & Results</h4>

I generated 24-hour ahead forecasts and compared them with actual prices.

Key Takeaways from the Forecast:

- Prices increase in the morning (07:00-09:00) and evening (17:00-19:00) due to peak electricity demand.

- Lower prices overnight (02:00-05:00) suggest lower consumption and decreased market activity.

- The model captures the daily pattern reasonably well, though there are occasional unexplained spikes (likely due to external factors like unexpected demand surges or fuel price fluctuations).

<img src="https://imgur.com/HJTH5fc.png" height="80%" width="80%" alt="Forecasting"/>
<br />
<br />
<img src="https://imgur.com/cYZ8olf.png" height="50%" width="50%" alt="Plot"/>
<br />
<br />
<img src="https://imgur.com/2FOgKsr.png" height="80%" width="80%" alt="Forecasted values"/>
<br />
<br />
<img src="https://imgur.com/IlpwUSm.png" height="80%" width="80%" alt="Forecasted values"/>
<br />
<br />
