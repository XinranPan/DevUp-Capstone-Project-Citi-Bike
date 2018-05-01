README
DevUp Data Science Program | Citi Bike Capstone Project
Packages Used:
-	Geopy
-	Pandas
-	Numpy
-	Seaborn
-	Matplotlib
-	Plotly
-	Folium
-	Sklearn 
-	XGBoost

1. Understand and Preprocessing Ridership Data
Part. 1
-	Note: All histograms and boxplots in Part. 1 use matplotlib.pyplot
-	Download the monthly Citi Bike csv datasets from https://s3.amazonaws.com/tripdata/index.html. Aggregate by reading and concatenating into a pandas dataframe.
-	Relabel all headers with lower cases for easier manipulation 
-	Check the initial dataframe, including heads, tails, and shape. There should contain 16364657 rows and 15 columns.
-	Add a new feature, “trip duration (min)”, by converting the original feature from seconds to minutes to more easily understand the distribution of data for cleanup.
-	Change the string values inside “start station name” and “stop station name” for easier manipulation
-	Create a new dataset which excludes the start/end station IDs and trip duration in seconds, as IDs and names are synonymous. Work with this new dataframe.
-	Because 16 million rows of data is too large of a dataset to work with for efficiency and memory constraints, randomly sample a subset of 5 million rows to continue the preprocessing process
-	To get a better understanding of this smaller dataset, describe the data and obtain descriptive statistics including mean, min, max, count.
-	The results show a wide range of trip durations ranging from 1 minute to 113 days. Plot the distribution of trip durations to better understand how to clean the dataset
-	The initial graph, a boxplot, only shows that many outliers exist in the dataset. This is not very useful so next, plot a histogram. Start by plotting points equal to or under 300 minutes to get a rough idea of the shape.
-	The histogram shows that most datapoints lay within 1-50 minutes, with some outliers from 50-100 minutes and the rest beyond. Check the other end by plotting points greater than 300 minutes.
-	Just as suspected, the points greater than 300 are all outliers. Even though under 300 minutes roughly only account for 68% or one standard deviation of the data (mean=16.35 minutes, standard deviation=290 minutes, and assuming a normal distribution), the other 32% count for a wider range of outliers. This is supported by the histogram whose occurrences are much lower than the data under 300 minutes.
-	Let's also consider datapoints from 45-300 minutes, (45 being the max rental time for a subscriber), by plotting another histogram.
-	The graph confirms the outliers, though greater in number of occurrences from 45. To simplify the cleaning process, remove from the dataset all trip durations longer than 75 minutes.
Assumption: Riders will travel for no more than 75 minutes. Realistically, trips do not longer, as each Citi Biki ride limit is either 30 or 45 min, and most riders would not wish to be heavily penalized for not re-docking their bikes. Include trips 45-75 min to account for riders who forget to dock, do not read the bike sharing policy, etc.
-	Plot the new histogram. Although the shape is clearly right skewed, most trip duration outliers have been removed. The new descriptive statistics are now in range with a mean time of 13 minutes (though a median would be more accurate in this scenario as the shape is so right skewed).
-	Next, take a look at the station names. Upon some cross checking on Citi Bike's list of docking stations, some of the stations names in the dataset are not docking stations but workshops or depot centers. Other station names have labels "don't use". Remove all rows containing these anomalies.
-	Drop all rows that contain the same start and end station.
Assumption: All trips must start at a station A and end at a station B where A and B are unique stations. No trips start and end at the same stations.
-	Check and drop duplicate rows. 
-	Check and drop rows containing null values.
-	Check and drop rows containing an unknow gender, “0”.
-	Check rider ages by plotting another histogram, boxplot, and show descriptive stats. Birth years range from the 1880’s to 2000. It’s improbable that many people over 100 year old rent bikes. This is verified by the boxplot which shows outliers ranging from before 1880 to the early 1940’s.
-	Plot points for ages 70+ or below 1948 to confirm outliers.
-	Drop rows containing birth years less than 1948.
Assumption: Riders are not over 70+ years old.
-	Now that the dataset is clean, randomly sample 1 million datapoints to work with for efficiency. Check the shape of the dataset and plot the trip duration distribution.

Part 2.
-	Create a copy of the new cleaned dataset to further manipulate. This way, if something goes wrong, we can always have a backup dataset.
-	For the later purpose of merging with a weather dataset, create from “start time” and using Pandas, two new features, “start date” and “time,” comprising of datetime types instead of objects. Now, we can merge with weather data based on the common ID, “start time.” More on this later.
-	Calculate the distance between the start lat/log and end lat/log coordinates using the Vincenty formula found in Geopy.
-	This distance is the shortest distance between two points and does not accurately reflect actual travel paths, including bike paths, detours, etc, which most often will cause the true trip distance to be longer than this calculated distance. To account for this, multiple the distance by a factor of 1.25.
Assumption: The distance between the start and end coordinate points is inaccurate as it does not account for actual routes taken. Assume that by lengthening the distance by 25%, this will help calculate more accurate trip distances.
-	Drop trip distances greater than 40 miles (in up to 75 minutes) as it's unlikely a rider would travel that far that fast, at a rate of 32 mph on busy city streets
-	Add a new feature, “trip rate (mph),” by calculating trip distance/trip duration
-	Drop bike rates faster than 30 mph. 
Assumption: All riders bike no faster than 30 mph.
-	Add a new feature, “age,” by subtracting 2018 from birth years

