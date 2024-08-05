---
layout: post
title: "Correlation, regression and hypothesis testing"
subtitle: "Basic statistics"
thumbnail-img: /assets/img/binomial-normal-apx.jpg
share-img: /assets/img/binomial-normal-apx.jpg
tags: [probability, data science]
---

Once again, it's interview season, and that means I have some studying to do. This time around, I've racked up enough experience to apply for not only software engineering roles but also data science gigs! Since statistics is a broad field and I don't use every possible concept for my current work, it might help to review the basics. In this post, I'll cover regression and hypothesis testing.

## Correlation

In regression problems, we have some random variable $$X$$ and want to use it to predict some dependent variable $$Y$$, often by fitting some trend line. We can try to measure how linearly related the variables are by using the concepts of covariance and correlation. Since all we care about right now is deviation away from the mean, assume that all random variables have mean $$0$$. (We can make this happen by normalizing, replacing random variable $$X$$ with $$X - \mathbb{E}[X]$$.)

>**Definition.** The covariance between two random variables $$X, Y$$ is $$\text{Cov}[X, Y] = \mathbb{E}[XY]$$.

There are a lot of facts and properties about covariance, but much of basic probability theory is just real analysis in disguise. You might notice that expectations are just integrals, and therefore the covariance is just an inner product in the $$\mathcal{L}^2$$ space of random variables with mean $$0$$. That means everything we know about inner products and vector spaces carries over for free! In particular, the covariance is
- Symmetric: $$\text{Cov}[X, Y] = \text{Cov}[Y, X].$$
- Linear: $$\text{Cov}[aX + Y, Z] = a\text{Cov}[X, Y] + \text{Cov}[X, Z].$$

Since we have an inner product, we might as well also get a norm out of it, which is called the variance.

>**Definition.** The variance of random variable $$X$$ is $$\text{Var}[X] = \sigma^2_X = \text{Cov}[X, X]$$. The standard deviation is $$\sigma_X = \sqrt{\text{Var}[X]}.$$

The variance tells us how much a random variable deviates from its mean, on average. As you might expect, if $$\text{Var}[X] = 0$$, then $$X$$ is a constant.

If we have two random variables $$X, Y$$, we'd like to be able to tell if they are linearly related. Viewing the variables as vectors, this question is the same as asking if they point in the same direction. Since covariance is just an inner product, $$\text{Cov}[X, Y] = \sigma_X \sigma_Y \cos \theta$$, where $$\theta$$ is the angle between the two variables. So we can use a normalized version of covariance to measure the angle.

>**Definition.** The Pearson correlation of random variables $$X, Y$$ is $$\rho_{X, Y} = \text{Cov}[X, Y] / \sigma_X \sigma_Y.$$

This gives us the cosine of the angle between the two vectors. If $$\rho_{X, Y} = 0$$, then the random variables are orthogonal to each other and we say they are *uncorrelated.* If $$\rho_{X, Y} = \pm 1$$, then there is a perfect linear fit between the two.

**Exercise.** What does it mean when $$\rho_{X, Y} = 1?$$ How about when $$\rho_{X, Y} = -1?$$

One reason we like to work with variances, covariances, and uncorrelated random variables so much is that their algebraic properties make it super easy to compute things like sample averages. For instance, suppose you're collecting $$n$$ data samples $$X_1, X_2, ..., X_n$$ and you want to know about the distribution of the sample mean $$X = \sum_i X_i / n$$. If we know all the samples are pairwise uncorrelated, then finding the variance of $$X$$ becomes simple:

$$
\begin{align}
\text{Var}[X] &= \text{Var}[\sum_i X_i / n] \\
&= \frac{1}{n^2} \sum_{i, j}\text{Cov}[X_i, X_j] \\
&= \frac{1}{n^2}( \sum_i \text{Var}[X_i] + \sum_{i \neq j}\text{Cov}[X_i, X_j]) \\
&= \frac{1}{n^2} \sum_i \text{Var}[X_i].
\end{align}
$$

This is extremely useful in hypothesis testing, in which we try to estimate the mean of a population by taking samples. In those applications, we need to ask how close the population mean lies to the sample mean, so knowing the variance enables us to explicitly compute confidence bounds. More on this later.

(Note to self: there are other kinds of correlation, like Spearman rank correlation. Should I take notes on them? It is not my goal to write an entire introductory probability book here.)

## Regression

