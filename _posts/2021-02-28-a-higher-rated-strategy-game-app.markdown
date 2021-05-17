---
layout: post
title:      "A Higher-Rated Strategy Game App"
date:       2021-02-28 17:25:34 -0500
permalink:  a_higher_rated_strategy_game_app
---

The [project](https://github.com/bronwencc/Module-5-Project) I completed for the Data Science bootcamp Module 5 involved finding a dataset I was unfamiliar with and analyze it. I browsed Kaggle and found a dataset of variables about strategy game apps from the Apple App Store. It can be found at this link: [https://www.kaggle.com/tristan581/17k-apple-app-store-strategy-games/](https://www.kaggle.com/tristan581/17k-apple-app-store-strategy-games/).

I chose the target feature to be the average user rating and found an AdaBoost model to have 64% accuracy with 11 significant, high-importance features.

One of my conclusions was that a lower-rated app could improve its rating by renaming the app and moving extraneous text to the Subtitle or Description field. This was based on higher-rated apps having more characters in their Description, having a Subtitle, and having fewer characters in their app Name.

Another finding was that a higher number of user ratings means that the app has a higher average user rating. There are higher-rated apps with a small number of ratings, but the chart below shows lower-rated apps have only smaller numbers of ratings.

![Plot of log number of user ratings vs average user rating](https://raw.githubusercontent.com/bronwencc/Module-5-Project/master/images/Distribution%20of%20Rating%20Count.png)

I made a scatter plot using the log of the number of user ratings because otherwise it was harder to compare the distribution on the low end and high end.

If an app is lower-rated, I would suggest asking (more) users to rate it or to look to higher-rated apps' tactics. For instance, I have seen apps (not only strategy games) that occasionally ask whether the user likes the app. If the user says they don't, it links to a feedback form. If the user say they do like the app, it sends them to leave a rating on the app store. This kind of marketing nudge filters out potentially negative reviews and low ratings in favor of higher ratings.

### Features

The analysis found 11 important features that account for whether an app has a higher rating or a lower rating. A higher-rated app was defined as having an average user rating of 4.5 or higher and a lower-rated app was defined as having an average rating of 4 or under. The apps' average ratings were provided at halves, so there are no apps in the dataset with an average rating between 4 and 4.5.

The five features with the highest importance, in order, were:
* The number of ratings
* Whether the app has a Subtitle
* The size of the application
* The length of Description text
* Whether the application is in the Simulation genre

The other features that make up the rest of the eleven were, in order:
* Whether the application is in the Casual genre
* Whether the application has at least one in-app purchase that cost 1.99 or 1.49 USD
* The length of the application name (total characters)
* Whether the application is in the Entertainment genre
* Whether the application supports the Japanese language
* Whether the application has at least one in-app purchase that cost more than 1.99 USD but less than 19.49 USD

I also engineered over 200 features using techniques similar to creating dummy variables (or one-hot encoding). I separated in-app purchase price points into bins, as well as converted the lists for the language and subgenre variables into their own dummy variables, one for each language and each subgenre. Since many records have multiple languages and subgenres, it wasn't a strict one-hot encoding. I wrote code to instead put a 1 in each new variable where the record did have that language, in-app purchase price, or subgenre. Otherwise, the variable for that record was set to be 0.

I also engineered other features that were not important in the final model. Those included whether a Developer was a company and whether the app had a recent update.

### Model

I tried nine models before determining AdaBoost had the best accuracy, at 63%. I then tuned hyperparameters for the `AdaBoostClassifier` with `GridSearchCV`. With optimal parameters, the score improved very slightly to 64%.

I considered whether using only the five most important features (listed earlier) would improve the accuracy and found it lowered it to 60%.

### Future Research

I would want to look into advertising strategies and at the icons and images also displayed when deciding whether to download an app. It would also be great to expand this work to other types of applications apart from strategy games to see how generalizable these findings are.

App Stores in other countries can differ as well. For instance, I could believe strategy game applications with Japanese language support were higher-rated because those games that were highly-rated in the Japanese market could be most compelling to localize into English.

Review and rating systems have also long been plagued by bots or paid reviewers. App stores and other online marketplaces try to identify and remove them, but it could be that higher-rated apps with a high number of ratings have a large number of false 5-star ratings.
