# ProducePicker

Code behind The Clairvoyant Cantaloupe, a tool to forecast the wholesale produce market.

# About
This repository contains the code I wrote as a Data Science Fellow at the 2018 Summer Insight Data Science Fellows session in NYC. 

The goal of my project was to enable restaurants to make better menu pricing and design decisions by forecasting the prices of produce ingredients. It's estimated that 80% of new restaurants fail within the first 5 years of operation, and a leading reason is how challenging it is to effectively price a menu. Ingredient costs, which represent up to 40% of operating costs, vary with factors such as seasonality and market trends. For this project, I used USDA wholesale produce data to develop a web app that forecasts produce costs in several US cities. You can find the web app at http://www.theclairvoyantcantaloupe.xyz. 

On the surface, this seemed like a pretty straightforward time series prediction problem, but a challenge I faced was the inconsistent nature of the data - pricing is irregularly sampled in time, long-term trends cause changes in average price, and each produce item has its own unique time-dependent pricing patterns. In particular, the sampling gaps in the data preclude the use of established time-series analysis algorithms like ARIMA.  

Instead, I used explored two different approaches. For the model I eventually used to generate the predictions shown on the web app, I did additive regression, modelling the price of each item as being composed of contributions from several sources - a long-term trend determined by a piecewise linear fit, as well as a shorter-term, seasonal component determined via Fourier analysis. To build this model I used Facebook's Prophet package.

I also explored using engineered features as inputs into XGBoost (boosted tree model used here as a regressor). For the features, I constructed typical lagging indicators like moving averages from each item's data, but I also included external data. For example, I ran a correlation analysis on all the produce data, and included features from items similar to the item being predicted. Additionally, I considered other external sources of data, including oil prices and several stock tickers with exposure to the wholesale produce market. 

Ultimately, the XGBoost model did perform slightly better than the additive regression, but as XGBoost is a bit of a black-box model, I selected the additive regression model as the final model due to how intuitive it is to interpret. The additive regression returned around 25% lower prediction errors, vs. my baseline model of carrying the last price forward as a prediction. 

# Code

The final model using Prophet is contained in prophet_test.ipynb, which contains the code I wrote to cross-validate, test, and run predictions. Before training my models, I used adjust_inflation.ipynb to convert pricing information to 2018 dollars using the CPI. After trianing, I used dump_to_pgsql.ipynb to store the predictions in my local Postgres DB. 

There are also numerous files for cleaning and downloading the data I used. The work I did with XGBoost is also contained in the repository. 

# Authors
Donald Lee-Brown
