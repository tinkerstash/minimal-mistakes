---
title: 'Feature engineering tricks'
categories: 'machine-learning'
excerpt: 'Some common tricks for feature engineering.'
---

### Standardization

Some algorithms are quite sensitive to the scale of each column or feature, e.g., SVM, neural nets. Here are some commons of preprocessing:

* Standard scaler: $$y = \frac{x - \mu_x}{\sigma_x}$$ where $$\mu_x$$ is the mean and $$\sigma_x$$ is the standard deviation. See `sklearn.preprocessing.StandardScaler`. If your data is not Gaussian-like, this might turn out badly. See below for ways to convert to Gaussian. The output has zero mean and unit variance.

* MinMax scaler: $$y = \frac{x - x_{\min}}{x_{\max} - x_{\min}}$$. See `sklearn.preprocessing.MinMaxScaler`. The output is between 0 and 1.

Be careful not to standardize using the test set. Otherwise, you are cheating. It is to be derived from train set and applied to test set.

### Binning numerical variables

If you have a numerical variable, you might consider binning it to enable your algorithm to ignore perturbation within a bin. This may make it easier for the algorithm to learn and generalize.

### Grouping categories

We can try to merge similar categories into one category such that there is more data per category value. This can improve the signal-to-noise ratio and makes life easier for the learning algorithm.

### Transform to Gaussian

A bunch of standard statistical tools assume a Gaussian distribution, e.g., standard hypothesis testing. However, your feature might not follow this distribution. Finding a transformation to make your data follow a Gaussian distribution might require some trial and error. Look up Box-Cox transformations. The one-parameter Box-Cox transformations is

$$y = \begin{cases}
\frac{x^{\lambda} - 1}{\lambda} & \text{if } \lambda \neq 0 \\
\log x & \text{if } \lambda = 0.
\end{cases}$$

It is typical to try $$\lambda=\pm \frac{1}{2}, \pm 1, \pm 2, \pm 3$$.

### Missing data

When data is missing, you can:

* Filter these rows away;
* Imputing their values, say with the mean or median;
* Use algorithms that deal with missing values naturally, e.g., R `gbm` package (gradient boosting).

### One-hot encoding

Categorical features may take several different values. Say they can take $$m$$ different values. We want to create $$m$$ columns such that for each row, only one of the $$m$$ column values is one while the others are zeros. Essentially, we are asking the learning algorithm to learn whether "feature=$$v$$" for various values $$v$$ has any significance. Otherwise, the algorithm will be trying to perform numerical operations on your original categorical value, which may not make any sense.

This is easy to do in Scikit or Tensorflow. Here is an excerpt from Scikit. There are 4 rows, 3 categorical features.

```python
>>> from sklearn.preprocessing import OneHotEncoder
>>> enc = OneHotEncoder()
>>> enc.fit([[0, 0, 3], [1, 1, 0], [0, 2, 1], [1, 0, 2]])  
OneHotEncoder(categorical_features='all', dtype=<... 'numpy.float64'>,
       handle_unknown='error', n_values='auto', sparse=True)
>>> enc.n_values_
array([2, 3, 4])
>>> enc.feature_indices_
array([0, 2, 5, 9])
>>> enc.transform([[0, 1, 1]]).toarray()
array([[ 1.,  0.,  0.,  1.,  0.,  0.,  1.,  0.,  0.]])
```

The first categorical feature leads to two columns because it can take only two values $$0,1$$. The second feature leads to three column because it can take three values $$0,1,2$$. The third feature leads to four columns because it can take four values $$0,1,2,3$$. Altogether there are $$2+3+4=9$$ columns. When asked to transform $$0, 1, 1$$, the output is $$(1,  0),  (0,  1,  0),  (0,  1,  0,  0)$$. Transforming $$1,2,3$$ would lead to $$(0,  1),  (0,  0,  1),  (0,  0,  0,  1)$$ instead.

### Crossing interacting features

We can take arithmetic or geometric or harmonic means of two or more variables. We can cross multiple categorical features. The idea is that these features interact with each other such that simple algorithm such as linear regression or logistic regression may not pick them up together.

For example, say there are two categories $$X,Y$$ and two values for each categories $$0,1$$. Say our output increases with $$X=1,Y=0$$ and $$X=0,Y=1$$ but decreases with $$X=0,Y=0$$ and $$X=1,Y=1$$. If we fix $$Y$$, it would appear $$X$$ has no effect on our output. If we fix $$X$$, it would appear $$Y$$ has no effect either. The algorithm has to consider $$X,Y$$ together in order to predict well. Certain algorithms can do that, but they tend to have higher variance and require more data. It might be better to provide a hint to the algorithm by crossing features that you think are important and interacting.

### Dimensionality reduction

There is a slew of dimensionality reduction techniques such as PCA, autoencoders, clustering. If we have a ton of features, it seems appropriate to do dimensionality reduction to try to quickly see how good these features are, before performing more careful feature selection.

