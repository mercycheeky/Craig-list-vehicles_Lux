# Craig-list-vehicles_Lux   (Time Series Model)
## Task 
The project involves transforming the provided record-level dataset into  Time series model. The main objective of this model is to gain insights into the temporal patterns of vehicle listings, with a particular emphasis on conducting an inventory analysis
overtime, segmented by regions. For instance, the model should facilitate the creation of a time series chart that represents the number of vehicles available overtime, filtered by specific criteria such as region, vehicle type e.t.c.
This will aid in understanding regional demand-supply dynamics, seasonal trends and other relevant insights.

# Step 1
I will import the necessary packages, handle missing values,drop unnecessary columns and convert the "posting_date" to a datateime data type
```ruby
require 'redcarpet'
markdown = Redcarpet.new("Hello World!")
puts markdown.to_html
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

'''








- You can get data used in this project from Kaggle, here https://www.kaggle.com/datasets/mbaabuharun/craigslist-vehicles
# My Approach:
- First I handled the missing values in the dataset. I resolved this by filling in the missing values with the median and mode of numerical and categorical data on the respective columns.
- Then, I converted the data types of the columns in the dataset to the appropriate data types, I converted the posting_date column should be converted to a datetime data type.
- I then used the posting_date column to create a datetime index for the dataset, this allowed me to easily analyze the temporal patterns of the data.
- Once I had clean data I explored it using different visualizations and statistical analysis approached. I used this step to analyze temporal patterns, seasonal trends, and demand-supply dynamics by region and vehicle type.And finally with a good understanding of the data, I created the time-series charts.

  Pandas Solution explained: https://colab.research.google.com/drive/1zW9k7r_TMHhftURnNpsc2njpuJrYzlSI#scrollTo=7mkc1zpI0kcA
