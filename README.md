# Exploratory Data Analysis of Most Streamed Spotify Songs 2023

## Introduction

This project delves into the **"Most Streamed Spotify Songs 2023"** dataset to uncover the underlying trends, patterns, and factors that contribute to a song's streaming success.

Through a comprehensive Exploratory Data Analysis (EDA), this study aims to:
- **Identify Key Trends:** Examine which genres, artists, and release periods dominated the streaming charts in 2023.
- **Analyze Song Attributes:** Investigate how factors such as song duration, release date, BPM, and danceability influence the number of streams.
- **Visualize Insights:** Create compelling visualizations to illustrate the findings and make the data accessible to a broader audience.
- **Investigate Correlations:** Explore relationships between streams and other musical characteristics like tempo, energy, and playlist placements.

### EDA Structure

The Exploratory Data Analysis in this project follows the structure below:
1. Overview of the Dataset
2. Basic Descriptive Statistics
3. Top Performers
4. Temporal Trends
5. Genre and Music Characteristics
6. Platform Popularity
7. Advanced Analysis

## Codes
### Opening the File and Accessing the Library

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

df = pd.read_csv('spotify-2023.csv', encoding='ISO-8859-1')
df
```
### 1. Overview of Dataset

#### How many rows and columns does the dataset contain?

```python
# Assigning the two elements as rows and columns which represent the row and column in the matrix
[rows, columns] = df.shape

print("The number of rows in the dataset is", rows, "and the number of columns is", columns)
```
#### What are the data types of each column? Are there any missing values?

```python
# dtypes is used to retrieve the data types of each column 

data_types = df.dtypes
print ("The data types in each column is: \n",data_types)
```
#### Are there any missing values?
```python
# df.isnull is used to check the missing values or null in the data frame

missing_val = df.isnull().sum() #To check each column to identify which part of the data is missing
print("The missing values in each column:\n", missing_val)
```
### 2. Basic Descriptive Statistics
#### What are the mean, median, and standard deviation of the streams column?
```python
# using errors = 'cource' in pd.to_numeric is to avoid errors that might arise from nonnumeric values
# df['streams].mean , .median , .mode is used to get the mean, median and mode from the stream column

df['streams'] = pd.to_numeric(df['streams'], errors='coerce')

mean_streams = df['streams'].mean()   
median_streams = df['streams'].median()
std_streams = df['streams'].std()

print("Mean:", mean_streams)
print("Median:", median_streams)
print("Standard Deviation:", std_streams)
```
#### What is the distribution of released_year and artist_count? Are there any noticeable trends or outliers?
```python
#To avoid Errors
df['streams'] = pd.to_numeric(df['streams'], errors='coerce') 

# Calculate the counts for each released year
released_year_counts = df['released_year'].value_counts().sort_index()  # Count each year and sort by year

# Set up the figure for side-by-side plots
plt.figure(figsize=(14, 6))

# Plot line graph data
plt.subplot(1, 2, 1)  # 1 row, 2 columns, position 1
plt.bar(released_year_counts.index, released_year_counts.values, color="steelblue", width=0.8)  # Set width for thinner bars
plt.title("Distribution of Released Years (Bar Graph)")
plt.xlabel("Released Year")
plt.ylabel("Count")

# Plot Bar Plot of Artist Count
plt.subplot(1, 2, 2)  # 1 row, 2 columns, position 2
sns.histplot(df['artist_count'], bins=10, kde=False, color="steelblue", binwidth=0.5)  # binwidth makes bars thinner
plt.title("Distribution of Artist Count")
plt.xlabel("Artist Count")
plt.ylabel("Count")

# Show the plot and using .tight_layout to adjust the spacing between subplots and preventing it from overlapping
plt.tight_layout()
plt.show()

#Making a Scatter Plot to show the relationship between Released Year and Artist Count
#Scatter plot to help us the connection between the two variables

