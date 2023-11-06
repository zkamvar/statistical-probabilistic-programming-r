---
title: 'Bayesian Statistics'
teaching: 10
exercises: 2
---



:::::::::::::::::::::::::::::::::::::: questions 

- What is Bayesian statistics?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Basic idea of Bayesian statistical thinking
- Bayesian formula: prior, likelihood, posterior
- Grid approximation
- Communicating posterior information
  - Point estimates
  - Intervals

::::::::::::::::::::::::::::::::::::::::::::::::

## Introduction

The fundamental ingredient of Bayesian statistics and probabilistic thinking is the Bayes' theorem, stated as

$$
  p(\theta | X) = \frac{p(\theta)  p(X | \theta)}{p(X)} \\
$$

Given a statistical model, the theorem can be used to infer probabilities of the values of the model parameters $\theta$ conditional on the available data $X$. These probabilities are quantified by the *posterior distribution* $p(\theta | X)$. The *prior distribution* $p(\theta)$ is used to impose beliefs about $\theta$ without taking the data into account. The *likelihood function* $p(X | \theta)$ gives the probability of the data conditional $\theta$ and specifies the effect of data on the posterior. The denominator on the right-hand side $p(X)$ is called the marginal probability, which is often practically impossible to compute, and usually the proportional version of the Bayes' formula is used:


$$
p(\theta | X) \propto p(\theta)  p(X | \theta).
$$

The proportional Bayes' formula produces an unnormalized posterior distribution which can then be normalized to access the normalized posterior. 

## Example: Binomial model

Let us illustrate the use of the Bayes' theorem with an example. 

Assume we are interested in estimating the prevalence of left-handedness based on a sample of 50 children. In this sample 7 children reported left-handedness and 43 were right-handed. Since the outcome is binary and the children independent (assumption) we can model left-handedness with the binomial distribution:

$$
\text{Number of left-handed} \sim Bin(n, \theta)
$$

The parameters $n$ and $\theta$ refer to the total number of children and proportion of the left-handed in the population, respectively. The likelihood for the data is $p(X|\theta) = Bin(7 | 50, \theta).$

Next, we should think what sort of prior information we'd like to use. For instance, the following distributions might be considered: $Unif(0, 1); \, N(0, 1)$ and $Beta(1, 10)$. 

::::::::::::::::::::::::::::::::::::: discussion

What could be the rationale for choosing each of these prior distributions?

::::::::::::::::::::::::::::::::::::::::::::::::



::::::::::::::::::::::::::::::::::::: instructor

For example: 

- uniform = absolutely no idea about $\theta$ a-priori
- normal = a parsimonious distribution, often a good choice
- Beta = conjugate prior, hyperparameters can be interpreted as prior data, 1 out of 10+1 people is a leftie. 

Extra-reading: BDA p.34: interpreting hyperparameters as prior data, conjugate prior

::::::::::::::::::::::::::::::::::::::::::::::::


### Grid approximation

In many cases, analytical computations of the posterior are not feasible and approximation methods need to be used. Although the analytical posterior for the binomial model (with Beta prior) is available, we'll next illustrate fitting this model with a grid approximation. 

The grid approximation is a method for approximating the posterior distribution. The idea is to discretize the parameter space into a grid and then calculate the likelihood and prior at each grid point. The product of these values forms the approximate unnormalized posterior. To obtain a proper probability distribution, the unnormalized posterior is then normalized by dividing each value by the sum of all values times the discretization interval (in 1D, area in 2D etc.). This results in an approximation of the posterior distribution. 

In R, the model can be implemented as follows. First we define the data variables: 


```r
# Sample size
N <- 50

# 7/50 are left-handed
x <- 7

# Define a grid of points in the interval [0, 1], with 0.01 interval
delta <- 0.01
theta_grid <- seq(from = 0, to = 1, by = delta)
```

Next, we'll define the likelihood, uniform prior and posterior functions. 


```r
likelihood <- dbinom(x = x, size = N, prob = theta_grid)
prior <- rep(1, length(theta_grid))
posterior <- likelihood*prior

# normalize posterior
posterior <- posterior/(sum(posterior)*delta)

# Make data frame
df <- data.frame(theta = theta_grid, likelihood, prior, posterior)
```

