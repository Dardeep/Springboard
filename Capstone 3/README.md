
# Solar Energy Co. Campaign optimization

This project is designed to help a solar energy company’s marketing team determine which city in the USA will offer the highest potential return on investment (ROI) for a targeted marketing campaign. The campaign aims to promote solar panel installations in areas where consumers are most likely to benefit from solar energy. The project leverages a combination of solar irradiance data, demographic data, and energy cost data to create a data-driven recommendation for city selection.


## Dataset Overview

The project uses three primary datasets:

#### 1. Solar Irradiance Data (NASA POWER API)
- This dataset contains daily solar irradiance details for 200 cities across the USA.
     #### Parameters:     
  * `ALLSKY_SFC_SW_DWN`: All Sky Surface Shortwave Downward Irradiance (kW-hr/m²/day)
  * `ALLSKY_SFC_UV_INDEX`: All Sky Surface UV Index
  *  `T2M`: Temperature at 2 Meters (Celsius)
  * `WS10M`: Wind Speed at 10  Meters (m/s)
  * And many other climate-related variables.     


#### 2. Demographic Data (U.S. Census Bureau)
- This dataset includes population, median household income, median home value, and housing unit details.
  #### Key Columns:     
  * `Total_Population`: Total population of the city
  * `Median_Household_Income`: Median income of households in the city.
  * `Median_Home_Value`: Median home value in the city.
  * `Total_Housing_Units`: Total number of housing units.
     

#### 3. Energy Data (EIA - Energy Information Administration)
- This dataset contains monthly residential electricity price and sales data from January 2021 to May 2024.
  #### Key Columns:     
  * `price`: Average residential electricity price (cents per kWh).
  * `sales`: Total electricity sales (million kWh).
  * `state`: U.S. state associated with the data.
   


                    Note:
                    (API Reference can be found below Appendix)
## Project Structure

The project is divided into the following phases:



### 1. Data Collection
- API references can be found under Appendix.
     #### Source APIs:     
  * NASA POWER API for solar irradiance and climate data.
  * U.S. Census Bureau API for demographic data.
  *  EIA API for energy consumption and cost data.


    
    
   #### Methodology:     
  * Data was fetched via APIs and stored in CSV files.
  * Data was updated to include the most recent information available.
  * NASA POWER API requires Latitude and Longitude when searching for City. 
  



### 2. Data Wrangling and EDA
- The data wrangling and exploratory data analysis (EDA) phase involved the following
  #### Solar Irradiance Data:     
  * **Cleaning:** Handled missing values and anomalies such as negative values for irradiance.
  * **Exploration:** Analyzed trends in solar irradiance across different cities and seasons.
  * **Visualization:** Created plots to visualize the distribution and trends of key variables such as solar irradiance and temperature.
  
  #### Census Data:     
  * **Standardization:** Standardized city names, removed duplicates, and cleaned up the dataset.
  * **Aggregation:** Calculated relevant statistics like the total number of 1-unit housing for each city.
  * **Analysis**: Examined the relationship between homeownership rates, income levels, and potential solar adoption.
  
  #### Energy Data:    
  * **Transformation:** Converted data types (e.g., dates), and standardized column names.
  * **Exploration:** Analyzed energy prices and consumption trends across different states.
  * **Correlation** Analysis: Investigated correlations between energy costs and solar potential.

### 3. Modeling
  
  
  #### Time Series Forcast:     
  * **Objective:** Predict future solar irradiance based on historical data.
  * **Model Used:** LSTM and time series forecasting methods.
  * **Outcome:** Provided forecasts for solar irradiance to predict the potential solar energy yield in different cities

  #### Scoring System:     
  * **Objective:** Develop a scoring system to rank cities based on solar potential, energy costs, and demographics.
  * **Weights:**
    * Solar Irradiance: 35%
    * Median Household Income: 15%
    * Median Home Value: 15%
    * 1-Unit Homes: 15%
    * Energy Prices: 10%
    * Total Electricity Sales: 10%      
  * **Outcome:** Produced a ranked list of cities, highlighting those with the highest potential ROI for solar energy campaigns.

## Visualization

To effectively communicate the results:

- #### Top 10 Cities:
  * **Bar Graphs:** Generated for each of the top 10 cities, showing the monthly `Final_Weighted_Score`. These graphs provide a visual representation of how each city ranks over time, highlighting trends and seasonal variations
  * **Mean Score Line:** A horizontal line on each graph shows the mean final score, labeled with the exact value, providing a quick reference for comparison.

## Key Findings

* **Sunnyvale, California,** was identified as the top city for Solar Energy Co.'s marketing campaign based on the weighted scoring system.

* The project demonstrates the flexibility of adjusting the weighting of different factors to reflect changing strategic priorities or new data inputs.
## Challenges

#### API Issues
- **Latitude and Longitude Requirement:** The NASA Power API required latitude and longitude for each city, complicating the data collection process.
- **Long Processing Time:** The API took approximately 7 hours to complete data retrieval for all cities, significantly slowing down the workflow.
- **Timeouts:** Frequent timeouts were encountered, necessitating the implementation of a sleep feature to manage API requests effectively.


