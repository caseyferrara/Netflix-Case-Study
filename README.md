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
* What trends are observable in the growth of content available on Netflix?
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
    EXTRACT(YEAR FROM DATE(date_added)) AS added_year,
    title,
    genre
  FROM 
    `data-project-99299.netflix.netflix_titles`,
    UNNEST(SPLIT(listed_in, ', ')) AS genre
  WHERE
    DATE(date_added) IS NOT NULL
),
GenreCounts AS (
  SELECT 
    added_year, 
    genre, 
    COUNT(title) AS title_count
  FROM 
    SplitGenres
  GROUP BY 
    added_year, genre
),
YearlyGenreTrends AS (
  SELECT 
    added_year, 
    genre, 
    title_count,
    SUM(title_count) OVER (PARTITION BY added_year) AS total_titles_year,
    (title_count / NULLIF(SUM(title_count) OVER (PARTITION BY added_year), 0)) * 100 AS genre_percentage_year
  FROM 
    GenreCounts
)
SELECT 
  added_year, 
  genre, 
  title_count,
  genre_percentage_year
FROM 
  YearlyGenreTrends
ORDER BY 
  added_year DESC, genre_percentage_year DESC
```
Key Insights:
* International Movies and TV Shows have become increasingly prevalent, indicating Netflix's focus on catering to a global audience. In 2021, International Movies made up about 11.56% of the content added.
* Comedies and Action & Adventure remain popular as they are consistently appearing in the top five genres for the recent years. This reflects their universal appeal.
* Earlier years (2010-2015) show a more limited genre distribution, which gradually expands over the years. This could be due to the expansion of Netflix's catalog and its move into original content.

### 5.2 Content Type Balance

I will investigate the historical trend in Netflix's catalog, specifically examining the balance between movies and TV shows over time. This analysis will utilize SQL queries to quantify and compare the proportions of movies and TV shows released each year, which will reveal any shifts in Netflix's content strategy. I want to determine whether there's a noticeable trend towards either movies or TV series, reflecting on how Netflix adapts its content offerings in response to consumer preferences and market trends.

```
WITH ContentCount AS (
  SELECT 
    EXTRACT(YEAR FROM DATE(date_added)) AS added_year, 
    type, 
    COUNT(show_id) AS count
  FROM 
    `data-project-99299.netflix.netflix_titles`
  WHERE
    DATE(date_added) IS NOT NULL
  GROUP BY 
    added_year, type
),
TotalCountPerYear AS (
  SELECT 
    added_year, 
    SUM(count) AS total_count
  FROM 
    ContentCount
  GROUP BY 
    added_year
)
SELECT 
  a.added_year, 
  a.type, 
  a.count,
  (a.count / NULLIF(b.total_count, 0)) * 100 AS type_percentage
FROM 
  ContentCount a
JOIN 
  TotalCountPerYear b ON a.added_year = b.added_year
ORDER BY 
  a.added_year, a.type
```

Key Insights:
* Starting in 2013, there's a noticeable increase in the percentage of TV shows being added. By 2015, nearly a third of the content added was TV shows, indicating a strategic shift towards increasing TV show offerings.
* From 2018 onwards, the percentage of movies added consistently remains higher than TV shows, but the gap narrows compared to the early years. Movies maintain a majority but with a considerable and stable presence of TV shows, indicating a more balanced approach in content strategy.
* While movies remain dominant, the increase in TV shows reflects Netflix's strategy to offer a more diverse and balanced range of content.

### 5.3 Year-over-Year Growth

I decided to calculate the yearly growth rate in the number of titles added on Netflix, aiming to identify trends in their content strategy. This approach allows us to quantify the year-over-year changes in Netflix's content library, offering insights into periods of significant expansion or strategic shifts. By analyzing these growth rates, we can figure out whether there has been an acceleration in adding new content, which indicates a focus on keeping the library fresh and up-to-date. Alternatively, steady or moderate growth rates might suggest a more balanced approach, maintaining a mix of new and existing titles.

```
WITH YearlyTitleCount AS (
  SELECT 
    EXTRACT(YEAR FROM DATE(date_added)) AS added_year,
    COUNT(show_id) AS title_count
  FROM 
    `data-project-99299.netflix.netflix_titles`
  WHERE
    DATE(date_added) IS NOT NULL
  GROUP BY 
    added_year
),
YearlyGrowth AS (
  SELECT 
    added_year,
    title_count,
    LAG(title_count) OVER (ORDER BY added_year) AS previous_year_count
  FROM 
    YearlyTitleCount
)
SELECT 
  added_year,
  title_count,
  previous_year_count,
  ((title_count - previous_year_count) / NULLIF(previous_year_count, 0)) * 100 AS growth_rate
FROM 
  YearlyGrowth
WHERE 
  previous_year_count IS NOT NULL
ORDER BY 
  added_year DESC
```

Key Insights:
* There was a surge in the number of titles added between 2016 and 2017, with the growth rate peaking at 173.66%. This period likely marks a strategic shift by Netflix towards expanding its content library, possibly aligning with a push for more original content and global expansion.
* Post-2017, the growth rate starts to normalize in 2018 (36.36%) but then shows a gradual decline, leading to a reduction in 2021 (-35.63%). This downturn could indicate a shift in strategy, perhaps focusing more on quality over quantity.
* The high growth rates in certain years suggest a focus on diversifying the content library to cater to a broadening subscriber base and to compete in the streaming market.

### 5.4 Geographical Variance

In my Geographical Variance analysis, I will explore the diversity of Netflix's content across different countries to understand how the platform tailors its offerings to various geographical regions. This analysis aims to reveal the extent of international representation, the prevalence of local content in specific markets, and the variety in genre distribution across regions. I anticipate uncovering patterns such as regional content preferences, the balance between movies and TV shows, and the inclusion of niche genres.

```
WITH SplitCountriesAndGenres AS (
  SELECT 
    show_id,
    type,
    SPLIT(country, ', ') AS country_array,
    SPLIT(listed_in, ', ') AS genre_array
  FROM 
    `data-project-99299.netflix.netflix_titles`
),
ExplodedCountriesAndGenres AS (
  SELECT 
    show_id,
    type,
    country,
    genre
  FROM 
    SplitCountriesAndGenres,
    UNNEST(country_array) AS country,
    UNNEST(genre_array) AS genre
  WHERE 
    country IS NOT NULL AND genre IS NOT NULL
)
SELECT 
  country,
  type,
  genre,
  COUNT(DISTINCT show_id) AS title_count
FROM 
  ExplodedCountriesAndGenres
GROUP BY 
  country, type, genre
ORDER BY 
  country, type, title_count DESC
```

Key Insights:
* There's a significant representation of international movies across various countries, indicating Netflix's focus on a globally diverse content library.
* Certain genres are more prevalent in specific regions, reflecting cultural preferences. For example, India shows a high number of dramas and comedies, while South Korea has a substantial focus on TV dramas and romantic TV shows.
* The data indicates varied content strategies for different regions - some countries have a broader range of genres and types of content, while others have more concentrated offerings.
