# The Evolution of Content Diversity on Netflix

## 1. Summary

This study will analyze how the variety of genres and the balance between movies and TV shows on Netflix have changed over time.

### 1.1 Purpose Statement

The streaming market is increasingly competitive, with numerous platforms vying for viewers' attention. Netflix needs to understand the evolution of its content diversity to stay relevant and appealing to a broad audience. Are they diversifying their content offerings effectively over time? 

## 2. Ask

### 2.1 Business Task

Analyze the Netflix dataset to understand the evolution of content diversity on the platform, focusing on the distribution of genres, the balance between movies and TV shows, and the distribution of content ratings over time. The aim is to provide strategic insights for content acquisition and marketing, ensuring that Netflix's offerings remain competitive and appealing to a broad audience.

### 2.2 Stakeholders

* Netflix's Content Strategy Team  
* Marketing Department  
* Investors and Analysts

### 2.3 Key Questions

* How has the distribution of different genres on Netflix changed over the years? Are certain genres becoming more or less prevalent?
* What is the historical trend in the balance between movies and TV shows on Netflix? Is there a shift towards one content type over the other?
* What trends are observable in the release years of content available on Netflix? Is there an inclination towards newer or older titles?
* Is there a noticeable difference in content diversity when considering different geographical regions or countries, if such data is available?

## 3. Prepare

### 3.1 Dataset

