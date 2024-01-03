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