Finally, we can plot these functions


```r
# wide to long format
df_l <- df %>%
  gather(key = "Function", value = "value", -theta)

# Plot
p1 <- ggplot(df_l, 
       aes(x = theta, y = value, color = Function)) + 
  geom_point(size = 2) +
  geom_line(size = 1) +
  scale_color_grafify()
```

```{.warning}
Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
ℹ Please use `linewidth` instead.
This warning is displayed once every 8 hours.
Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
generated.
```

```r
p1
```

<img src="fig/bayesian-statistics-rendered-unnamed-chunk-4-1.png" style="display: block; margin: auto;" />


::::::::::::::::::::::::::::::::::::: instructor

Take a moment to analyze the figure. 

::::::::::::::::::::::::::::::::::::::::::::::::


Notice that the likelihood function is not a distribution in terms of the parameter $\theta$, so it doesn't sum to one. Below, we normalize it for better illustration. 


Now that we have a posterior distribution (approximation) available, we can try to quantify it by computing features of it. The mode, average and variance and commonly employed point estimates: 


```r
data.frame(Estimate = c("Mode", "Mean", "Variance"), 
           Value = c(df[which.max(df$posterior), "theta"],
                     sum(df$theta*df$posterior*delta), 
                     sum(df$theta^2*df$posterior*delta) - sum(df$theta*df$posterior*delta)^2))
```

```{.output}
  Estimate      Value
1     Mode 0.14000000
2     Mean 0.15384615
3 Variance 0.00245618
```


::::::::::::::::::::::::::::::::::::: discussion

These are point estimates. What information do they give? What is absent? Think of other ways in which you could quantify the posterior. How could you quantify the uncertainty. 

Why are the mode, mean and variance computed the way they are above?

What is the conclusion of the analysis in terms of handedness? 

::::::::::::::::::::::::::::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::: instructor

Actual value from a study from 1975 with 7,688 children in US grades 1-6 was 9.6%

Hardyck, C. et al. (1976), Left-handedness and cognitive deficit
https://en.wikipedia.org/wiki/Handedness

::::::::::::::::::::::::::::::::::::::::::::::::



### Effect of the prior

Next, let's compare the effect of the prior, using the distributions mentioned above. 


```r
uniform_prior <- dunif(x = theta_grid, min = 0, max = 1)
normal_prior <- dnorm(x = theta_grid, mean = 0, sd = 0.1)
beta_prior <- dbeta(x = theta_grid, shape1 = 2, shape2 = 10)

posterior1 <- likelihood*uniform_prior/(sum(likelihood*uniform_prior)*delta)
posterior2 <- likelihood*normal_prior/(sum(likelihood*normal_prior)*delta)
posterior3 <- likelihood*beta_prior/(sum(likelihood*beta_prior)*delta)

# Normalized likelihood
likelihood <- likelihood/(sum(likelihood)*delta)


df2 <- data.frame(theta = rep(theta_grid, 3), 
                  likelihood = rep(likelihood, 3),
                  prior = c(uniform_prior,  normal_prior, beta_prior), 
                  posterior = c(posterior1, posterior2, posterior3), 
                  prior_type = rep(c("uniform", "normal", "beta"),
                                   each = length(theta_grid)))

df2_w <- df2 %>% gather(key = "Function", value = "value", -c(theta, prior_type))

p2 <-ggplot(df2_w,
         aes(x = theta, y = value, color = Function)) + 
    geom_point() + 
    geom_line() +
    facet_wrap(~prior_type,
               scales = "free", 
               ncol = 1) +
  scale_color_grafify()
    
p2    
```

<img src="fig/bayesian-statistics-rendered-unnamed-chunk-6-1.png" style="display: block; margin: auto;" />


::::::::::::::::::::::::::::::::::::::::::::::::::::: challenge

Play around with the parameters of the prior distributions and see how it affects the posterior. 

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

The main limitation of the grid approximation method is that it becomes impractical for models with a large number of parameters. The reason is that the number of computations grows as $O \{ n^p \}$ where $n$ is the number of grid points per model parameter and $p$ the number of parameters. This quickly becomes prohibitive, and the grid approximation is seldom used in practice. The standard approach to posterior computations is to draw samples from it with Markov chain Monte Carlo (MCMC) methods, which we will go through later. In the following episodes, we will learn how to perform computations on samples drawn from a distribution and eventually implement our own MCMC algorithms. 

