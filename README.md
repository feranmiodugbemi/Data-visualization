# Documentation on Spotify dataset and visualization

### 1. Dataset Overview
**Dataset Name:** Most Streamed Spotify Songs 2024  
**Source:** Kaggle  
**Key Features:**
- **Track**: The name of the song.
- **Album Name**: The album the track belongs to.
- **Artist**: The artist of the track.
- **Release Date**: The date the track was released.
- **ISRC**: International Standard Recording Code.
- **All Time Rank**: Track's ranking in all-time lists.
- **Track Score**: A score indicating the track's performance or popularity.
- **Spotify Streams**: Number of streams on Spotify.
- **Spotify Playlist Count**: Count of playlists featuring the track on Spotify.
- **Spotify Playlist Reach**: Reach of playlists featuring the track on Spotify.
- **Spotify Popularity**: Popularity score on Spotify.
- **YouTube Views**: Number of views on YouTube.
- **YouTube Likes**: Number of likes on YouTube.
- **TikTok Posts**: Number of posts on TikTok featuring the track.
- **TikTok Likes**: Number of likes on TikTok for the track.
- **TikTok Views**: Number of views on TikTok for the track.
- **YouTube Playlist Reach**: Reach of playlists featuring the track on YouTube.
- **Apple Music Playlist Count**: Count of playlists featuring the track on Apple Music.
- **AirPlay Spins**: Number of spins on AirPlay.
- **SiriusXM Spins**: Number of spins on SiriusXM.
- **Deezer Playlist Count**: Count of playlists featuring the track on Deezer.
- **Deezer Playlist Reach**: Reach of playlists featuring the track on Deezer.
- **Amazon Playlist Count**: Count of playlists featuring the track on Amazon.
- **Pandora Streams**: Number of streams on Pandora.
- **Pandora Track Stations**: Number of stations featuring the track on Pandora.
- **Soundcloud Streams**: Number of streams on Soundcloud.
- **Shazam Counts**: Number of Shazam queries for the track.
- **TIDAL Popularity**: Popularity score on TIDAL.
- **Explicit Track**: Indicator if the track is explicit.

### 2. Data Cleaning Process
The cleaning process includes:
- **Date Conversion:** Convert 'Release Date' to a datetime format and create a 'Year' column.
- **Remove Columns:** Drop columns with all empty values (e.g., TIDAL Popularity).
- **Data Type Conversion:** Convert columns to appropriate numeric types.
- **Handle Missing Values:** Replace null values with 0 for numeric columns.
- **Remove Formatting:** Remove commas from numeric strings and convert to integers or floats.


### 3. Visualization Details

- **Top 20 Most Frequent Artists**

  - **Type:** Horizontal Bar Plot
  - **Purpose:** Identify the most frequently appearing artists in the dataset, indicating their prominence or popularity.
  - **Code:**
    ```python
    value_counts = df['Artist'].value_counts()
    plt.figure(figsize=(10, 6))
    value_counts.head(20).plot(kind='barh')
    plt.xlabel('Number of appearances')
    plt.ylabel('Artist')
    plt.title('Top 20 Most Frequent Artists')
    plt.gca().invert_yaxis()
    plt.grid(axis='x')
    plt.show()
    ```

- **Top 20 Artists Based on Aggregated Performance Scores**

  - **Type:** Horizontal Bar Plot
  - **Purpose:** Rank artists based on a combined performance score, highlighting those with the highest overall engagement across platforms.
  - **Code:**
    ```python
    artist_agg = df.groupby('Artist').agg({
        'Spotify Streams': 'sum',
        'YouTube Views': 'sum',
        'TikTok Views': 'sum',
        'Spotify Playlist Reach': 'sum',
        'YouTube Playlist Reach': 'sum',
        'Apple Music Playlist Count': 'sum',
        'Shazam Counts': 'sum'
    })
    artist_agg['Score'] = (
        artist_agg['Spotify Streams'] * 0.3 +
        artist_agg['YouTube Views'] * 0.3 +
        artist_agg['TikTok Views'] * 0.2 +
        artist_agg['Spotify Playlist Reach'] * 0.1 +
        artist_agg['YouTube Playlist Reach'] * 0.05 +
        artist_agg['Apple Music Playlist Count'] * 0.05 +
        artist_agg['Shazam Counts'] * 0.05
    )
    top_artists = artist_agg.sort_values(by='Score', ascending=False).head(20)
    plt.figure(figsize=(12, 8))
    top_artists['Score'].plot(kind='barh')
    plt.xlabel('Score')
    plt.ylabel('Artist')
    plt.title('Top 20 Artists Based on Combined Score')
    plt.gca().invert_yaxis()
    plt.grid(axis='x')
    plt.show()
    ```

