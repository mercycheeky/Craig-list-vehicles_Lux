# Craig-list-vehicles_Lux   (Time Series Model)
## Task 
The project involves transforming the provided record-level dataset into  Time series model. The main objective of this model is to gain insights into the temporal patterns of vehicle listings, with a particular emphasis on conducting an inventory analysis
overtime, segmented by regions. For instance, the model should facilitate the creation of a time series chart that represents the number of vehicles available overtime, filtered by specific criteria such as region, vehicle type e.t.c.
This will aid in understanding regional demand-supply dynamics, seasonal trends and other relevant insights.

# Step 1
I will import the necessary packages, handle missing values,drop unnecessary columns and convert the "posting_date" to a datateime data type
```
# load all the necessary package that I will use in the notebook
import pytz
import warnings
import pandas as pd
import plotly.express as px
import matplotlib.pyplot as plt
import plotly.graph_objects as go
from statsmodels.tsa.seasonal import seasonal_decompose
warnings.filterwarnings('ignore', category=FutureWarning)

# load the dataset into my notebook
data_path = "data/craigslist_vehicles.csv"
data = pd.read_csv(data_path)

#preview the first 5 rows of 'craigslist_vehicles.csv' dataset
data.head()

#list the columns i have in my dataframe
data.columns

# check if columns exist before dropping
columns_to_drop = ['Unnamed: 0', 'url', 'region_url', 'VIN', 'image_url', 'description', 'county', 'lat', 'long', 'removal_date']

# filter the columns to drop only those that exist in the DataFrame
columns_to_drop_existing = [col for col in columns_to_drop if col in data.columns]

# drop the existing columns
data = data.drop(columns=columns_to_drop_existing)

# convert 'posting_date' to datetime data type
data['posting_date'] = pd.to_datetime(data['posting_date'],  utc=True)
#preview the first 5 rows of 'craigslist_vehicles.csv' dataset after droping unncessary columns and coverting "post)time" to date. 
data.head()
#check the column data types to confirm
data.dtypes


# then i handle missing values: I fill the numeric ones with mean and categorical ones with mode. 
def handle_missing_values(data):
    # fill missing numerical values with mean
    numerical_columns = ['year', 'odometer']
    data[numerical_columns] = data[numerical_columns].fillna(data[numerical_columns].mean())

    # fill missing categorical values with mode
    categorical_columns = ['manufacturer', 'model', 'condition', 'cylinders', 'fuel', 'title_status',
                           'transmission', 'drive', 'size', 'type', 'paint_color', 'posting_date']
    data[categorical_columns] = data[categorical_columns].apply(lambda x: x.fillna(x.mode().iloc[0]))

    return data
data = handle_missing_values(data)
```

# Step 2
After successfully handling the missing values and cleaning the data, I will move to the next step where I will aggregate the data based on the 'posting_date','region', and 'type' of vehicle to be able to analyze the temporal patterns, seasonal trends and demand-supply dynamics.
This will allow me to perform various analyses and gain insights into how the inventory varies overtime in different regions and vehicle types.

```
def convert_to_tz_aware(posting_date):
    if not posting_date.tzinfo:
        return posting_date.replace(tzinfo=pytz.utc)
    else:
        return posting_date

data['posting_date'] = data['posting_date'].apply(convert_to_tz_aware)

data_agg = data.groupby(['region', 'type', 'posting_date']).size().reset_index(name='count')

data_agg = data_agg.sort_values(by='posting_date')

print(data_agg.head())
![Screenshot 2024-02-14 075854](https://github.com/mercycheeky/Craig-list-vehicles_Lux/assets/56400871/2585cf27-112b-481f-a28a-b725f2d02749)


```






- You can get data used in this project from Kaggle, here https://www.kaggle.com/datasets/mbaabuharun/craigslist-vehicles
# My Approach:
- First I handled the missing values in the dataset. I resolved this by filling in the missing values with the median and mode of numerical and categorical data on the respective columns.
- Then, I converted the data types of the columns in the dataset to the appropriate data types, I converted the posting_date column should be converted to a datetime data type.
- I then used the posting_date column to create a datetime index for the dataset, this allowed me to easily analyze the temporal patterns of the data.
- Once I had clean data I explored it using different visualizations and statistical analysis approached. I used this step to analyze temporal patterns, seasonal trends, and demand-supply dynamics by region and vehicle type.And finally with a good understanding of the data, I created the time-series charts.

  Pandas Solution explained: https://colab.research.google.com/drive/1zW9k7r_TMHhftURnNpsc2njpuJrYzlSI#scrollTo=7mkc1zpI0kcA