plt.figure(figsize=(8, 6))
plt.scatter(df['released_year'], df['artist_count'], alpha=0.6, color="purple")
plt.title("Scatter Plot of Released Year vs. Artist Count")
plt.xlabel("Released Year")
plt.ylabel("Artist Count")
plt.grid(True) #this adds grid to the background 
plt.show()
```
### 3. Top Performers
#### Which track has the highest number of streams? Display the top 5 most streamed tracks.
```python

#Using .head() to provide the Top 5 rows which contains most streamed tracks
op_tracks = df.sort_values(by='streams', ascending=False).head(5)

# Create the bar graph
# plt.barh creates a horizontal bar chart
plt.figure(figsize=(12, 6))
bars = plt.barh(top_tracks['track_name'], top_tracks['streams'], color=['#6a0dad', '#9932cc', '#ba55d3', '#da70d6', '#ee82ee'])  # colors as preference
plt.xlabel('Streams')
plt.title('Top 5 Most Streamed Tracks')
plt.gca().invert_yaxis()  # Invert y-axis to have the highest on top

# Add annotations
for bar in bars:
    plt.text(bar.get_width(), bar.get_y() + bar.get_height()/2, f'{int(bar.get_width()):,}',  
             va='center', ha='left', color='black', fontsize=10)

plt.show()
```
#### Who are the top 5 most frequent artists based on the number of tracks in the dataset?
```python

# using .value_counts to count how many times each artist appears in the column
artist_counts = df['artist(s)_name'].value_counts().head(5)

# filtering to only include rows with the top 5 artists
filtered_df = df[df['artist(s)_name'].isin(top5_artists)]

colors = ['#6a0dad', '#9932cc', '#ba55d3', '#da70d6', '#ee82ee']

# To create bar graph
plt.figure(figsize=(12, 6))
bars = plt.barh(top5_artists, artist_counts.values, color=colors)  # Use horizontal bars with custom colors

for bar in bars:
    plt.text(bar.get_width(), bar.get_y() + bar.get_height()/2, f'{int(bar.get_width()):,}', 
             va='center', ha='left', color='black', fontsize=10)

# Styling the plot
plt.title('Top 5 Artists by Number of Tracks')
plt.xlabel('Number of Tracks')
plt.ylabel('Artist(s) Name')
plt.gca().invert_yaxis()  # Invert y-axis to have the highest on top
plt.show()
```

### 4. Temporal
#### Analyze the trends in the number of tracks released over time. Plot the number of tracks released per year.
```python
# .value_counts to count the number of tracks per year and sort by year
tracks_per_year = df['released_year'].value_counts().sort_index()

# Creating a bar plot for the data of the tracks released each year
plt.figure(figsize=(18, 7))  
plt.bar(tracks_per_year.index, tracks_per_year.values, color='steelblue')

plt.title('Number of Tracks Released Per Year')
plt.xlabel('Year')
plt.ylabel('Number of Tracks')

# setting the x -axis by using .xticks
plt.xticks(tracks_per_year.index, rotation=45, ha='right', fontsize=8)  # Rotate labels and adjust font size

# To remove all grid lines for clarity
plt.grid(False)

plt.show()
```
#### Does the number of tracks released per month follow any noticeable patterns? Which month sees the most releases?
```python
# Check and plot for the number of releases per month
track_month = df['released_month'].value_counts().reset_index()
track_month.columns = ['released_month', 'count']  # Rename columns

# Month mapping
months = {1: "January", 2: "February", 3: "March", 4: "April", 5: "May", 6: "June",
          7: "July", 8: "August", 9: "September", 10: "October", 11: "November", 12: "December"}


track_month['name_month'] = track_month['released_month'].map(months) #Replacing month with their names
track_month.sort_values('released_month', inplace=True)  # Sort by month numbers for proper chronological order

# Selecting the two months with the highest release counts
top_months = track_month.nlargest(2, 'count')['name_month'].values

