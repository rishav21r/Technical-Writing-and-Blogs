# Mastering Data Cleaning: 5 Simple Steps to Automate with Python

In our world that relies on data, it's really important to have clean data to get the right answers and make good choices. Manually cleaning data can take a lot of time and can have mistakes. Python has strong tools to clean data automatically, making it faster and more accurate. 

In this article, I'll show you 5 easy steps to clean data automatically with Python.

## Step 1: Setting Up Your Environment

Before we start cleaning up our data, let's get Python ready. First, make sure you have Python installed on your computer. Then, let's get the tools we need by installing these libraries:

```python
pip install pandas numpy
```

These libraries are our main tools for working with and understanding data. Pandas give us DataFrames, which are perfect for cleaning up messy data, while NumPy helps us work with large amounts of data organized in rows and columns.

## Step 2: Handling Data Formats

Data can come in many forms, like CSV files, JSON files, or even Excel spreadsheets. Each type needs a different way to open and work with it. Pandas has tools to help with this, making it simple to bring your data into a DataFrame like a table with rows and columns. Let me explain how and show you the Python code for different data types:

#### Code Explained:

The read_data(file_path) function takes the location of your file and figures out what type of file it is (CSV, JSON, etc.). Then, it uses the right Pandas tool to open the file and put the data into a DataFrame so you can start working with it.

```python
import os
import pandas as pd

def read_data(file_path):
    # Extract the file extension
    _, file_ext = os.path.splitext(file_path)
    
    # Read data based on file extension
    if file_ext == '.csv':
        return pd.read_csv(file_path)
    elif file_ext == '.json':
        return pd.read_json(file_path)
    elif file_ext in ['.xls', '.xlsx']:
        return pd.read_excel(file_path)
    else:
        raise ValueError("Unsupported file format")

# Load your dataset
df = read_data('your_dataset.csv')

```

#### Detailed Steps:
##### 1. Extract the File Extension:
- The function uses `os.path.splitext(file_path)` to split the file path into two parts: the root and the extension. The extension is stored in `file_ext`.

##### 2. Conditional Reading Based on Extension:

- If the file extension is `.csv`, it uses `pd.read_csv(file_path)` to read the data into a DataFrame.
- If the file extension is `.json`, it uses `pd.read_json(file_path)`.
- If the file extension is `.xls` or `.xlsx`, it uses `pd.read_excel(file_path)`.
- If the file format is unsupported, it raises a ValueError with an appropriate message.

This function ensures you can handle multiple data formats with a single function call, making your data import process more flexible and efficient. Let’s break down the supported formats:

- CSV (Comma-Separated Values): It is commonly used for data exchange because it’s simple and supported by many applications.
- JSON (JavaScript Object Notation): Used for transmitting data in web applications. 
- Excel Files: Widely used for data storage and analysis in business contexts.

## Step 3: Dealing with Duplicates

Duplicate data can skew your analysis and lead to incorrect conclusions. Eliminating duplicates ensures that each piece of information in your dataset is unique, giving you a clearer picture of the data. Let's delve deeper into how to tackle duplicates and break down the Python code that makes it happen.

### Code Explanation
The `drop_duplicates(df, columns=None)` function acts as a data detective, meticulously identifying and removing duplicate rows lurking within your DataFrame.

```python
def drop_duplicates(df, columns=None):
    if columns is None:
        df.drop_duplicates(inplace=True)
    else:
        df.drop_duplicates(subset=columns, inplace=True)
    return df

# Apply the function to your dataset
df = drop_duplicates(df)
```

### Detailed Steps:

#### 1. Check for Duplicates:

- The function `drop_duplicates` takes a DataFrame `df` and an optional parameter columns.
- If columns are not provided, the function checks for duplicates across all columns.
- If columns are specified, it checks for duplicates only within the specified subset of columns.

#### 2. Remove Duplicates:

- `df.drop_duplicates(inplace=True)` removes duplicate rows directly in the original DataFrame if no columns are specified.
- `df.drop_duplicates(subset=columns, inplace=True)` removes duplicate rows based on the subset of columns specified.

#### 3. Return the Cleaned DataFrame:

- The function returns the cleaned DataFrame with duplicates removed.

#### Example:
Assume you have a DataFrame with the following data:

| Name | Age | Gender | Country |
|------|-----|--------|---------|
| John | 28  | Male   | USA     |
| Anna | 22  | Female | UK      |
| John | 28  | Male   | USA     |
| Mike | 32  | Male   | Canada  |


To remove duplicate rows based on all columns:

```python
df = drop_duplicates(df)
```

This will result in:

| Name | Age | Gender | Country |
|------|-----|--------|---------|
| John | 28  | Male   | USA     |
| Anna | 22  | Female | UK      |
| Mike | 32  | Male   | Canada  |


To remove duplicate rows based on specific columns, say `['Name', 'Age']`:

```python
df = drop_duplicates(df, columns=['Name', 'Age'])
```

This will result in the same DataFrame as above since the duplicate row was identified by matching the Name and Age columns. Removing duplicates helps ensure that your dataset contains unique observations, which is crucial for accurate analysis and results.


## Step 4: Handling Missing Values

Missing values are a frequent hurdle in data analysis. However, Python equips us with the tools to address them effectively.  Whether your strategy involves eliminating rows with missing data or employing imputation techniques, understanding the available methods and their corresponding code is key. Let's delve into the details and empower ourselves to make informed decisions about our data.

### Code Explanation

The process involves two main functions: check_missing_data(df) and dealing_missing_data(df). The check_missing_data(df) function assesses the proportion of missing values in the DataFrame and decides whether to drop rows with missing values or handle them column by column. The dealing_missing_data(df) function then addresses missing values by either imputing them or flagging them for further inspection.

Two primary functions drive this process: `check_missing_data(df)` evaluates the extent of missing values and determines the handling strategy (row deletion or column-wise imputation). Subsequently, `dealing_missing_data(df)` executes the chosen approach, either filling in missing values or marking them for review.

```python
def check_missing_data(df):
    # Calculate the proportion of rows with any missing values
    proportion_null_rows = 100 * (df.isnull().any(axis=1).sum() / df.shape[0])
    
    if proportion_null_rows <= 5:
        print(f"There are {df.isnull().any(axis=1).sum()} rows with null values. All of them are erased!")
        df.dropna(inplace=True)
    else:
        print("Too many null values, we need to check columns by columns further.")
        if df.isnull().sum().sum() > 0:
            print("\nProportion of missing values by column")
            values = 100 * (df.isnull().sum() / df.shape[0])
            print(values)
            dealing_missing_data(df)
        else:
            print("No missing values detected!")

def dealing_missing_data(df):
    # Handle the missing values
    values = 100 * (df.isnull().sum() / df.shape[0])
    to_impute = []
    to_check = []
    
    for name, proportion in values.items():
        if int(proportion) == 0:
            continue
        elif int(proportion) <= 10:
            to_impute.append(name)
            df[name].fillna(df[name].median(), inplace=True)
        else:
            to_check.append(name)
    
    print(f"\nThe missing values in {to_impute} have been replaced by the median.")
    print(f"The columns {to_check} should be further investigated.")

# Apply the function to your dataset
check_missing_data(df)
```
