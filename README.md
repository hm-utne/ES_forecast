# Forecasting Day-Ahead Risk

This repository contains a general codebase for forecasting Value at Risk (VaR) and Expected Shortfall (ES) using the methodology detailed in the master’s thesis:

**"Forecasting Day-Ahead EURUSD Exchange Rate Risk: Leveraging the Volatility Surface and Machine Learning"**  
(Moen, Pedersen & Utne, 2024)

## Overview

The code utilizes a rolling window approach with CatBoost, LightGBM, and XGBoost to forecast one-day-ahead ES. It leverages a dataset containing information from the volatility surface to make predictions.

The models predict VaR and ES at various confidence levels (1%, 2.5%, 5%, 95%, 97.5%, and 99%) to capture tail risks. The ES estimation process requires preliminary VaR estimation as a foundational step. The code generates a refined distribution of potential losses based on these VaR values and calculates ES by averaging these VaR estimates across the generated quantiles. To maintain logical consistency, the code includes a function to prevent quantile crossing. For efficiency, the code also utilizes parallel processing.

## Getting Started

### Prerequisites

- Python 3.x
- Required libraries: `pandas`, `numpy`, `matplotlib`, `seaborn`, `joblib`, `sklearn`, `xgboost`, `catboost`, `lightgbm`
- Data for the volatility surface and realized returns

### Data Preparation

Data should be organized in an Excel file with columns for date, actual return data, and explanatory variables.

### Running the Code

1. **Define Functions**: In the `1. Functions` section, specify the target variable in the `sort_columns_descending` function. For example:

    ```python
    # Function to sort columns in correct order
    def sort_columns_descending(df):
        df1 = df
        # Specify the suffixes to remove
        suffixes_to_drop = [".1", ".2", "True Brent Returns", 'Date']  # Add more suffixes as needed
    ```

2. **Model Specification and Forecasting**: Define the model, quantile regression, and plotting in the `2. Model specification and forecasting` section. Adjust parameters such as hyperparameters, window size, and plotting as needed.

3. **Run Model Variants**: In the `3. Running all model variants` section, modify the file path in the `run_quantile_regression_rolling_window` function to point to your data file:

    ```python
    # Run the quantile regression with the current feature set
    run_quantile_regression_rolling_window('Brent.xlsx', 'Sheet1', 'Date', 'Brent Returns', features)
    ```

    Execute the script. It will generate an Excel file with the VaR and ES predictions for all model variants. Comment out model variants as needed to focus on specific ones.

4. **Handle Quantile Crossing**: Use the functions in `1. Functions` to address quantile crossing in `4. Handle quantile crossing`. Specify the model variant to correct quantile crossing using the input file "file_pathtest" (the raw ES_VaR predictions from Step 3) and output to "outputname".

    ```python
    file_pathtest = '/content/IV_Slope_ES_VaR_catboost.xlsx'
    temp_data = pd.read_excel(file_pathtest)
    temp_data_ordered = sort_columns_descending(temp_data)
    temp_data_corrected = ensure_non_crossing(temp_data_ordered)
    ```

    Adjust column names to remove any suffixes:

    ```python
    temp_data.columns = [col.rstrip(".1") if col.endswith(".1") else col for col in temp_data.columns]
    temp_data.columns = [col.rstrip(".2") if col.endswith(".2") else col for col in temp_data.columns]
    ```

5. **Estimate ES**: In the `5. Estimate ES` section, specify the model variant for estimating and downloading the ES estimates. Use the output from Step 4 as input, generating a file with the final ES estimates.

### Considerations

The provided hyperparameters are tuned for use on various datasets. Adjust them as needed for specific cases.

## Reference

For a detailed explanation of the methodology and findings, refer to the master’s thesis:

Moen, I. A., Pedersen, M. S., & Utne, H. M. (2024). *Forecasting Day-Ahead EURUSD Exchange Rate Risk: Leveraging the Volatility Surface and Machine Learning*. Master’s thesis, Norwegian University of Science and Technology.

## Contact

For questions or feedback, please contact [@hm-utne](https://github.com/hm-utne).