# Highlighting the two months with the highest releases
colors = ['#ff9999' if month in top_months else '#cccccc' for month in track_month['name_month']]

# creating the plot and adding color for clarity
plt.figure(figsize=(10, 7))
plt.bar(track_month['name_month'], track_month['count'], color=colors)
plt.title("Number of Releases Per Month")
plt.xlabel("Months")
plt.ylabel("Number of Tracks")
plt.xticks(rotation=45)
plt.show()
```
### 5. Genre and Music Characteristics
#### Examine the correlation between streams and musical attributes like bpm, danceability_%, and energy_%. Which attributes seem to influence streams the most?
```python
# Load the data set and avoid error 
df = pd.read_csv('spotify-2023.csv', encoding='ISO-8859-1') 


# Convert to numeric if needed and handle non-numeric entries
columns_to_convert = ['streams', 'acousticness_%', 'energy_%', 'valence_%', 'instrumentalness_%', 'liveness_%', 'danceability_%', 'speechiness_%']
for column in columns_to_convert:
    df[column] = pd.to_numeric(df[column], errors='coerce')

# Using .dropna to remove the missing values 
df.dropna(subset=columns_to_convert, inplace=True)

# Calculate the correlation matrix
correlation_matrix = df[columns_to_convert].corr()

# Print the values
print("Correlation between streams and the given musical attributes in the data:")
print(correlation_matrix['streams'])  # Only displaying correlation with streams

# Create a heatmap using sns.heatmap
plt.figure(figsize=(10, 8))
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', fmt=".2f") 
plt.title('Heatmap of Correlation Matrix')
plt.show()
```
#### Is there a correlation between danceability_% and energy_%? How about valence_% and acousticness_%?
```python
# Calculate the correlation between danceability_% and energy_%
correl_DE = df[['danceability_%', 'energy_%']].corr()

# Calculate the correlation between valence_% and acousticness_%
correl_VA = df[['valence_%', 'acousticness_%']].corr()

# Print the results
print("The correlation between danceability_% and energy_% is:")
print(correl_DE)
print()
print("The correlation between valence_% and acousticness_% is:")
print(correl_VA)
```
### 6. Platform Popularity
#### How do the numbers of tracks in spotify_playlists, spotify_charts, and apple_playlists compare? Which platform seems to favor the most popular tracks?
```python
# Load the dataset
df = pd.read_csv('spotify-2023.csv', encoding='ISO-8859-1')

# Convert playlist columns to numeric
playlist_columns = ['in_spotify_playlists', 'in_deezer_playlists', 'in_apple_playlists']
df[playlist_columns] = df[playlist_columns].apply(pd.to_numeric, errors='coerce')

# Getting the total number of tracks in each playlist category
total_tracks = df[playlist_columns].sum()

# Print the results
print("Total number of tracks in each playlist category:")
print(total_tracks)

# Creating a bar graph for plotting the results
colors = ["#4B9CD3", "#FFC300", "#FF5733"]  # Custom colors for each category
plt.figure(figsize=(8, 5))

# Using bar_width to lessen the thickness
bar_width = 0.4  

plt.bar(total_tracks.index, total_tracks.values, color=colors, width=bar_width)

plt.title("Total Number of Tracks in Each Playlist Category")
plt.xlabel("Playlist Category")
plt.ylabel("Total Number of Tracks")
plt.xticks(rotation=0)  # Ensure x-ticks are horizontal for readability
plt.show()

# Based on the top 5 tracks and using .loc to select specific rows
df.loc[[55, 179, 86, 620, 41], ['track_name', 'artist(s)_name', 'in_spotify_playlists', 'in_deezer_playlists', 'in_apple_playlists']].head()
```
### 7. Advance Analysis
#### Based on the streams data, can you identify any patterns among tracks with the same key or mode (Major vs. Minor)?
```python
# load the file
df = pd.read_csv('spotify-2023.csv', encoding='ISO-8859-1')