## Example: Normal model 

Let us implement another standard statistical model, the normal model, with the grid approximation. We'll assume that the variance of the model in known, $\sigma^2=1,$ and we'd like to learn the mean parameter $\mu.$

Let us first generate some data from the model, 5 independent observations from a normal distribution with unknown mean parameter:


```r
# Sample size
N <- 5

# Generate data
sigma <- 1
unknown_mu <- runif(1, -5, 5)
x <- rnorm(n = N,
           mean = unknown_mu,
           sd = sigma) 


# Define a grid of points for mu
delta <- 0.01
mu_grid <- seq(from = -5, to = 5, by = delta)
```


Similarly as before, we'll then define the prior, likelihood, and compute posterior distribution. We'll use a normal prior with mean 0 and standard deviation 1 for $\mu$. As the observations are assumed to be independent, the likelihood function is product of the likelihoods of the individual points:

$$p(X | \theta) = \prod_{i = 1}^{N} p(X_i | \theta),$$

where $X_i$ are individual data points and $N$ the sample size. 

$$\log p(X | \theta) = \sum_{i = 1}^{N} \log p(X_i | \theta)$$


::::::::::::::::::::::::::::::::::::::::::: challenge

Implement the grid approximation the normal model with the generated data as described above.

:::::::::::::::::::::: solution



```r
df <- data.frame(mu = mu_grid)

# Likelihood
for(i in 1:nrow(df)) {

  df[i, "likelihood"] <- prod(dnorm(x = x,
                                    mean = df[i, "mu"], sd = sigma,
                                    log = FALSE))
}


# Prior: mu ~ N(0, 1)
df <- df %>% 
  mutate(prior = dnorm(x = mu,
                     mean = 0 ,
                     sd = 1))

# Posterior
df <- df %>% 
  mutate(posterior = prior * likelihood) %>% 
  mutate(posterior = posterior/(sum(posterior)*delta)) # normalize

# Normalize likelihood (for better illustration)
df <- df %>% 
  mutate(likelihood = likelihood/(sum(likelihood)*delta))
```

::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::


Now, we can plot the prior, likelihood, and posterior, along with the unknown true value of $\mu$ (black vertical line).    



```r
# Wide --> long format
df_l <- df %>% 
  gather(key = "Function", value = "value", -c("mu"))


p <- ggplot(df_l,
                aes(x = mu, y = value, color = Function)) +
  geom_line(size = 1) +
  geom_vline(xintercept = unknown_mu, 
             color = "black") +
  scale_color_grafify()

print(p)
```

<img src="fig/bayesian-statistics-rendered-unnamed-chunk-9-1.png" style="display: block; margin: auto;" />


::::::::::::::::::::::::::::::::::::: discussion

Play around with different samples sizes $N$ and priors for $\mu$ and see how the results change. 

What happens if the true value of $\mu$ is not within the defined grid?

:::::::::::::::::::::::::::::::::::::::::::::::









## Example: Gamma model

Assume the following data was generated from a $\Gamma(\alpha, \beta)$ distribution: 


```r
X <- c(0.34, 0.2, 0.22, 0.77, 0.46, 0.73, 0.24, 0.66, 0.64)
```

Estimate the parameters alpha, beta using the grid approximation. Compute the marginal posterior of alpha. 


Let's define a grid of points for $\alpha$ and $\beta$ and make a data frame with all the pairwise combinations of the grid points. Note that since this model contains two parameters, the grid points reside in 2D space!


```r
delta <- 0.05
alpha_grid <- seq(from = 0.01, to = 15, by = delta)
beta_grid <- seq(from = 0.01, to = 25, by = delta)


df <- expand.grid(alpha = alpha_grid, beta = beta_grid)
```


Next, we'll compute the likelihood which is the product of the likelihoods of individual observations. 


```r
# Loop over all alpha, beta combinations
for(i in 1:nrow(df)) {
  df[i, "likelihood"] <- prod(
    dgamma(x = X,
           shape = df[i, "alpha"],
           rate = df[i, "beta"])
    )
}
```

