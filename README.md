# Craig-list-vehicles_Lux   (Time Series Model)
## Task 
The project involves transforming the provided record-level dataset into a Time series model. The main objective of this model is to gain insights into the temporal patterns of vehicle listings, with a particular emphasis on conducting an inventory analysis
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

```
![Screenshot 2024-02-14 075854](https://github.com/mercycheeky/Craig-list-vehicles_Lux/assets/56400871/f7367f68-5b94-41a0-86a5-1c3ed2d874a5)

```
# Create an interactive time-series chart
fig = px.line(data_agg, x='posting_date', y='count', color='region', line_group='type',
              title='Number of Available Vehicles Over Time by Region and Vehicle Type',
              labels={'count': 'Number of Vehicles'})

# Customize the layout
fig.update_layout(
    xaxis_title='Posting Date',
    yaxis_title='Number of Vehicles',
    hovermode='x',
    showlegend=True,
)

# Show the chart
fig.show()
```

![Screenshot 2024-02-14 081533](https://github.com/mercycheeky/Craig-list-vehicles_Lux/assets/56400871/8c69c5a1-353e-47d6-b553-dc0feee3b2b7)

![Screenshot 2024-02-14 081726](https://github.com/mercycheeky/Craig-list-vehicles_Lux/assets/56400871/e132e7fe-ed33-498d-a241-dae8048054fc)

```
# group the data by day and count the number of listings
data_freq = data_agg.groupby(pd.Grouper(key='posting_date', freq='D')).sum().reset_index()

# create the time frequency graph
fig_freq = go.Figure(data=go.Bar(
    x=data_freq['posting_date'],
    y=data_freq['count'],
    marker_color='royalblue',
    opacity=0.8
))

# customize the layout
fig_freq.update_layout(
    title='Time Frequency Graph: Number of Vehicle Listings per Day',
    xaxis_title='Posting Date',
    yaxis_title='Number of Vehicle Listings',
    xaxis_tickangle=-45,
)

# show the time frequency graph
fig_freq.show()
```

![Screenshot 2024-02-14 081821](https://github.com/mercycheeky/Craig-list-vehicles_Lux/assets/56400871/e219ee3d-9146-43cc-9c11-750623f73718)

```
# perform seasonal decomposition
data_agg = data_agg.set_index('posting_date')
result = seasonal_decompose(data_agg['count'], model='additive', period=365)

# create a new DataFrame to store the decomposition components
decomposed_data = pd.DataFrame({
    'trend': result.trend,
    'seasonal': result.seasonal,
    'residual': result.resid,
})

# reset the index for plotting
decomposed_data = decomposed_data.reset_index()

# plot the seasonal decomposition
fig_decompose = go.Figure()

fig_decompose.add_trace(go.Scatter(x=decomposed_data['posting_date'], y=decomposed_data['trend'],
                                   mode='lines', name='Trend'))
fig_decompose.add_trace(go.Scatter(x=decomposed_data['posting_date'], y=decomposed_data['seasonal'],
                                   mode='lines', name='Seasonal'))
fig_decompose.add_trace(go.Scatter(x=decomposed_data['posting_date'], y=decomposed_data['residual'],
                                   mode='lines', name='Residual'))

# customize the layout
fig_decompose.update_layout(title='Seasonal Decomposition of Time Series',
                            xaxis_title='Posting Date',
                            yaxis_title='Counts',
                            showlegend=True)

# show the plot
fig_decompose.show()
```
![Screenshot 2024-02-14 082113](https://github.com/mercycheeky/Craig-list-vehicles_Lux/assets/56400871/fa71c577-7f89-426e-8d84-3a98b4d711ec)

Explanation:

- In the plot displayed above, the trend and seasonality are not discernible; only the residual component is visible. This is primarily due to the limited time period over which the data was collected, making it challenging to capture and observe the underlying seasonal and trend patterns.

### Data source
- You can get data used in this project from Kaggle, here https://www.kaggle.com/datasets/mbaabuharun/craigslist-vehicles
# My Approach:
- First I handled the missing values in the dataset. I resolved this by filling in the missing values with the median and mode of numerical and categorical data on the respective columns.
- Then, I converted the data types of the columns in the dataset to the appropriate data types, I converted the posting_date column should be converted to a datetime data type.
- I then used the posting_date column to create a datetime index for the dataset, this allowed me to easily analyze the temporal patterns of the data.
- Once I had clean data I explored it using different visualizations and statistical analysis approached. I used this step to analyze temporal patterns, seasonal trends, and demand-supply dynamics by region and vehicle type.And finally with a good understanding of the data, I created the time-series charts.

  Pandas Solution explained: https://colab.research.google.com/drive/1zW9k7r_TMHhftURnNpsc2njpuJrYzlSI#scrollTo=7mkc1zpI0kcA