# Convert 'streams' to numeric
df['streams'] = pd.to_numeric(df['streams'], errors='coerce')

# To make the column more readable
if df['mode'].dtype == 'int64':
    mode_mapping = {0: 'Minor', 1: 'Major'}
    df['mode'] = df['mode'].map(mode_mapping)

# Using .dropna to drop missing values 
df.dropna(subset=['key', 'mode', 'streams'], inplace=True)

# Group by 'key' and 'mode' and calculate the average streams
grouped = df.groupby(['key', 'mode'])['streams'].mean().reset_index()

# Sort the grouped data by streams in descending order
sorted_grouped = grouped.sort_values(by='streams', ascending=False)

# Plotting the results using seaborn.barplot by putting key and streams in the xy axis.
plt.figure(figsize=(14, 7))
sns.barplot(x='key', y='streams', hue='mode', data=sorted_grouped, palette='coolwarm')
plt.title('Average Streams by Key and Mode')
plt.xlabel('Musical Key')
plt.ylabel('Average Streams')
plt.legend(title='Mode')
plt.xticks(rotation=90)  # Rotate x-axis labels for clarity if there are many keys
plt.show()
```
#### Do certain genres or artists consistently appear in more playlists or charts? Perform an analysis to compare the most frequently appearing artists in playlists or charts
```python
# Create a column
playlist_columns = ['in_spotify_playlists', 'in_deezer_playlists', 'in_apple_playlists']

# Convert columns to numeric
# .fillna replace missing value to 0
for column in playlist_columns:
    df[column] = pd.to_numeric(df[column], errors='coerce').fillna(0)

# To sum up the values of the column
artist_playlist_appearance = df.groupby('artist(s)_name')[playlist_columns].sum()

# Sum of all playlist columns to get total appearances per artist across all platforms
artist_playlist_appearance['total_appearances'] = artist_playlist_appearance.sum(axis=1)

# Sort artists and using .head to indicate it only the top 5
sorted_artists = artist_playlist_appearance.sort_values(by='total_appearances', ascending=False).head(5)  # Only the top 5