- **Cross-Platform Correlation Visualization**

  - **Type:** Heatmap
  - **Purpose:** Visualize correlations between metrics from different platforms and understand how engagement on one platform relates to others.
  - **Code:**
    ```python
    correlation_data = df[['Spotify Streams', 'Spotify Playlist Count', 'Spotify Playlist Reach',
                           'YouTube Views', 'TikTok Views', 'YouTube Playlist Reach', 
                           'Apple Music Playlist Count', 'Deezer Playlist Reach', 
                           'Pandora Streams', 'Soundcloud Streams', 'Track Score', 
                           'Spotify Popularity']]
    correlation_matrix = correlation_data.corr()
    plt.figure(figsize=(8, 6))
    sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', fmt='.2f', linewidths=.5, vmin=-1, vmax=1)
    plt.title('Correlation Between Various Platform Metrics')
    plt.show()
    ```

- **Trends in Explicit Content Over Time**

  - **Type:** Line Plot
  - **Purpose:** Show how the number of explicit tracks has changed over the years.
  - **Code:**
    ```python
    explicit_tracks = df[df['Explicit Track'] == 1]
    explicit_tracks_per_year = explicit_tracks.groupby('Year')['Track'].count()
    plt.figure(figsize=(15, 10))
    explicit_tracks_per_year.plot(kind='line', marker='o')
    plt.title('Number of Explicit Tracks per Year')
    plt.xlabel('Year')
    plt.ylabel('Number of Tracks')
    plt.grid(True)
    plt.show()
    ```

- **Change in Track Score Throughout the Years**

  - **Type:** Line Plot
  - **Purpose:** Examine how the average track score has varied by year, providing insights into trends in track quality or success.
  - **Code:**
    ```python
    avg_track_score_per_year = df.groupby('Year')['Track Score'].mean()
    plt.figure(figsize=(15, 10))
    avg_track_score_per_year.plot(kind='line', marker='o')
    plt.title('Change in Track Score Throughout the Years')
    plt.xlabel('Year')
    plt.ylabel('Average Track Score')
    plt.grid(True)
    plt.show()
    ```

- **Popularity Reach Across Top Platforms**

  - **Type:** 3D Scatter Plot
  - **Purpose:** Visualize how tracks perform across Spotify, YouTube, and TikTok simultaneously and identify patterns in performance.
  - **Code:**
    ```python
    plot_data = df[['Spotify Streams', 'YouTube Views', 'TikTok Views', 'Track Score']].dropna()
    x = plot_data['Spotify Streams']
    y = plot_data['YouTube Views']
    z = plot_data['TikTok Views']
    color = plot_data['Track Score']
    
    fig = plt.figure(figsize=(14, 10))
    ax = fig.add_subplot(111, projection='3d')
    sc = ax.scatter(x, y, z, c=color, cmap='viridis', s=50, alpha=0.7, edgecolors='w')
    ax.set_xlabel('Spotify Streams')
    ax.set_ylabel('YouTube Views')
    ax.set_zlabel('TikTok Views')
    ax.set_title('3D Scatter Plot of Track Performance Across Platforms')
    cbar = plt.colorbar(sc, ax=ax, shrink=0.5, aspect=5)
    cbar.set_label('Track Score')
    plt.show()
    ```

- **Impact of Release Date on Track Popularity**

  - **Type:** Line Plot
  - **Purpose:** Assess how release dates influence track popularity.
  - **Code:**
    ```python
    df_yearly = df.groupby('Year')['Spotify Popularity'].mean().reset_index()
    plt.figure(figsize=(12, 6))
    sns.lineplot(data=df_yearly, x='Year', y='Spotify Popularity', marker='o')
    plt.title('Average Track Popularity by Release Year')
    plt.xlabel('Release Year')
    plt.ylabel('Average Spotify Popularity')
    plt.grid(True)
    plt.show()
    ```

- **Market Share of Music Discovery**

  - **Type:** Pie Chart
  - **Purpose:** Show the distribution of tracks across different music discovery platforms, highlighting the market share of each.
  - **Code:**
    ```python
    platform_counts = {
        'Spotify': df['Spotify Playlist Count'].sum(),
        'YouTube': df['YouTube Views'].sum(),
        'TikTok': df['TikTok Posts'].sum(),
        'Apple Music': df['Apple Music Playlist Count'].sum(),
        'Deezer': df['Deezer Playlist Count'].sum(),
        'Amazon': df['Amazon Playlist Count'].sum(),
        'Pandora': df['Pandora Streams'].sum(),
        'Soundcloud': df['Soundcloud Streams'].sum()
    }
    
    plt.figure(figsize=(10, 10))
    plt.pie(platform_counts.values(), labels=platform_counts.keys(), autopct='%1.1f%%', colors=plt.cm.Paired(range(len(platform_counts))))
    plt.title('Distribution of Tracks Across Platforms')
    plt.show()
    ```

- **Artist Engagement Across Platforms**

  - **Type:** Parallel Coordinates Plot
  - **Purpose:** Compare average engagement metrics for each artist across multiple platforms.
  - **Code:**
    ```python
    from pandas.plotting import parallel_coordinates
    
    plot_data = df[['Artist', 'Spotify Streams', 'YouTube Views', 'TikTok Views', 'Apple Music Playlist Count']].dropna()
    artist_engagement = plot_data.groupby('Artist').mean().reset_index()
    
    plt.figure(figsize=(14, 8))
    parallel_coordinates(artist_engagement, 'Artist', colormap=plt.get_cmap('tab10'))
    plt.title('Artist Engagement Across Platforms')
    plt.xlabel('Platforms')
    plt.ylabel('Average Values')
    plt.legend(title='Artist', bbox_to_anchor=(1.05, 1), loc='upper left')
    plt.grid(True)
    plt.show()
    ```

