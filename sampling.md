---
title: 'Working with samples'
teaching: 10
exercises: 2
---





:::::::::::::::::::::::::::::::::::::: questions

- How do you work with posterior samples?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives


- Computing point estimates and posterior intervals based on posterior samples
- Forecasting with the posterior predictive distribution


::::::::::::::::::::::::::::::::::::::::::::::::

In the last episode, we were introduced to the Bayesian formula and fit the binomial and normal models with the grid approximation. However, the poor scalability of the grid approximation makes it impractical to use on models of even moderate size. The standard solution is to Markov chain Monte Carlo (MCMC) methods that draw random samples from the posterior distribution. Later, we will learn about MCMC methods but now we'll learn working with samples. 

## Example: binomial model

Let's revisit the binomial model considered in the previous episode. The binomial model with a beta distribution is an example of a model where the analytical shape of the posterior is known. 

$$p(\theta | X) = Beta(\alpha + x, \beta + N - x),$$
where $\alpha$ and $\beta$ are the hyperparameters and $x$ the number of successes out of $N$ trials. 

::::::::::::::::::::::::::::::::::::::::: challenge

Derive the analytical posterior distribution for the Beta-Binomial model.

::::::::::::::::::::::::::::::: solution


\begin{align}
p(\theta | X) &\propto  p(X | \theta) p(\theta) \\
              &= ... 
\end{align}


::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::


Typically, the analytical posterior is not available and we'll be working with samples. We'll delve into MCMC methods in later episodes but now we can generate posterior samples based on the grid approximation. 



```r
# Sample size
N <- 50

# 7/50 are left-handed
x <- 7

## Grid approximation
delta <- 0.01
theta_grid <- seq(0, 1, by = delta)

likelihood <- dbinom(x = x, size = N, prob = theta_grid)
alpha <- 2
beta <- 4
prior <- dbeta(theta_grid, 2, 4)
posterior_unnormalized <- likelihood * prior
posterior <- posterior_unnormalized / (sum(posterior_unnormalized)*delta)
```

Posterior samples can be generated from the grid approximation as follows. Take a moment to think why this code achieves posterior samples. 


```r
# Number of samples
n_samples <- 5000

theta_samples <- sample(theta_grid, size = n_samples, replace = TRUE, prob = posterior) %>% 
  data.frame(theta = . )
```


Next, let's plot histograms for these samples along with the analytical density, the normalized likelihood, and the "true" value (blue) based on a larger population sample. 


```r
p <- ggplot() +
  geom_histogram(data = theta_samples, 
                 aes(x = theta, y = after_stat(density)),
                 bins = 30, alpha = 0.75, 
                 fill = posterior_color)

# Add analytical distributions
analytical_df <- data.frame(theta = seq(0, 1, by = delta)) %>%
  mutate(posterior_analytical = dbeta(x = theta , alpha + x, beta + N - x)) %>%
  mutate(likelihood = likelihood/(sum(likelihood)*delta)) %>%
  gather(key = "Function", value = "value", -theta)


# Frequency in a large population sample (Hardyck, C. et al., 1976)
theta_true <- 9.6/100

p <- p +
  geom_line(data = analytical_df,
            aes(x = theta, y = value, color = Function),
            linewidth = 1) +
  geom_vline(xintercept = theta_true,
             color = "blue",
             linewidth = 1) +
  scale_color_grafify() +
  scale_fill_grafify()

print(p)
```

<img src="fig/sampling-rendered-unnamed-chunk-4-1.png" style="display: block; margin: auto;" />


<!-- In Episode 1, we summarized the posterior with points estimates, namely the posterior mode (MAP), mean and variance.  -->

The standard way of reporting posterior information is based on *credible intervals* (CI), which refer to areas of the parameter space where a certain amount of posterior mass is located. Usually CIs are computed as quantiles of posterior, so for instance the 95\% CI would be located between the 2.5\% and 97.5\% percentiles. Another approach is to compute the smallest such set that contains 95\% of the posterior, which are also called highest posterior density intervals (HPDI). 

Let us now compute the percentile-based CIs for the handedness example, along with the posterior mode (MAP), and include them in the figure. 

(Figure too busy --> clarify)


```r
# MAP
posterior_density <- density(theta_samples$theta)
MAP <- posterior_density$x[which.max(posterior_density$y)]

# 95% credible interval
CIs <- quantile(theta_samples$theta, probs = c(0.025, 0.975))

p <- p +
  geom_vline(xintercept = CIs, linetype = "dashed") + 
  geom_vline(xintercept = MAP) +
  geom_vline(xintercept = theta_true, color = "blue", size = 1) +
  labs(title = "Black = MAP and CIs")
```

```{.warning}
Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
ℹ Please use `linewidth` instead.
This warning is displayed once every 8 hours.
Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
generated.
```

```r
print(p)
```

<img src="fig/sampling-rendered-unnamed-chunk-5-1.png" style="display: block; margin: auto;" />


Another perspective into processing posterior information is to find the amount the posterior mass in a given interval (or some more general set). This approach enables determining probabilities for hypotheses. For instance, we might be interested in knowing the probability that the target parameter is less than 0.2, between 0.05 and 0.10, or less than 0.1 or greater than 0.20. Such probabilities can be recovered based on samples simply by computing the proportion of samples in these sets. 


```r
theta_less_than_0.15 <- mean(theta_samples$theta < 0.15)
theta_between_0.1_0.2 <- mean(theta_samples$theta >= 0.1 & theta_samples$theta <= 0.2)
theta_outside_0.1_0.2 <- mean(theta_samples$theta < 0.1 | theta_samples$theta > 0.2)
```

Let's visualize these probabilities as proportions of the analytical posterior:

<img src="fig/sampling-rendered-unnamed-chunk-7-1.png" style="display: block; margin: auto;" />




::::::::::::::::::::::::::::::::::::: discussion

How would you compute CIs based on an analytical posterior density?

Can you draw samples from the likelihood?

:::::::::::::::::::::::::::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::: challenge

Write a function that returns the highest posterior density interval. Compute the 95% HPDI for the posterior samples generated (bin_samples) and compare it to the 95% CIs.


:::::::::::::::::::::: hint

If you sort the samples in order, each set of $n$ consecutive samples contains $100 \cdot n/N \%$ of the posterior. 

:::::::::::::::::::::::::::


:::::::::::::::::::::::::::: solution

Solution
:::::::::::::::::::::::::::::::::::::


:::::::::::::::::::::::::::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::: keypoints 

- point 1

::::::::::::::::::::::::::::::::::::::::::::::::

