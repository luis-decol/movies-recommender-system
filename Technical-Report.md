The objective of this project was to build a movies recommender system. I took three different approaches: the first one using collaborative filtering, the second one using movies similarities and the third one using a hybrid system combining the first two. As these models have different inputs and outputs, they have different metrics to evaluate them, being the most important RMSE and cosine similarity. I’ve been able to predict the ratings users would give to movies they haven’t watched and determine the similarity between movies, and this can be used to recommend movies to users.

The data comes from the famous MovieLens dataset, which was extracted from the MovieLens webpage by GroupLens Research from the Department of Computer Science and Engineering at the University of Minnesota Twin Cities. The datasets used for this project consisted on the following information: user ratings to different movies and movies information. The main limitation has been computational power, as my ratings dataset, after data cleaning and pre-processing, consisted on 22 million user-movie combinations (≈ 102,000 users and 12,000 movies). This project has been done using a sample of only 3 million items, so one of my next steps will be to run the models with more data to get better results. From this problem derives the limitation of user recommendations: as not all the users are taken into account, I can’t recommend movies to some of them. Another limitation was that the information I had about movies wasn’t the best so, although I could calculate a good similarity score between movies, this could’ve been even better with better data.

Moving on deeply into the steps I undertook, in total I had 5 datasets: user ratings, movies metadata, movies keywords, movies crew (cast and production team) and links (to link the dataframes with different movie ids). My data cleaning and pre-processing was done using the first two, as those were the main ones for my models. Some of the steps I took in this part were dropping useless columns, changing data types, filling missing values with 0s, extracting features from columns (e.g. year from release date), creating new columns, etc. Also, I decided to only keep the users who had rated 50 or more movies and the movies that had received 50 or more ratings, because the ones with less ratings aren’t a good source of information (and also this way I could reduce the size of the dataset).

For the EDA, I explored the ratings distributions in different ways (ratings for each category, number of ratings by user/movie, etc.) and also did some NLP to compare the movies summaries of good vs bad movies. I discovered that there was a user who had rated more than 18,000 movies (that’s the equivalent to watching one movie per day for 50 years!) but then, reading online I found that this was actually true.

For the modelling section I used Surprise, a Python library for building and testing recommender systems. Before trying any models, to reduce fitting and testing times, I proceeded to further reduce the dataset by only keeping movies with more than 5000 ratings and users with more than 150 ratings. This reduced the dataset from 22 to 10 millions rows, meaning that it was still too big, so I decided to take a random sample of size 0.3 and proceeded to work with those 3.1 million items. Then I started the modelling. First I tried a collaborative-filtering approach, both user and item based. I started by using the famous SVD algorithm, the Slope One algorithm and the SVD++, being the grid search of the last one the one with best result (smallest RMSE, 0.679). (Here I had many more models to try but, to not make it take too long, I used Surprise’s model performance comparison to choose which ones to try).

With this model I proceeded to get the predicted ratings to visually compare them with the real ratings to get a taste of how well the model performed. As the model predicted values between 1 and 5 but the ratings went from 0.5 to 0.5, I decided to round the estimations to the nearest 0.5/1 (e.g. if a movie has a true rating of 4.5 and an estimated rating of 4.39, as it is impossible to rate a movie with 4.39, the estimated rating should be rounded to 4.5). This was only done for visualizing the results, as the RMSE was calculated with the original estimations.

Then I proceeded to calculate Precision@k and Recall@k. This helped me to confirm that the SVD++ performed better than the Slope One. Also, the Precision scores are good, and the recall ones not so much, while the Recall improves and Precision slightly decays with a bigger K. This happens because Precision@K is definded as the number of relevant items recommended out of all the recommended (being 'relevant' the ones with a predicted rating above a threshold, 3.5 in this case) and Recall@K as the number of relevant items recommended out of all the relevant ones, so if I only recommend few movies, or calculate the Recall with a low K, the Recall@K will be low. The final thing I did with this collaborative-filtering model was to create a function that would return the top n recommendations for a given user.

The next section consisted on a content-based model that aimed to, given a movie as an input, recommend the most similar movies based on their characteristics. I first built one using the text from the movie overview and tagline and then improved it including also the top 3 actors, the director (3 times to give it more weight) the genre and the keywords. The output of this model was a DataFrame with the top n more similar movies and their corresponding cosine similarity score.

Finally, I tried to build a hybrid recommender system, one that could include the two previous models. There are many different approaches for this, so first I tried one that takes as an input a user id and a movie title and, based on that, recommends the top n similar movies but also with the predicted rating for each movie. This model isn’t optimized yet and I have to try different approaches, but for now that’s what I have. My next steps are to keep on working on the hybrid model and try to run the collaborative-filtering models in the whole dataset (or at least in bigger samples) to make it better.

How and where could this model be applied in an enterprise environment? There are many ways of using this kind of models in real life and it doesn’t have to necessarily be with movies, some of them are very famous, such as Netflix, Amazon and YouTube. This could be used for any retailer of any kind that has information about the products and/or the users. Also, could be used for news recommendations, search queries, restaurants, financial services, etc.