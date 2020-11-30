---
layout: post
title:      "Two Experiences with Feature Engineering Using pandas"
date:       2020-07-27 15:40:02 -0400
permalink:  two_experiences_with_feature_engineering_using_pandas
---


I have now completed four projects for this Data Science course and have learned that one of the aspects of data exploration (Exploratory Data Analysis or the E of OSEMN) that I enjoy is called feature engineering.

I think of it as mainly thinking critically about the dataset and how text variables might become categorical variables, for example.  This post will review two features I engineered, each for a different project.

### Getting the winter season in Module 3
(Project code repository slide deck available at [this link](https://github.com/bronwencc/dsc-2-final-project-online-ds-sp-000/blob/master/Module2-Final-Project.pdf).)
The Northwind database is several SQL tables with sales information for the fictional Northwind company.  Once I knew I wanted to look at how shipping details were different for different orders, I could use the date of a shipment to determine what the weather was like for that order.  This evolved to become a season as a proxy for the weather and then ultimately, my alternate hypothesis was whether freight costs were different for wintry weather.  I could use the date to determine which meteorological season the shipment occurred in. However, orders are shipped to the Americas and Europe, so I needed to account for the fact that the Southern Hemisphere has its seasons opposite from the Northern Hemisphere.

I first added the "Hemisphere" column for whether the ShipRegion was in the Southern or Northern Hemisphere.  One value that I needed to be sure of was "Central America" but I checked and a majority of that region is in the Northern Hemisphere.  This left "South America" as the only ShipRegion in the Southern Hemisphere.

Next, I wrote code to classify the dates by meteorological season based on which three-month period and which hemisphere the record fell into.  This used a series of `if` statements comparing months for seasons (Mar-May, Jun-Aug, Sep-Nov, Dec-Feb) in a function `which_season`, returning numbers 1 to 4, with 1 for Spring.  Below is half of the code from `which_season`, for the Southern Hemisphere, followed by the call creating the `Season` column in the `ordersdf` dataframe.
```
else:#if Southern Hemisphere
        if 3<=month<6: #between March 1 and June 1, excluding June 1
            return 3 #3 for Autumn
        elif 6<=month<9:
            return 4 #4 for Winter
        elif 9<=month<12: #between September 1 and December 1
            return 1 #1 for Spring
        else:
            return 2

ordersdf["Season"] = [which_season(date.month, hemi) for date, hemi in zip(ordersdf.ShippedDate, ordersdf.Hemisphere)]
```

Finally, I sampled the Season feature to get two lists of Freight costs, based on whether it was the number 4 or not:
```
winter=[x for x, y in zip(shipdf["Freight"], shipdf["Season"]) if y==4]
notwinter=[x for x, y in zip(shipdf["Freight"], shipdf["Season"]) if y!=4]
```

![Plot of frequency of orders by season, 1 for spring, 4 for winter](https://raw.githubusercontent.com/bronwencc/bronwencc.github.io/master/img/seasons-orders.png)

### Combining rare dummy features for Module 5
(Module 5 project repository available [here](https://github.com/bronwencc/Module-5-Project).)
Another piece of code I enjoyed working on was for a dataset of details about strategy game apps (from [Kaggle](https://www.kaggle.com/tristan581/17k-apple-app-store-strategy-games/)), where some features were lists of subgenres or available language codes, for instance.  Although I did not ultimately make use of it in my final model, the resulting engineered feature was interesting to consider.

After separating out the lists within those particular features to become dummy columns, I noticed that many dummy columns had few entries.  For example, Weather was a very unusual genre for a strategy game app.

I wrote a function to take in a dataframe of purely dummy columns for the same feature and return it with a new column with "etc" in the name that combined all the dummy columns below a given threshold of non-null values.  The new column "etc" was also a binary of whether a record fell into any one (or more) of these less-frequently appearing dummy categories.

First, it found whether a column had more non-null values and added it to a list of column names, using `isna()` and given a number `threshold` for how many non-null entries a column needs to have to not be combined into "etc".
```
hold=len(dataframe.index)-threshold #acceptable number of null values
    for colname in dataframe.columns:
        totalnull = dataframe[colname].isna().sum() #finding how many NaN entries in a column
        if totalnull > hold: #if the column has more than the acceptable number of null values
            colcombine.append(colname)
```
Then, the function loops through each record. For each column added to the `colcombine` list above, it sets 1 in the "etc" column if the current column from `colcombine` has a 1 at that index.
```
try:
     if(dataframe.loc[idx,col]==1):
           dataframe.loc[idx,prefix+"_etc"]=1 #sets that index position in "etc" to be 1
except: #if the location is null, go to next column in colcombine
      continue
```

When I ran it on subgenre information, I found that even collecting the dummy columns that had fewer than 100 entries (if fewer than 100 records had 1 in that column) resulted in a column with 600+ records.  This was less than a tenth of the dataset but more than the new least-frequently appearing subgenre, with 188 records.  Ultimately, I did not use this function and left the subgenres categorical variable as individual dummy columns so all of them could be taken into account when determining feature importances.
