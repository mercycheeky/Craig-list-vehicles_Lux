# Craig-list-vehicles_Lux
- You can get data used in this project from Kaggle, here https://www.kaggle.com/datasets/mbaabuharun/craigslist-vehicles
# My Approach:
-  the missing values in the dataset. I resolved this by filling in the missing values with the median and mode of numerical and categorical data on the respective columns.
- Then, I converted the data types of the columns in the dataset to the appropriate data types, I converted the posting_date column should be converted to a datetime data type.
- I then used the posting_date column to create a datetime index for the dataset, this allowed me to easily analyze the temporal patterns of the data.
- Once I had clean data I explored it using different visualizations and statistical analysis approached. I used this step to analyze temporal patterns, seasonal trends, and demand-supply dynamics by region and vehicle type.And finally with a good understanding of the data, I created the time-series charts.

  Pandas Solution explained: https://colab.research.google.com/drive/1zW9k7r_TMHhftURnNpsc2njpuJrYzlSI#scrollTo=7mkc1zpI0kcA
