# Fight Data Preprocessing and Cleaning Script

After gathering a comprehensive dataset that includes every event, fight, and round statistic, we now focus on preparing the data for analysis. Our goal is to predict the outcome of three-round (non-title) fights fought in the men's division using real-time performance metrics from the first two rounds.

This script is designed to preprocess and clean fight data, focusing on filtering relevant fights, calculating disparities between fighters, and splitting fight statistics for machine learning analysis. The cleaned dataset is saved as a CSV file.

### Exclusion of Women's and Title Fights
We exclude women's and title fights because of the limited sample size. The vast majority of MMA fights are fought in the men's division, and of those, only a small fraction are title fights (which span 5 rounds). To ensure consistent and reliable predictions, our model focuses exclusively on three-round fights, where the first two rounds provide sufficient data for analysis.

### Exclusion of Early Stoppages
Fights that end before the second round are excluded from our analysis, as these early stoppages do not yield enough in-round statistics to meet the requirements of our model. By filtering for fights that make it past round two, we ensure that our dataset is comprehensive enough to provide meaningful insights based on fighter performance over two rounds.

### Modern Data Focus
Given the rapid evolution of MMA over the past decade, it’s important to focus on the most relevant data. Therefore, we limit our dataset to fights from the last ten years. This helps ensure that the model's predictions are based on modern fight dynamics, making it more applicable to the current landscape of the sport.

### Calculation of Performance Disparities
Instead of simply analyzing raw statistics for each fighter, we calculate performance **disparities** — for example, the difference in strikes landed or control time between fighters. This approach gives us a clearer understanding of how each fighter performed relative to their opponent, which is a more accurate predictor of fight outcomes.

### Addressing Multicollinearity
Certain statistics, like total strikes landed, significant strikes attempted, and significant strikes to the head, are excluded from our model to avoid **multicollinearity**. Multicollinearity occurs when highly correlated variables are included, which can reduce the model's accuracy and make it difficult to interpret. 

We also exclude reversals, distance strikes, clinch strikes, and ground strikes, as they are not consistently available in real-time.


## Function Descriptions (Ordered by Execution in `process_and_clean_data()`)

### 1. `filter_by_time_format(df, num_rounds=3)`
- **Description**: Filters the dataset to include only fights that match the specified number of rounds (default is 3). This step ensures that only three-round non-title fights, which are the focus of our model, are retained. Irrelevant columns for rounds 4 and 5 are removed to streamline the dataset.

### 2. `filter_by_early_stoppage(df, stop_round=2)`
- **Description**: Filters the dataset to exclude fights that ended in or before the second round. This ensures that only fights with complete statistics for rounds 1 and 2 are included, as our model relies on these rounds for prediction.

### 3. `filter_by_date(df, start_date, end_date=None)`
- **Description**: Filters the dataset based on a date range, keeping only fights that occurred between the `start_date` and the `end_date`. We limit the data to the last ten years to ensure that the model is trained on modern MMA fights, reflecting recent trends and techniques in the sport.

### 4. `drop_NaN_values(df)`
- **Description**: Removes rows with missing values from the dataset. This ensures that the dataset is complete and free of inconsistencies, which is essential for reliable machine learning analysis.

### 5. `filter_by_gender(df, gender)`
- **Description**: Filters the dataset to include only fights from the specified gender division. Since we are focusing on the men's division due to the larger sample size, this step helps streamline the dataset.

### 6. `dob_to_age(dob_date, event_date)`
- **Description**: Converts a fighter's date of birth into their age at the time of the fight, including fractional years based on the event date. Fighter age is a critical variable as it can affect performance, and this transformation allows us to include it as a predictor.

### 7. `control_time_to_minutes(control_time)`
- **Description**: Converts control time from the 'MM:SS' format into total minutes for easier numerical analysis. Control time is a key metric in determining a fighter’s ability to dominate their opponent.

### 8. `attempted_landed_split(df, column_name)`
- **Description**: Splits columns with the format 'X of Y' (e.g., strikes landed out of attempts) into two separate columns for 'landed' and 'attempted' values. This split enables us to analyze the efficiency and accuracy of a fighter's performance.

### 9. `calculate_disparities(df, rounds=2)`
- **Description**: Calculates disparities in fight statistics (e.g., strikes, takedowns) between two fighters over the first two rounds. By focusing on disparities, we gain insight into how one fighter's performance compares to their opponent, which is a stronger predictor of fight outcomes than raw stats.

### 10. `split_fighter_statistics(df)`
- **Description**: Splits the fight data so each row represents one fighter's statistics for a fight, allowing individual analysis for each fighter as an independent sample. This restructuring is important for machine learning, where each row corresponds to a single fighter's performance.

### 11. `select_final_columns(df)`
- **Description**: Selects the final set of columns to be used in the cleaned dataset. Only the most relevant statistics are retained, with certain metrics excluded to avoid multicollinearity or due to incomplete real-time reporting.

### 12. `process_and_clean_data(file_path)`
- **Description**: The main processing function that applies all the above steps (filters, calculations, and transformations) to clean and preprocess the fight data. The cleaned dataset is saved to a CSV file, ready for machine learning analysis.