The [Netflix Movies and TV Shows](https://www.kaggle.com/datasets/shivamb/netflix-shows) dataset sourced from Kaggle encompasses a total of 8,807 entries, each representing a unique title available on the platform. It is structured across 12 distinct columns, capturing a range of attributes for each title. These attributes include the show's unique identifier (show_id), the type of content (categorized as either 'Movie' or 'TV Show'), the title name, director, cast involved, country of production, the date it was added to Netflix, its release year, viewer rating, duration, the genres it is listed under (listed_in), and a brief description of the show or movie. This dataset provides a comprehensive snapshot of Netflix's content, offering insights into aspects like genre diversity, content type distribution, production origins, and temporal trends in content acquisition and release, which are pivotal for analyzing Netflix's evolving content strategy and market positioning.

### 3.2 Data Credibility

Credibility of the Netflix dataset from Kaggle depends on its source, completeness, and accuracy. While hosted on Kaggle, a reputable data science platform, the dataset's reliability depends on the uploader's credibility, which requires further verification. Its broad coverage across genres, release years, and countries suggests completeness, but the accuracy of details like release years and genre classifications needs thorough checking. The dataset's relevance is time-sensitive, given the rapidly changing streaming content landscape. This dataset offers potential insights into Netflix's content strategy but should be used cautiously, acknowledging these limitations.

### 3.3 Data Organization

The dataset contains 8,807 rows.

There are 12 columns in the dataset, each representing a different attribute of the titles. These attributes include:

* show_id: A unique identifier for each show.
* type: Differentiates between a movie and a TV show.
* title: The title of the show or movie.
* director: The director of the show or movie.
* cast: The cast involved in the show or movie.
* country: The country of production.
* date_added: The date the title was added to Netflix.
* release_year: The year the title was released.
* rating: The viewer rating of the title.
* duration: Duration of the show or movie.
* listed_in: The genres the title is listed under.
* description: A brief description of the title.

## 4. Process

For the analysis of the Netflix dataset, I chose due to its proficiency in handling and querying large datasets efficiently. Its compatibility with structured data formats allows for easy manipulation and extraction of insights from the dataset's diverse content. SQL's capabilities for complex queries are essential for in-depth analysis of genre distribution, content types, and temporal trends. SQL's integration with data visualization tools make it an ideal tool for reliable and effective data analysis, ensuring both accuracy and consistency in the derived insights.

### 4.1 Cleaning the Data

Before importing the dataset into BigQuery, we will use Google Sheets to perform data cleaning on the CSV-formatted dataset. This step is crucial to ensure data quality and accuracy, which will facilitate more reliable and insightful analysis in BigQuery.

### 4.2 Identifying Missing Values

I used the ISBLANK() function in Google Sheets to identify missing values across the dataset. A total of 888 rows were found missing *country* values. Given the importance of the *country* column in our analysis, these rows will be removed to maintain data consistency and integrity. Additionally, 9 rows missing the *date added* field will also be eliminated. For the *rating* column, I identified 6 rows with missing values, which I was able to manually input the appropriate ratings.

### 4.3 Standardizing Formats

* I selected the date columns and applied a consistent format. This was achieved through Format > Number > Date, where I chose a standard date format. This step is vital for accurate temporal analysis, as inconsistent date formats can lead to errors in querying and interpretation.

* Columns containing numbers were formatted for uniformity, using Format > Number. I chose appropriate formats for different numerical data types, such as plain numbers for counts and decimals for more precise values. This standardization ensures that numerical data is interpreted correctly during analysis.

* Functions like =PROPER(text), =UPPER(text), and =LOWER(text) were used to ensure consistent capitalization across all text entries. Additionally, the =TRIM(text) function helped remove any extra spaces, ensuring text data remains clean and uniform.

* I chose to split this data into two distinct columns for clarity: one for movies (minutes) and another for TV shows (seasons). This approach not only simplifies analysis but also aids in maintaining data clarity and consistency.

### 4.4 Importing Dataset into BigQuery

After completing the data cleaning process in Google Sheets, I started importing the dataset into BigQuery for analysis. This began with exporting the dataset from Google Sheets as a CSV file, ensuring compatibility with BigQuery. The CSV file was then uploaded to Google Cloud Storage, which acts as a bridge for transferring data to BigQuery. In BigQuery, we created a new dataset and established a table with a schema reflecting the CSV file's structure and data types. The final step involved importing the data from Google Cloud Storage into this BigQuery table, carefully configuring the import settings to align with the dataset's format and structure.

## 5. Analyze

In our analysis phase, I'm focusing on key aspects of Netflix's content evolution. I'll examine genre distribution over time to see which genres have become more or less prevalent, reflecting shifts in viewer preferences. Another aspect is the balance between movies and TV shows, determining if there's a trend favoring one content type. We'll also analyze the release years of titles to understand if Netflix is focusing on newer or older content. Additionally, we'll explore content diversity across different geographical regions to see if Netflix's library varies by region. This analysis aims to provide a nuanced understanding of how Netflix's content strategy has evolved, offering insights for data-driven decisions.

### 5.1 Genre Distribution Trends

In my Genre Distribution Trends analysis, I'll explore the changes of genres on Netflix over the years. This will reveal shifts in viewer preferences and adaptations in Netflix's portfolio. My focus will be on identifying trends. This analysis aims to understand Netflix's content diversification strategy which will provide insights into its content acquisition and production decisions.

```
WITH SplitGenres AS (
  SELECT 
    release_year, 
    title,
    genre
  FROM 
    `data-project-99299.netflix.netflix_titles`,
    UNNEST(SPLIT(listed_in, ', ')) AS genre
),
GenreCounts AS (
  SELECT 
    release_year, 
    genre, 
    COUNT(title) AS title_count
  FROM 
    SplitGenres
  GROUP BY 
    release_year, genre
),
YearlyGenreTrends AS (
  SELECT 
    release_year, 
    genre, 
    title_count,
    SUM(title_count) OVER (PARTITION BY release_year) AS total_titles_year,
    (title_count / SUM(title_count) OVER (PARTITION BY release_year)) * 100 AS genre_percentage_year
  FROM 
    GenreCounts
)
SELECT 
  release_year, 
  genre, 
  title_count,
  genre_percentage_year
FROM 
  YearlyGenreTrends
ORDER BY 
  release_year, genre_percentage_year DESC
```
Key insights from the analysis:
* In the last five years, International Movies have consistently been one of the top genres on Netflix. This indicates a strong focus on diversifying content to cater to a global audience.
* The genre Dramas remains at the top, consistently ranking among the top three genres. This genre's popularity highlights its appeal across different viewer segments.
* While international content and dramas dominate, there's also a notable presence of other genres like 'Comedies' and 'Documentaries', reflecting Netflix's strategy to maintain a varied content portfolio.

## 5.2 Content Type Balance

I will investigate the historical trend in Netflix's catalog, specifically examining the balance between movies and TV shows over time. This analysis will utilize SQL queries to quantify and compare the proportions of movies and TV shows released each year, which will reveal any shifts in Netflix's content strategy. I want to determine whether there's a noticeable trend towards either movies or TV series, reflecting on how Netflix adapts its content offerings in response to consumer preferences and market trends.

```
WITH ContentCount AS (
  SELECT 
    release_year, 
    type, 
    COUNT(show_id) AS count
  FROM 
    `data-project-99299.netflix.netflix_titles`
  GROUP BY 
    release_year, type
),
TotalCountPerYear AS (
  SELECT 
    release_year, 
    SUM(count) AS total_count
  FROM 
    ContentCount
  GROUP BY 
    release_year
)
SELECT 
  a.release_year, 
  a.type, 
  a.count,
  (a.count / b.total_count) * 100 AS type_percentage
FROM 
  ContentCount a
JOIN 
  TotalCountPerYear b ON a.release_year = b.release_year
ORDER BY 
  a.release_year, a.type
```

Key insights from analysis:
* There's a clear trend showing a gradual shift from movies to TV shows. In earlier years like 2012 and 2013, movies dominated the content significantly, accounting for over 75% of the titles. However, by 2021, this trend reversed, with TV shows comprising approximately 56% of the content.
* The increasing percentage of TV shows from 2015 onwards, with a notable jump around 2019-2020, reflects Netflix's emphasis on serialized content. This could be in response to consumer preferences for binge-watching and the success of original series.
* This shift might be part of a broader diversification strategy, aiming to offer a more balanced mix of movies and series to cater to varied viewer preferences and compete with other streaming services.