#### Data Imputation
- **Missing UV Index Data:**
    -  There were 5338 missing entries for `ALLSKY_SFC_UV_INDEX.`
    - The approach involved finding matching rows based on city, `UVA`, and `UVB` values, with an initial tolerance of 0.02 for differences. If no matches were found, the tolerance was increased to 0.05. The average value from matching rows was imputed, or `NaN` was returned if no suitable match was found.
    - To further improve the accuracy and completeness of the data, a linear regression model was employed to fill in the missing UV Index values. The model produced the following performance metrics:

            `Mean Absolute    Error (MAE): 0.0239`
        
            `Mean Squared Error (MSE): 0.00096`

            `Root Mean Squared Error (RMSE): 0.0311`
      
            `R-squared (R²): 0.9986`
    -  This regression-based approach effectively resolved the issue, significantly improving the dataset's completeness and reliability.


#### Modeling Stage
During the modeling stage, we encountered significant challenges when trying to forecast the time series data.

- **ARIMA/SARIMA:** Initially, ARIMA was considered for forecasting each time series variable independently. However, this approach required filtering the data for each city/state combination and applying the model separately to each variable. This was cumbersome and didn't account for the interactions between multiple variables.

- **Multivariate Time Series Modeling:** To address the limitations of ARIMA, we explored using Vector Autoregression (VAR), which allows for handling multiple time series simultaneously and modeling the relationships between them. While VAR provided a way to manage multiple time series, the downside was that we still had to apply the model individually to each city. Additionally, we faced complications with cities sharing the same name across different states, necessitating a combination of city and state identifiers to avoid inaccuracies.

- **Challenges with VAR:** When implementing VAR, we encountered issues with generating negative values in the forecasts. This could have been due to the data not being fully stationary—a critical requirement for VAR models. Given the complexity of the dataset and the difficulties in ensuring stationarity across all variables, this approach proved to be less effective.

- **Resolution with LSTM Model:** To overcome these challenges, we ultimately resorted to using a Long Short-Term Memory (LSTM) model. LSTM, a type of recurrent neural network, is well-suited for capturing temporal dependencies and handling complex, non-linear relationships in time series data. This approach provided more accurate and reliable forecasts without the need to handle each variable and city/state combination independently, making it a more scalable solution for the project.




## How to Use This Project


#### 1. Data Preparation:
- Ensure all datasets (solar, census, EIA) are properly formatted and loaded into the project.
- Follow the data merging and normalization steps to prepare the data for analysis.

#### 2. Scoring and Ranking:
- Adjust the weights in the weights dictionary if necessary to reflect specific business objectives.
- Run the scoring model to generate a new ranking of cities based on the updated criteria.

#### 3. Visualization:
- Use the provided code to generate bar graphs for the top cities, helping stakeholders easily interpret the results.
- Review the visualizations to understand how different cities perform over time and under different scoring scenarios.
## Requirements

The following Python libraries and packages are required to run the project:

- **pandas:** For data manipulation and analysis.
- **numpy:** For numerical computing and handling large arrays.
- **seaborn:** For data visualization, especially for generating attractive and informative statistical graphics.
- **statsmodels:** Specifically, the tsa.vector_ar.var_model.VAR module is used for implementing Vector Autoregression (VAR) models.
- **matplotlib:** For creating static, animated, and interactive visualizations in Python.
- **scikit-learn:** Provides tools for data preprocessing, model evaluation, and machine learning algorithms. In this project, it is used for:
  - **MinMaxScaler:** To normalize data before feeding it into machine learning models.
  - **mean_absolute_error, mean_squared_error, r2_score:** For evaluating model performance.
  - **GridSearchCV:** For hyperparameter tuning.
  - **TimeSeriesSplit and cross_val_score:** For time series cross-validation and scoring.
- **tensorflow:** Specifically, TensorFlow’s Keras API is used for building and training Long Short-Term Memory (LSTM) neural networks.
  - **Sequential:** To initialize the neural network model.
  - **LSTM:** A type of recurrent neural network layer, effective for time series forecasting.
  - **Dense:** A regular fully connected neural network layer.
- **keras (specifically wrappers.scikit_learn.KerasRegressor):** For integrating Keras models into scikit-learn, making it easier to perform hyperparameter tuning.
## Appendix

Additional references


 ### API Reference

#### NASA API 

```http
  https://github.com/Dardeep/Springboard/blob/main/Capstone%203/notebooks/API/NASA%20API.ipynb

  "https://power.larc.nasa.gov/api/temporal/daily/point"
```

| Parameter | Type     | Description                |
| :-------- | :------- | :------------------------- |
| `api_key` | `string` | **N/A**. Your API key |

#### US Census API

```http
https://github.com/Dardeep/Springboard/blob/main/Capstone%203/notebooks/API/Census%20API.ipynb

https://api.census.gov/data/2021/acs/acs5?get=NAME

```

| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `api_key`      | `string` | **275b5f346b7d21848c7d080e4d742ee5c7c45d9c**. API Key |

#### EIA API 

```http
https://github.com/Dardeep/Springboard/blob/main/Capstone%203/notebooks/API/EIA%20API.ipynb

  "https://api.eia.gov/v2/electricity/retail-sales/data/"
```

| Parameter | Type     | Description                |
| :-------- | :------- | :------------------------- |
| `api_key` | `string` | **Z3PEB7JFkYmEIkrmU4tb3nOLZjBEI77Poq44MIdk**. Your API key |