---

## Overview of the `process_and_clean_data()` Function

The main function, `process_and_clean_data()`, orchestrates the preprocessing of the fight dataset by following these steps:

1. **Filter by rounds**: Filters out irrelevant rounds using the `filter_by_time_format()` function to focus on three-round non-title fights, which form the majority of our dataset and are more consistently reported.

2. **Remove early stoppages**: Excludes early stoppages using the `filter_by_early_stoppage()` function, ensuring we have complete statistics for the first two rounds, which are critical for our model.

3. **Filter by date**: Keeps fights within the desired date range using the `filter_by_date()` function to ensure we are working with modern, relevant data from the past 10 years.

4. **Drop missing values**: Cleans missing data using the `drop_NaN_values()` function to ensure the dataset is complete and reliable.

5. **Filter by gender**: Keeps only fights from the men's division using the `filter_by_gender()` function due to the larger sample size and more consistent data.

6. **Calculate age and control time**: Converts date of birth to age and control time to minutes using the `dob_to_age()` and `control_time_to_minutes()` functions, respectively, as both are important metrics for performance analysis.

7. **Split 'attempted of landed' columns**: Splits columns with 'X of Y' format using the `attempted_landed_split()` function to isolate landed vs attempted actions for greater analytical precision.

8. **Calculate disparities**: Computes statistical disparities between fighters using the `calculate_disparities()` function, as relative performance is a stronger predictor than absolute numbers.

9. **Split fighter data**: Creates independent rows for each fighter using the `split_fighter_statistics()` function to make the data suitable for machine learning analysis.

10. **Select final columns**: Keeps only the relevant columns using the `select_final_columns()` function, ensuring the model is trained on the most meaningful variables.

11. **Save the cleaned dataset**: Outputs the cleaned dataset to a CSV file, ready for machine learning analysis.

```python
# Main processing function
def process_and_clean_data(file_path):
    """
    The main function for preprocessing and cleaning fight data. It applies a series of filters, calculations, and transformations 
    to prepare the dataset for analysis.
    
    Parameters:
    - file_path (str): The path to the CSV file containing raw fight data.

    Returns:
    - None: Saves the cleaned dataset to a CSV file called 'final_cleaned_data.csv'.
    """
    df = pd.read_csv(file_path)
    df.replace(['--', ''], pd.NA, inplace=True)

    # Filter by rounds and other steps
    df = filter_by_time_format(df, num_rounds=3)
    df = filter_by_early_stoppage(df, stop_round=2)
    df = filter_by_date(df, start_date="2014-01-01")
    df, dropped_rows = drop_NaN_values(df)
    df = filter_by_gender(df, gender="men")

    # Convert DOB to age and control time to minutes
    dob_columns = ['fighter_a_dob', 'fighter_b_dob']
    for col in dob_columns:
        df[col.replace('_dob', '_age')] = df.apply(lambda row: dob_to_age(row[col], row['event_date']), axis=1)

    control_columns = [col for col in df.columns if 'control_time' in col]
    for col in control_columns:
        df[col] = df[col].apply(control_time_to_minutes)

    # Split columns with "Attempted of Landed" format
    columns_to_split = [
        'rnd_1_a_sig_strikes', 'rnd_1_b_sig_strikes', 'rnd_1_a_total_strikes', 'rnd_1_b_total_strikes',
        'rnd_1_a_takedowns', 'rnd_1_b_takedowns', 'rnd_1_a_body_strikes', 'rnd_1_b_body_strikes',
        'rnd_1_a_leg_strikes', 'rnd_1_b_leg_strikes', 'rnd_2_a_sig_strikes', 'rnd_2_b_sig_strikes',
        'rnd_2_a_total_strikes', 'rnd_2_b_total_strikes', 'rnd_2_a_takedowns', 'rnd_2_b_takedowns',
        'rnd_2_a_body_strikes', 'rnd_2_b_body_strikes', 'rnd_2_a_leg_strikes', 'rnd_2_b_leg_strikes'
    ]

    for col in columns_to_split:
        attempted_landed_split(df, col)

    # Calculate disparities for the first two rounds and sum them
    df = calculate_disparities(df, rounds=2)

    # Split fighter statistics into individual rows
    df = split_fighter_statistics(df)

    # Select final columns for the cleaned dataset
    df_final = select_final_columns(df)

    # Save the final dataset to a new CSV file
    df_final.to_csv('final_cleaned_data.csv', index=False)
    print("Final cleaned data has been saved as 'final_cleaned_data.csv'")
```


## Usage

To use this script, place your raw fight data CSV file in the appropriate location, specify the file path in the `process_and_clean_data()` function, and run the script. The cleaned data will be saved as `final_cleaned_data.csv` in the current directory.

```python
file_path = r"your_raw_data_path"
process_and_clean_data(file_path)
```

To use this script, place your raw fight data CSV file in the appropriate location, specify the file path in the
`process_and_clean_data()` function, and run the script. The cleaned data will be saved as `final_cleaned_data.csv`
in the current directory.