# Plotting the results
plt.figure(figsize=(10, 6))
# using sns.barplot to create horizontal bar plot
sns.barplot(x=sorted_artists['total_appearances'], y=sorted_artists.index, color='steelblue')
plt.title('Top 5 Artists by Total Playlist Appearances')
plt.xlabel('Total Appearances Across All Playlists')
plt.ylabel('Artist(s) Name')
plt.show()
```

## Results
### 1. Overview of Data Set

- How many rows and columns does the dataset contain?

![Overview of Data Set](https://github.com/user-attachments/assets/5bfa01ac-6270-411c-af24-35b6aed4244e)

- What are the data types of each column? Are there any missing values?

![Data Types and Missing Values](https://github.com/user-attachments/assets/0e24c441-2be0-4896-b37b-311530bf4986)

- Are there any missing values?

![Missing Values](https://github.com/user-attachments/assets/d5ab85ba-ff28-446e-9ad0-0938c54c09ab)

### 2. Basic Descriptive Statistics
- What are the mean, median, and standard deviation of the streams column?

  ![image](https://github.com/user-attachments/assets/7ff866f6-6b5d-43bc-a787-de2a5d627b07)
- What is the distribution of released_year and artist_count? Are there any noticeable trends or outliers?

  ![image](https://github.com/user-attachments/assets/bf260a52-848d-444f-b3d8-b0ecbd4359d0)
### 3. Top Performers

- Which track has the highest number of streams? Display the top 5 most streamed tracks.
  ![image](https://github.com/user-attachments/assets/ab46b878-14f9-45be-aa06-079cae97369c)

- Who are the top 5 most frequent artists based on the number of tracks in the dataset?
  ![image](https://github.com/user-attachments/assets/9a743d91-fe85-4c4b-8ef4-15cfcdfc17a2)


### 4. Temporal
- Analyze the trends in the number of tracks released over time. Plot the number of tracks released per year.
  ![image](https://github.com/user-attachments/assets/3c455df6-ba29-42f0-8d03-a2cd2175425d)

- Does the number of tracks released per month follow any noticeable patterns? Which month sees the most releases?
  ![image](https://github.com/user-attachments/assets/71c72cb0-530d-4c10-b41c-e59b5ee7047d)

### 5. Genre and Music Characteristics
- Examine the correlation between streams and musical attributes like bpm, danceability_%, and energy_%. Which attributes seem to influence streams the most?
  
  ![image](https://github.com/user-attachments/assets/46ebdb2d-0270-4a5c-b9d5-bbc4cfdb3413)


  ![image](https://github.com/user-attachments/assets/f8fa6d95-4efe-4726-b2f1-4ba83b8fa74b)

- Is there a correlation between danceability_% and energy_%? How about valence_% and acousticness_%?

  ![image](https://github.com/user-attachments/assets/e6793106-103c-4cb2-972f-e2a42f7656e1)

### 6. Platform Popularity

- How do the numbers of tracks in spotify_playlists, spotify_charts, and apple_playlists compare? Which platform seems to favor the most popular tracks?

  ![image](https://github.com/user-attachments/assets/0cb1a51a-23ea-42e3-937f-2221deac609c)

### 7. Advance Analysis

- Based on the streams data, can you identify any patterns among tracks with the same key or mode (Major vs. Minor)?

  ![image](https://github.com/user-attachments/assets/fdcdb54b-b984-48d2-b95d-f3bfd0ca145f)

- Do certain genres or artists consistently appear in more playlists or charts? Perform an analysis to compare the most frequently appearing artists in playlists or charts.

  ![image](https://github.com/user-attachments/assets/3be8adb1-f598-42f4-bff1-64111dc29bb1)


## Key Insights 
 - One key insight I gained from this exploratory data analysis is the importance of using the **ISO-8859-1** encoding when loading CSV files. I found that without specifying this encoding, errors may occur, especially when handling special characters. By using ISO-8859-1, I ensured that any special characters present in the file were accurately interpreted and loaded into the DataFrame, thereby preventing potential issues during the data import process.

 - Similarity Between Python and MATLAB Syntax, I also observed that the syntax used in Python bears similarities to that of MATLAB as i gain knowledge in taking MATLAB Onramp. For instance, the expression [rows, columns] = df.shape in Python closely mirrors the indexing structure used in MATLAB, which made the transition between these environments more intuitive.

 -  Error Handling in Data Processing, I faced challenges in retrieving data due to recurring errors, often caused by non-numeric values. To address this, I learned the importance of using the parameter **errors='coerce'** when converting data types. This parameter prevents runtime errors by coercing invalid entries into a manageable format, typically converting them to NaN, thereby allowing the data processing to proceed smoothly.

 -  One important lesson I learned was that real-world data is often messy. Dealing with missing values and converting data types showed me that data isn’t always clean or ready for analysis. I had to spend time preparing it before doing anything meaningful, which gave me valuable insight into what actual data analysis involves. This experience taught me that data cleaning is just as important as the analysis itself.
-   **Reflection**: Being a programmer is challenging, it takes a lot of time to learn, and there's plenty of trial, error, and frustration just to understand what's wrong with the code. Even as a student learning the basics, I find it tough to tackle simple problems. However, working on this project has given me a valuable opportunity to deepen my programming knowledge and skills. 

## References
- ECE 2112 Modules and Cheat Sheet
- ELGIRIYEWITHANA, NIDULA. “Most Streamed Spotify Songs 2023.” Www.kaggle.com, www.kaggle.com/datasets/nelgiriyewithana/top-spotify-songs-2023.
- Bro Code. “Learn Python in 1 Hour! 🐍 (2024).” YouTube, 18 Aug. 2024, www.youtube.com/watch?v=8KCuHHeC_M0.
- ChatGpt 












  