Next, we'll add priors for $\alpha$ and $\beta$. They are both positive which should be reflected in the prior. A conjugate prior for the Gamma likelihood [exists](https://en.wikipedia.org/wiki/Gamma_distribution#Bayesian_inference) but we'll use simple $\Gamma$ priors with large variance.


```r
# Priors: alpha, beta ~ Gamma(2, .1)
df <- df %>% 
  mutate(prior = dgamma(x = alpha, 2, .1)*dgamma(x = beta, 2, 0.1))

# Posterior
df <- df %>% 
  mutate(posterior = prior*likelihood) %>% 
  mutate(posterior = posterior/(sum(posterior)*delta^2)) # normalize


# Plot
p_joint_posterior <- df %>% 
  ggplot() + 
  geom_tile(aes(x = alpha, y = beta, fill = posterior)) + 
  scale_fill_gradientn(colours = rainbow(5))

p_joint_posterior
```

<img src="fig/bayesian-statistics-rendered-unnamed-chunk-13-1.png" style="display: block; margin: auto;" />


Next, we'll compute the MAP, which is a point in the 2-dimensional parameter space. 


```r
df[which.max(df$posterior), c("alpha", "beta")]
```

```{.output}
      alpha beta
59194  4.66 9.86
```

However, often in addition to the parameters of interest, the model contains parameters we are not interested. For instance, if we were only interested in $\alpha$, then $\beta$ would called a 'nuisance' parameter. Nuisance parameters are part of the full ('joint') posterior, but they can be discarded by integrating the joint posterior over these parameters (see BDA3: p.63 for details). A posterior integrated over some of the parameters is called a marginal posterior. 

Let's now compute the marginal posterior for $\alpha$ by integrating over $\beta$. Intuitively it can be helpful to think of marginalization as a process where all of the joint posterior mass is drawn towards the $\alpha$ axis, as if drawn by a gravitational force. 


```r
# Get marginal posterior for alpha
alpha_posterior <- df %>% 
  group_by(alpha) %>% 
  summarize(posterior = sum(posterior)) %>% 
  mutate(posterior = posterior/(sum(posterior)*delta))


p_alpha_posterior <- alpha_posterior %>% 
  ggplot() + 
  geom_line(aes(x = alpha, y = posterior), 
            color = posterior_color, 
            size = 1)

print(p_alpha_posterior)
```

<img src="fig/bayesian-statistics-rendered-unnamed-chunk-15-1.png" style="display: block; margin: auto;" />


::::::::::::::::::::::::::::::::::::::::::::::::::::: challenge

Does the MAP of the joint posterior of $\theta = (\alpha, \beta)$ correspond to the MAPs of the marginal posteriors of $\alpha$ and $\beta$?

::::::::::::: solution
No. Why?
:::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::


## Posterior intervals


## Final challenge

::::::::::::::::::::::::::::::::::::: challenge

The following data is a collection of daily milk yield (in liters) for dairy cows.


```r
X <- c(30.25, 34.98, 29.66, 20.14, 23.92, 38.61, 36.89, 34.68, 25.83, 29.93)
```


Estimate the average daily yield  $\mu$ using the normal model. 

Use the grid approximation and choose some non-uniform priors.
Plot the marginal posterior for $\mu$.
Compute the 90% credible interval.
What is the probability that the daily milk yield is more than 30 liters?

:::::::::::::::::::::::: solution
Solution
::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::: keypoints 

- Likelihood determines the probability of data conditional on the model parameters
- Prior distribution encodes prior beliefs about the model parameters
- Posterior distribution quantifies the probability of parameter values conditional on the data.
- Posterior is a compromise between the data and prior. The less data available, the bigger the effect of the prior. 
- The grid approximation is a means for accessing posterior distributions which may be difficult to compute analytically
- Marginal posterior is accessed by integrating over nuisance parameters. 


::::::::::::::::::::::::::::::::::::::::::::::::



## Reading 

- Gelman *et al.*, Bayesian Data Analysis (3rd ed.): Ch. 1-3
- McElreath, Statistical Rethinking (2nd ed.): Ch. 1.2



