# Dataset Generation Script with Groupby Operations and Stata Example

This script generates a synthetic dataset of cigarette consumption based on gender and city with corresponding weights for each data point. 

## Code (Python)

```python
import pandas as pd
import numpy as np
np.random.seed(42)

# Define the cities and genders
cities = ['New York', 'Los Angeles', 'Chicago']
genders = ['Female', 'Male']

# Define the number of samples for each city and gender
sample_sizes = {
    'Female': {'New York': 400, 'Los Angeles': 40, 'Chicago': 60},
    'Male': {'New York': 171, 'Los Angeles': 213, 'Chicago': 116}
}

# Define the weights corresponding to each gender and city
weights = {
    'Female': {'New York': 0.286/0.4, 'Los Angeles': 0.127/0.04, 'Chicago': 0.087/0.06},
    'Male': {'New York': 0.285/0.171, 'Los Angeles': 0.126/0.213, 'Chicago': 0.089/0.116}
}

# Create the dataset
data = []

for gender in genders:
    for city in cities:
        # Generate random sample data for each gender and city
        samples = pd.DataFrame({
            'gender': [gender] * sample_sizes[gender][city],
            'city': [city] * sample_sizes[gender][city],
            # Random number of cigarettes smoked (e.g., between 0 and 72)
            'y': np.random.randint(0, 73, size=sample_sizes[gender][city]),
            # Assign the weights based on gender and city
            'weight': [weights[gender][city]] * sample_sizes[gender][city]
        })
        data.append(samples)

# Combine all the data
df = pd.concat(data).reset_index(drop=True)

# Shuffle the dataset for randomness
df = df.sample(frac=1).reset_index(drop=True)

# Display the dataset
print(df)
```
## Preview dataset
```


   gender         city   y    weight
0  Female     New York  51  0.715000
1    Male  Los Angeles  69  0.591549
2    Male     New York  30  1.666667
3    Male  Los Angeles   2  0.591549
4    Male  Los Angeles  48  0.591549
...
995  Male  Los Angeles   1  0.591549
996  Male     New York  37  1.666667
997  Male  Los Angeles  54  0.591549
998  Female     New York   5  0.715000
999  Male      Chicago  45  0.767241
[1000 rows x 4 columns]
```
### Verify counts 
```
# Group by gender and print the size of each group
print(df.groupby('gender').size())

# Group by city and print the size of each group
print(df.groupby('city').size())

# Group by both gender and city, and show unique weight values
print(df.groupby(['gender', 'city'])['weight'].unique())

# Group by both gender and city, and show the size of each weight group
print(df.groupby(['gender', 'city'])['weight'].size())
```
### Grouped by Gender:

```
gender
Female    500
Male      500
dtype: int64
```
### Grouped by City:
```
city
Chicago        176
Los Angeles    253
New York       571
dtype: int64
``` 
### Unique Weights by Gender and City:
```
gender  city       
Female  Chicago                      [1.45]
        Los Angeles                 [3.175]
        New York       [0.7149999999999999]
Male    Chicago        [0.7672413793103448]
        Los Angeles    [0.5915492957746479]
        New York       [1.6666666666666663]
Name: weight, dtype: object
```
### Size of Each Gender-City Combination:
```
gender  city       
Female  Chicago         60
        Los Angeles     40
        New York       400
Male    Chicago        116
        Los Angeles    213
        New York       171
Name: weight, dtype: int64
```

## Code (Stata)
If you'd like to analyze the dataset using Stata, you can load it and perform group-wise analysis.

Load the dataset in Stata:
```
use "https://github.com/ivanadr/survey-weights/raw/c17914da6d93a69e94215f02050f3a1cd2afd597/sample.dta", clear
Display the first few rows to verify the data has loaded correctly:

list in 1/4
```

```
Sample output:

     +-----------------------------------------------+
     | index   gender          city    y      weight |
     |-----------------------------------------------|
  1. |     0   Female      New York   51        .715 |
  2. |     1     Male   Los Angeles   69    .5915493 |
  3. |     2     Male      New York   30   1.6666667 |
  4. |     3     Male   Los Angeles    2    .5915493 |
     +-----------------------------------------------+
```

```
tab gender city
```

```

           |               city
    gender |   Chicago  Los Ang..   New York |     Total
-----------+---------------------------------+----------
    Female |        60         40        400 |       500 
      Male |       116        213        171 |       500 
-----------+---------------------------------+----------
     Total |       176        253        571 |     1,000
```
