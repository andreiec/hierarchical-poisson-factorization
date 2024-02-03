## Hierarchical Poisson Factorization

The project for the Probabilistic Programming course consisted of implementing a Hierarchical Poisson Factorization in Python using PyMC.

## Exploratory Data Analysis

The dataset was made of a single CSV file (“beer_reviews.csv”). It has a total of 1.5 million beer reviews. For this project, I only used the top 100 most rated beers and top 100 users by number of reviews submitted, thus reducing the number to 8468 reviews. I also multiplied the reviews by a factor of 2 to get from the form 0.5, 1.0, 1.5.. to integers 1, 2, 3.. . The results above are before filtering by top users and top beers.

## Train Test Split

I divided the dataset into two groups: training and testing data. They were divided at random using the conventional 80-20 proportion. The split was arbitrary because it allows us to preserve the attributes of the entire dataset in the train and test datasets while also lowering the likelihood of producing bias in one of the subsets. I used the “sklearn.train_test_split” function to achieve this.

## The model

The model that I used is the one presented in the paper attached to the project requirements file. It consists of four Gamma distributions and a Poisson distribution. As for the hyperparameters, I’ve started with the recommended 0.3 for each value and after training for some iterations I ran a grid search. The metric that I used for the grid search was Root mean square error (RMSE) and the values searched were:

a_prime, c_prime: 0.3, 0.5, 1.0, 1.5, 2.0 (Gamma Left)
a_prime/b_prime, c_prime/d_prime: 0.3, 0.5, 1.0, 1.5, 2.0 (Gamma Right)
a, c: 0.3, 0.5, 1.0, 1.5, 2.0 (Gamma Features)

## Training

Training the model consisted of using the “sample” function of the PyMC module where I used the following hyperparameters:

- samples: 10000
- tune: 8000
- chains: 4

After training, I concatenated each chain into one.

To evaluate the model, I constructed predictions from test pairings of users and beers, then measured the differences between the ground truth and anticipated values. To forecast new values, I used the trace from the sample steps and computed the rating using the values for each feature by multiplying each value in each sample iteratively.

The other metric that I used besides RMSE was 1-off Accuracy, where a prediction is considered accurate if it lies within a specified range - in this case one unit from the true values.


After training we get the following results:

- train_data
  - RMSE = 1.355
  - oneOffAcc = 0.490

- test_data
  - RMSE = 4.244
  - oneOffAcc = 0.034

We can clearly see that the model is struggling to predict data that it has not seen yet. Due to limited computational power I was able to run only a fraction of samples needed for a performing model.

## Recommendations

The process of proposing new beers to users is quite simple. I simply utilize the parameters from traces to produce a prediction about what ratings a user would give to all of the beers then sort the results by the biggest score. After that I select the first 5 beers (this parameter can be changed to fit more beers).

## Conclusion

Even if there is a lot of room for improvements and tweaking, the model can understand complicated relationships between users and beers and accurately predict data when given enough training examples. While this model is strong, one disadvantage of utilizing MCMC for parameter updates is scalability, since it appears to take a long time to train a model even for a small enough dataset when compared to other methods of parameter updates. Nonetheless, this technique is interesting, and with more tuning, it has the potential to provide excellent outcomes.
