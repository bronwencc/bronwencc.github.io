---
layout: post
title:      "Dimensionality & PCA"
date:       2020-07-27 17:38:51 +0000
permalink:  dimensionality_and_pca
---


One aspect of data science I already had in my daily life before starting this course was clustering.

For instance, in subscribing to podcasts and then subscribing to the podcasts that were advertised on the podcasts, my podcast feed develops clusters for certain media companies.

Clustering relies on the distance between datapoints and determining how similar two datapoints are.  The course used Principal Component Analysis (PCA) to reduce dimensionality and thereby get a more accurate result in the Market Segmentation with Clustering Lab in Section 38.

I wanted to take this opportunity to explore the "curse of dimensionality" as it relates to PCA from `sklearn`.

With more dimensions to a dataset, the less accurate the resultant clustering is *possible* to be.

The more dimensions for each datapoint means there's less overall accuracy.  Each additional dimension means more complexity; more possibilities that lower the chance for a high degree of certainty.

The alpha in statistical p-value tests is often at 0.05.  With a large amount of dimensions (a.k.a. high dimensionality), it becomes impossible to say that the result has 95% certainty because each dimension adds uncertainty to the prediction.  It makes sense to want to have as much information or as many dimensions to a datapoint as possible, but after a sufficiently large amount, the dimensions cause more uncertainty. So, one can use a technique like PCA to identify which dimensions are the most helpful to the predictions.

In Python, a PCA module can be imported from [sci-kit learn](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html):
```
from sklearn.decomposition import PCA
mypca = PCA(n_components=3)
mypca.fit_transform(dataset)
```
It's helpful to try different numbers of components to see which amount has the highest accuracy and then use PCA to transform the data to keep that number of components that are found to be most relevant.

Sci-kit learn's `PCA` also has an explained variance ratio that tells what fraction of the variance is explained by the principal components.

`mypca.explained_variance_ratio_` might give a result like `[0.4, 0.3, 0.22]` (after rounded to the first digit), which means that 40% of the variance is explained by the first component, 30% by the second and 20% by the third.  However, what's usually more important is at what total percent variance explained is sufficient for your purposes (maybe 95-99.99%) and then use the appropriate number of components.  To do that, find the cumulative sum using `numpy.cumsum`:
```
np.cumsum(mypca.explained_variance_ratio_)
```
This gives the rounded result: `[0.4, 0.7, 0.9]`, because 90% of the variance is explained by three principal components and 70% is explained by two.  This also means that all other components explain 10%.  Since `PCA` puts the data in order of highest to lowest, one might see how many more components would explain 95% of the variance along with these first three.  Typically, this cumulative sum, as you add more components, appears to reach an asymptote at 99% or 100%.

After selecting a desired cumulative explained varience threshold and thus, number of principal components, one can fit the data to the PCA and make clusters with it.  When clustering, it compares that many features, which reduces run time and increases the reliability of the clusters.  The distances between datapoints are thereby calculated of the most important features; the smaller, but most impactful, number of dimensions.
