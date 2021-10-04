# Movie-Recommendation-System

### What is a Recommender System?
Recommender systems can be understood as systems that make suggestions. We often ask our friends about their views on recently watched movies. Based on that, we decide whether to watch the movie or drop the idea altogether. Imagine if we get the opinions of the maximum people who have watched the movie. We also get ideas about similar movies to watch, ratings, reviews, and the film as per our taste.
Selection of Movies would become so easy; don’t you think so?

That is what Recommender systems usually do. They help us make a selection, narrow down our search, and enjoy the selected list of movies without wasting time searching for which movie to watch.

Recommender systems used in the project are of two types Simple Recommender and Content-based filtering.

- Simple Recommender uses provided data in the database to manipulate the results by sorting the different fields in the database.
- Content-based filtering approaches utilize a series of discrete characteristics of an item in order to recommend additional items with similar properties.

### DataSet
We will work with two datasets. One is Credits, and the other is Movies.
The dataset is taken from Kaggle, it is called TMDB data i.e. The Movie Database. The link for the databse used is provided :
This is a smaller database which is used for the Content Recommender System:
https://www.kaggle.com/tmdb/tmdb-movie-metadata

The other dataset is a big one which we have used for the Simple Recommender.
https://www.kaggle.com/rounakbanik/the-movies-dataset?select=movies_metadata.csv

## Simple Recommender

A simple way out to find the score is to go with the average ratings, but using this won’t be fair enough since a movie with 8.7 average rating and only 8 votes cannot be considered better than the movie with 7.9 as as average rating but 40 votes. So,let us use IMDB’s weighted rating (wr) which is given as :-
![image](https://user-images.githubusercontent.com/78725677/120984209-110db700-c798-11eb-983f-21f8bd6d3d74.png)

where,

v is the number of votes for the movie;
m is the minimum votes required to be listed in the chart;
R is the average rating of the movie; and
C is the mean vote across the whole report

The next step is to determine an appropriate value for m, the minimum votes required to be listed in our list of good movies. We will use 90th percentile as our cutoff. In other words, for a movie to feature in the list, it must have more votes than at least 90% of the movies in the list.

The same system is then applied to the genre column so that the best movies based on the genre can be calculated by the system.

Using the other columns in the database downloaded we can then calculate all the other features of the recommendation system like the budget, revenue, return , popularity etc.

### Features
- adult: Indicates if the movie is X-Rated or Adult.
- belongs_to_collection: A stringified dictionary that gives information on the movie series the particular film belongs to.
- budget: The budget of the movie in dollars.
- genres: A stringified list of dictionaries that list out all the genres associated with the movie.
- homepage: The Official Homepage of the move.
- id: The ID of the move.
- imdb_id: The IMDB ID of the movie.
- original_language: The language in which the movie was originally shot in.
- original_title: The original title of the movie.
- overview: A brief blurb of the movie.
- popularity: The Popularity Score assigned by TMDB.
- production_companies: A stringified list of production companies involved with the making of the movie.
- production_countries: A stringified list of countries where the movie was shot/produced in.
- release_date: Theatrical Release Date of the movie.
- revenue: The total revenue of the movie in dollars.
- runtime: The runtime of the movie in minutes.
- spoken_languages: A stringified list of spoken languages in the film.
- status: The status of the movie (Released, To Be Released, Announced, etc.)
- tagline: The tagline of the movie.
- title: The Official Title of the movie.
- video: Indicates if there is a video present of the movie with TMDB.
- vote_average: The average rating of the movie.
- vote_count: The number of votes by users, as counted by TMDB.


## Content-Based Recommender System

### Need for Content Based Filtering
The suggestions and the recommendations made by the recommender engine helps to narrow down the search as per our own preferences.
- The need for content-based filtering arises as we become more selective with our choices and preferences. 
- When we do not wish to waste time searching for similar content.
- When we have a smart way to deal with the issue.
- We can rely on the suggestions 

### What are Content-Based Recommender Systems
They make recommendations based on the descriptive attributes of items.To put it; Content = Description.
In content-based methods, the ratings and buying behavior of users are combined with the content information available in the items. E.g. Jane likes Terminator movie; based on the similar genre keywords other science fiction movies, such as Alien and Predator will be recommended.

### Advantages of Content Based Filtering:
- In making recommendations for new items, when sufficient rating data are not available for that item.
- Even if there is no history of ratings for a particular item; still recommendations can be made.


We will Compute Term Frequency-Inverse Document Frequency (TF-IDF) vectors for each overview. The tf–idf weight is a weight often used in information retrieval and text mining. This weight is a statistical measure used to evaluate how important a word is to a document in a collection or corpus.

TF: The number of times a word appears in a document, divided by the total number of words in that document; Term Frequency, which measures how frequently a term occurs in a document.
TF(t) = (Number of times term t appears in a document) / (Total number of terms in the document).

The second term is the Inverse Document Frequency (IDF), computed as the logarithm of the number of the documents in the corpus divided by the number of documents where the specific term appears. IDF, measures how important a term is.
While computing TF, all terms are considered equally important. Few terms like: and, the, is, of,that; might appear many a times but are of little significance. Hence, we need to weigh down the frequent terms while scale up the rare ones, by computing the following:

IDF(t) = log_e(Total number of documents / Number of documents with term t in it).

Applying TF-IDF in our example; we obtain a matrix , where each column represents a word in the overview vocabulary (all the words that appear in at least one document) and each row represents a movie, as before.This is done to reduce the importance of words that occur frequently in plot overviews and therefore, their significance in computing the final similarity score.

#### Similarity Score
With the matrix obtained, we can now compute a similarity score. There are several ways for this; such as the Euclidean, the Pearson and the cosine similarity score. It is often a good idea to experiment with different metrics.
We will use the cosine similarity to calculate a numeric quantity that denotes the similarity between two movies. We use the cosine similarity score since it is independent of magnitude and is relatively easy and fast to calculate. Mathematically, it is defined as follows:
Since we have used the TF-IDF vectorizer, calculating the dot product will directly give us the cosine similarity score. Therefore, we will use sklearn linear_kernel() instead of cosine_similarities() since it is faster.
We are going to define a function that takes in a movie title as an input and outputs a list of the 10 most similar movies. Firstly, for this, we need a reverse mapping of movie titles and DataFrame indices. i.e., we need a mechanism to identify the index of a movie in our metadata DataFrame, given its title.

We can now define our recommendation function. These are the following steps we’ll follow:-

- Get the index of the movie given its title.
- Get the list of cosine similarity scores for that particular movie with all movies. Convert it into a list of tuples where the first element is its position and the second is the similarity score.
- Sort the aforementioned list of tuples based on the similarity scores; that is, the second element.
- Get the top 10 elements of this list. Ignore the first element as it refers to self (the movie most similar to a particular movie is the movie itself).
- Return the titles corresponding to the indices of the top elements