2. Ridership Data Visualization 

Note: Questions appear in the order they are coded in in the Jupyter notebook
Question 1. Top 5 stations with the most starts (showing # of starts)
-	Use Folium to create a map of the top five start stations indicated by markers.
-	First, obtain the names of the top five most visited start stations and their counts as a Panda Series and the corresponding dataset (containing five rows of data).
-	Create a corresponding list of start station coordinates and also a list start station names.
-	Create a dictionary with keys=start station names and values=station coordinates.
-	Create a function which takes into account the above steps and returns a street map of NYC and Citi Bike markers indicating the location of the top five starts.

-	Use Seaborn to create a bar chart of the number of counts for the top five start stations
-	Create start station name ylabels which can be wrapped for aesthetics
-	Include the total counts for the top five start stations


Question 3.
-	Use Folium to create a map of the top five trips indicated by trip routes between station markers.
-	Create a new feature “start_end” by concatenating the string elements of start and end station names with a hyphen in between Ie. “e 7 st & avenue a-cooper square & e 7 st”
-	Count the top five trips
-	Create a dataset containing only rows with the top five trips
-	Obtain start and end station coordinates, change coordinates into tuples, and append coordinate pairs to a list. This is for calculating the distance between start and end stations
-	Create another list with all single coordinates, not as a start/end pair and add matching station names and coordinates into a dictionary. This is for labeling station markers
-	Create a function which takes in both a list start/end coordinate pairs and a list of single coordinates and returns a map with the top five trips and their start/end stations


Question 5.
-	Use a Pandas dataframe to count the number of occurrences for each bike ID, meaning the number of times the bike started or stopped at a station. Take the largest count as the busiest bike in NYC.
-	Use the number of occurrences to locate this bike’s corresponding ID
-	Calculate the total number of minutes used by summing all “trip duration (min)” values for this bike
-	Obtain all coordinates corresponding to the start and stop stations the busiest bike visited
-	Create a function which takes in coordinate points and returns a heatmap of all visited start and stop station in 2017
-	Create an additional bubble chart (interactive in the notebook only, unfortunately) to look at distribution of data by gender and age

Question 4. Average Trip Distance by Gender and Age, Trip Rate by Gender and Age
-	Create a new subset of data to answer questions involving gender.
-	Create a new feature, “age groups”, and assign every row an age group corresponding to either under 20, 20-40, 40-60, or 60+
-	Create datasets for male and female only data
-	Use Seaborn to create a violinplot to show distribution of trip rates by age and gender
-	Use Seaborn to create a violinplot to show distribution of trip distances by age and gender

Question 2. Trip duration by user type
-	Use Seaborn to create a facetgrid displaying the distribution of trip duration broken down into male/female and subscriber/customer

New Kiosk Feature: Travel Time Prediction
Model 1: Linear Regression
-	Model a simple univariate linear model with the independent variable as trip distances and the prediction as trip duration in minutes.
-	Split into a 66:33 train test set with a start seed of 7.
-	Fit and train the model.
-	Predict the trip duration and graph the regression line and test set datapoints using Pyplot.
-	As the scatter plot shows, the linear model does a poor job of predicting time travel as trip distance does not necessarily correlate with time.

-	Let's bring in an NYC weather dataset and then work on a different model.


Clean and Merge Weather Data
-	Obtained from https://www.ncdc.noaa.gov/cdo-web/datasets
-	Clean the data in Excel as preprocessing is fairly simple. No need to remove rows as there are exactly 365 datapoints, one for year day of 2017. Drop features Name, Latitude, Longitude, Elevation and Station.
-	Merge this weather data with the Citi Bike data with a simple left join with start date as the ID using Pandas.
-	Create a couple of simple graphs using pyplot to ensure the weather data makes sense.
-	Create a heatmap to analyze feature correlation. Remove “bike id” and “birth year”

Model 2: XGBoost
-	More efficient, robust and include better parameters for tuning the model
-	Prepare the training, validation and testing by converting non integer elements in the dataframe to integers using LabelEncoder. Ensure all dataset elements are either int or floats before modeling.
-	60:20:20 train, validate, test sets
-	Use log(y) instead of y for better RSME error prediction
-	Two validation scatter plot for confirmation
-	Y_pred vs y_actual scatter plot shows a good model for short trip times
-	Built function to output start, destination, trip distance, and predicted trip duration