Ok, now that we have some basic math set up to talk about probability, we can return to the original problem of linear regression. Suppose I have some data samples $$(X, y)$$, where there are $$n$$ samples, $$m$$ input variables $$X$$, and one output variable $$y$$. Further, suppose that I want to fit a trend line through them, modeling the relationship as $$y = X\beta + \epsilon$$, where $$\beta$$ are unknown parameters and $$\epsilon$$ is some random noise. We'd like to try to recover $$\beta$$.

One very reasonable approach would be to pick a model that doesn't make much room for error. Given $$(X, y)$$, we could pick the estimator $$\hat{\beta}$$ to minimize $$ \vert \vert \hat{\epsilon} \vert \vert^2 = \vert \vert y -  X\beta \vert \vert^2.$$ This estimator is called Ordinary Least Squares (OLS), for obvious reasons: it minimizes the squared error term.

**Theorem** (OLS): The optimal solution is to pick $$\hat{\beta} = (X^T X)^{-1} X^T y.$$

**Proof.** We have to minimize the loss function $$\mathcal{L}(\beta) = \vert \vert y - X \beta \vert \vert^2$$. So just set its derivative to zero, $$0 = y - X\beta$$, yielding $$y = X\beta.$$ We would like to directly solve, $$\beta = X^{-1} y,$$ except this doesn't work because $$X$$ might not be invertible. So instead we tack on a transpose, $$X^Ty = X^T X \beta$$. Assuming $$X$$ is linearly independent, we can invert the Gram matrix $$(X^T X)$$ and get the result we wanted. $$\square$$

(Note: ordinary least squares is the special case where $$X$$ consists of two variables: a constant $$1$$ and a single regressor $$x$$. This gives us the model $$y = \alpha + \beta x + \epsilon.$$ It might be a good idea to remember the formula for OLS here: $$\hat{\beta} = \frac{\sum_i (x_i - \bar{x})(y_i - \bar{y})}{\sum_i (x_i - \bar{x})^2}.$$)

Once we have gotten a model, we'd like to evaluate its performance. So now we design a metric called $$R^2$$ to tell us how good its predictions are.

>**Definition.** Let $$y$$ be data with $$n$$ samples, let $$\bar{y}$$ be their mean, and let $$\hat{y}$$ be a prediction. 
>- The residual sum of squares is $$\text{SS}_{\text{res}} = \sum_i (y-\hat{y})^2$$.
>- The total sum of squares is $$\text{SS}_{\text{tot}} = \sum_i (y-\bar{y})^2$$.
>
>Then the coefficient of determination is $$R^2 = 1 - \text{SS}_{\text{res}} / \text{SS}_{\text{total}}$$.

In practice, we get the predicted value by applying some regression model to the data. Very generally speaking, the $$R^2$$ value tells us about the goodness of fit of our model; the value will be higher if our model performs well. For instance, a perfect prediction yields $$R^2 = 1$$, while always predicting the mean yields $$R^2 = 0$$. Worse models than this (like predicting a constant that is not the mean) can even produce a negative $$R^2$$ value.

**Exercise.** Verify all this.

More specifically, we can interpret $$R^2$$ as the fraction of the variance in the data from the mean which was explained by our model. Define the explained sum of squares as $$\text{SS}_{\text{reg}} = \sum_i (\hat{y}-\bar{y})^2$$, the variance of our model's predictions from the mean. For simple linear regression problems, we get the nice equation $$\text{SS}_{\text{reg}} + \text{SS}_{\text{res}} = \text{SS}_{\text{tot}}$$.

(Note: in the special case that we regress $$y$$ on a single variable $$x$$, and we use the OLS regressor, $$R$$ is simply the Pearson correlation coefficient. There is a generalization of this fact when $$x$$ is a vector, but I don't want to write that up right now.)

(P.S. If you're like me and you've taken an econometrics class, then you'll know there is a whole load of fluff about how, if we make a bunch of statistical assumptions about the nature of the errors, then OLS turns out to have nice properties like unbiasedness and consistency. Mumble something about the Gauss-Markov Theorem here. But I'm just reviewing for data science interviews, so it's not my goal to go into economic methodology in detail.)


## Hypothesis testing

TODO: WRITE THIS SECTION

Have some hypothesis about your data and want to test whether it's true. As a toy example, suppose we want to figure out if more people in a large country like ice cream than don't like ice cream.  How should this work? We could try knocking on doors and asking every single citizen whether they like ice cream, but that would be exhausting and maybe impossible. Instead, take a random sample!

- Select a null hypothesis

Various kinds of tests.

- Z-test: compare means when the distribution is normal.
- t-test: compare means more generally.
- F-test: paired data.
