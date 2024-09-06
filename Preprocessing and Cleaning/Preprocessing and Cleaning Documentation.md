# Fight Data Preprocessing and Cleaning Script

This script is designed to preprocess and clean fight data, focusing on filtering relevant fights, calculating disparities between fighters, and splitting fight statistics for machine learning analysis. The cleaned dataset is saved as a CSV file.

## Function Descriptions (Ordered by Execution in `process_and_clean_data()`)

### 1. `filter_by_time_format(df, num_rounds=3)`
- **Description**: Filters the dataset to include only fights that match the specified number of rounds (default is 3). Removes irrelevant columns for the selected number of rounds (e.g., rounds 4 and 5 if `num_rounds=3`).

### 2. `filter_by_early_stoppage(df, stop_round=2)`
- **Description**: Filters the dataset to exclude fights that ended in or before the specified round (default is round 2), ensuring only longer fights are analyzed.

### 3. `filter_by_date(df, start_date, end_date=None)`
- **Description**: Filters the dataset based on a date range, keeping only fights that occurred between the `start_date` and the `end_date` (defaulting to the current date if `end_date` is not provided).

### 4. `drop_NaN_values(df)`
- **Description**: Removes rows with missing values from the dataset. Returns both the cleaned dataset and a separate dataset of rows that were dropped due to missing values.

### 5. `filter_by_gender(df, gender)`
- **Description**: Filters the dataset to include only fights from the specified gender division (e.g., 'men' or 'women').

### 6. `dob_to_age(dob_date, event_date)`
- **Description**: Converts a fighter's date of birth into their age at the time of the fight, including fractional years based on the event date.

### 7. `control_time_to_minutes(control_time)`
- **Description**: Converts control time from the 'MM:SS' format into total minutes for easier numerical analysis.

### 8. `attempted_landed_split(df, column_name)`
- **Description**: Splits columns with the format 'X of Y' (e.g., strikes landed out of attempts) into two separate columns for 'landed' and 'attempted' values.

### 9. `calculate_disparities(df, rounds=2)`
- **Description**: Calculates disparities in fight statistics (e.g., strikes, takedowns) between two fighters over the specified number of rounds (default is 2 rounds).

### 10. `split_fighter_statistics(df)`
- **Description**: Splits the fight data so each row represents one fighter's statistics for a fight, allowing individual analysis for each fighter as an independent sample.

### 11. `select_final_columns(df)`
- **Description**: Selects the final set of columns to be used in the cleaned dataset, focusing on relevant statistics for each fight and fighter performance.

### 12. `process_and_clean_data(file_path)`
- **Description**: The main processing function that applies all the above steps (filters, calculations, and transformations) to clean and preprocess the fight data. The cleaned data is saved to a CSV file.

---

## Overview of the `process_and_clean_data()` Function

The main function, `process_and_clean_data()`, orchestrates the preprocessing of the fight dataset by following these steps:

1. **Filter by rounds**: Filters out irrelevant rounds using the `filter_by_time_format()` function.

2. **Remove early stoppages**: Excludes early stoppages using the `filter_by_early_stoppage()` function.

3. **Filter by date**: Keeps fights within the desired date range using the `filter_by_date()` function.

4. **Drop missing values**: Cleans missing data using the `drop_NaN_values()` function.

5. **Filter by gender**: Keeps only fights from the specified gender using the `filter_by_gender()` function.

6. **Calculate age and control time**: Converts date of birth to age and control time to minutes using the `dob_to_age()` and `control_time_to_minutes()` functions.

7. **Split 'attempted of landed' columns**: Splits columns with 'X of Y' format using the `attempted_landed_split()` function.

8. **Calculate disparities**: Computes statistical disparities between fighters using the `calculate_disparities()` function.

9. **Split fighter data**: Creates independent rows for each fighter using the `split_fighter_statistics()` function.

10. **Select final columns**: Keeps only the relevant columns using the `select_final_columns()` function.

11. **Save the cleaned dataset**: Outputs the cleaned dataset to a CSV file.

This systematic approach ensures that the data is well-prepared for further analysis.

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
