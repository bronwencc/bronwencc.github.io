---
layout: post
title:      "Heat-maps Carry Insight"
date:       2019-12-05 04:34:56 +0000
permalink:  heat-maps_carry_insight
---


Heat-maps are one of the more overtly interesting visualizations that I have so far encountered in data science.  With a choice of colors and what value should be at the center, a multiplication table can look far more exciting than "1 x 1 = 1".  Although it looks similarly simplistic, heatmap of a times table provides a larger context to look at individual relationships.

When looking at correlations between the variables in a dataset, a heatmap can be an easy way for visual learners to spot multicollinearity.  Some variables may be very highly correlated but others may be almost correlated to some degree.  In this case, it may then be helpful to see to what degree variables are correlated before removing only the ones with a few high correlations instead of also the variables that are somewhat correlated.  The more independent, uncorrelated variables that can be left in a model, the better.  The benefit of removing a correlated variable is particularly pronounced in linear regression models, where a few redundant variables adds little to the analysis and increases the computation time.  A heatmap can give a quick overview of how similar variables are to one another in a linear way.

However, as aesthetically pleasing as they can look, a data visualization may only be as good as the information it imparts.  For heatmaps, a scale, contrasting colors, and labels provide context.

A phrase heard among accessibility design advocates is that "accessible design is good design", which applies to all areas of life, but also in data visualization design.  In choosing the color scheme for a heatmap, [Seaborn](https://seaborn.pydata.org/generated/seaborn.heatmap.html) has a fairly good default, simply provided by `seaborn.heatmap(*args*)`, which has a scale from light blue to pale orange, passing through black in between.  (The "middle" value can be set by the `center` argument).  I mentioned contrasting colors because if the highest and lowest values have shades that are too similar, it can be hard to tell whether there is difference between them, particularly for someone who is colorblind, for example.  A contrasting (also known as complementary) pair can be found across from each other on the color wheel.

[A color wheel from Moses Harris 1766 via imgur](https://i.imgur.com/Mv7BbPW.jpg)


I find heat-maps to be one of the more simple visualizations that help to understand correlations, especially if it has effective color choices.  They could be useful in taking a quick glance at survey results, to see which answers correlate more or less closely, among other applications.   However, in choosing which variables to use in a model, my preference is to not judge by eye alone, and instead remove variables by checking which numerical values of a correlation matrix are higher than a certain threshold.