- **Total Track Engagement Over Time**

  - **Type:** Line Plot
  - **Purpose:** Show total track engagement (sum of streams and views) over time.
  - **Code:**
    ```python
    df['Total Engagement'] = df['Spotify Streams'] + df['YouTube Views'] + df['TikTok Views']
    df_yearly_engagement = df.groupby('Year')['Total Engagement'].sum().reset_index()
    
    plt.figure(figsize=(12, 6))
    sns.lineplot(data=df_yearly_engagement, x='Year', y='Total Engagement', marker='o')
    plt.title('Total Track Engagement Over Time')
    plt.xlabel('Year')
    plt.ylabel('Total Engagement')
    plt.grid(True)
    plt.show()
    ```
    
### 4. Documentation of Visualizations
**Top 20 Most Frequent Artists:**
- **Purpose:** Identify the most frequently appearing artists in the dataset.
- **Insights:** Artists with the highest frequency of appearance, indicating their prominence or popularity.
- **Narrative:** This visualization shows which artists dominate the dataset in terms of appearances, providing a snapshot of current popular or recurrent artists in the data.

**Top 20 Artists Based on Aggregated Performance Scores:**
- **Purpose:** Rank artists based on a weighted combination of their performance metrics (e.g., Spotify streams, YouTube views).
- **Insights:** Artists with the highest combined performance scores, offering a view of those with overall high engagement across platforms.
- **Narrative:** By aggregating performance metrics, this visualization identifies the top-performing artists, helping to recognize those with broad cross-platform success.

**Cross-Platform Correlation Visualization:**
- **Purpose:** Understand the relationships between different platform metrics.
- **Insights:** Correlations between metrics like Spotify streams and YouTube views, indicating how engagement on one platform might influence others.
- **Narrative:** This heatmap reveals how various platform metrics are interrelated, providing insights into how performance on one platform correlates with others.

**Trends in Explicit Content Over Time:**
- **Purpose:** Analyze how the number of explicit tracks has changed over time.
- **Insights:** Trends in explicit content, showing how its prevalence has evolved.
- **Narrative:** The line plot tracks the number of explicit tracks per year, illustrating shifts in explicit content trends in the music industry.

**Change in Track Score Throughout the Years:**
- **Purpose:** Examine how the average track score has varied by year.
- **Insights:** Trends in track quality or popularity over time.
- **Narrative:** This visualization depicts how the average track score has evolved, offering insights into the changing standards or success of tracks over the years.

**Popularity Reach Across Top Platforms:**
- **Purpose:** Compare track performance across multiple platforms in a 3D space.
- **Insights:** Visualization of how tracks perform on Spotify, YouTube, and TikTok simultaneously.
- **Narrative:** The 3D scatter plot shows how track popularity on one platform relates to performance on others, revealing patterns of cross-platform success.

**Impact of Release Date on Track Popularity:**
- **Purpose:** Assess how release dates influence track popularity.
- **Insights:** Trends in how average popularity scores change over time.
- **Narrative:** This line plot shows how the average Spotify popularity of tracks has varied by release year, highlighting any potential seasonal or temporal trends.

**Market Share of Music Discovery:**
- **Purpose:** Display the distribution of music discovery across various platforms.
- **Insights:** The relative market share of different platforms based on track counts or engagement metrics.
- **Narrative:** The pie chart provides a visual representation of how different platforms contribute to the overall music discovery landscape.

### 5. Limitations and Recommendations
**Limitations:**
- **Data Completeness:** Some metrics may be missing or incomplete.
- **Biases:** The dataset might be skewed towards certain types of tracks or artists.
- **External Factors:** Factors affecting popularity (e.g., marketing, events) might not be accounted for.

**Recommendations:**
- **Further Analysis:** Consider including additional metrics or external data for a more comprehensive view.
- **Data Enrichment:** Enhance the dataset with additional information from other sources to fill gaps.

### 6. Instructions for Running the Code
To run the code in Jupyter Notebook:
1. **Install Dependencies:** Ensure you have the required libraries installed (e.g., ipykernel and jupyter). These libraries will allow you to be able to use jupyter lab
   ```bash
   pip3 install ipykernel jupyter
   ```

2. **Start up Jupyter lab:** Launch Jupyter Lab from the terminal or command prompt
	```bash
	jupyter lab
	``` 
	*After starting up jupyter lab, ensure you connect the python kernel to python3*
4. **Load the Dataset:** Import the dataset and apply the data cleaning function.
   ```python
   import pandas as pd
   df = pd.read_csv('Most Streamed Spotify Songs 2024.csv')
   df = feature_engineering(df)
   ```
5. **Run Visualization Code:** Execute the code cells containing your visualization scripts to generate and display the plots.
