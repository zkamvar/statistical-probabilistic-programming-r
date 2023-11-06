---
title: 'exercises'
teaching: 10
exercises: 2
---



:::::::::::::::::::::::::::::::::::::: questions 

- How can I get routine in probabilistic programming?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

The purpose of this episode is to provide material for practicing probabilistic programming. The exercises are listed approximately based on the episode they refer to. 

::::::::::::::::::::::::::::::::::::::::::::::::



## Bayesian statistics

### (easy) Explanation
In your own words, explain the following concepts 

1. Posterior distribution
2. Informative prior distribution
3. Grid approximation
4. Effect of prior when different amount of data is available
5. Marginal posterior distribution




### (easy) Formula explanations

For the models described by the following formulas, come up with realistic scenarios where the models could be used. 

1. 
\begin{align}
y_i & \sim Normal(\mu, \sigma) \\
\mu & \sim Normal(0, 1) \\
\sigma & \sim Exponential(1)
\end{align}

2. 
\begin{align}
y_i & \sim Normal(\alpha + \beta x_i, \sigma) \\
\alpha & \sim Normal(0, 10) \\
\beta & \sim Normal(0, 1) \\
\sigma & \sim Exponential(1)
\end{align}

Formulate statistical models for the following scenarios. Give rationalizations for the likelihood and priors. 

1. A batch of 10000 avocados is shipped from South America to North Europe. You'd like to estimate the proportion of the batch that doesn't get bad in transit. 

2. A dart-throwing robot is poorly calibrated. Its throws produce a dense, normally distributed cluster with an approximately identity covariance matrix. However, the throws seems to veer down and left from bull's eye. You want the calibrate the robot based on a set of previous throws.  

Provide examples not found in the course material. 


### (easy) Formula explanations 2

What prior distributions would you use for the following parameters? Justify your choices. 

1. Life expectancy in the developed world
2. The probability of a successful 3 point basketball throw (for yourself)
3. The regression coefficient for milage per liter of gas and car weight
4. The waiting time for a government bureau helpline
5. The effect of national energy drink consumption on yearly rainfall


### (medium) Analyze grid approximation

In Cox regression, the risk for an event (such as the onset of a disease) can be estimated based on follow-up data and background variables at the beginning of the study. Assume the effect of a cancer medicine is studied in control and treatment groups. The data frame below gives the posterior distribution for the effect size $\beta$ of the medicine on survival compared to the control group. What assumptions were made in the analysis that most likely will produce incorrect conclusions about the efficacy of the drug?


```r
df <- data.frame(
  beta = c(-0.25, -0.219, -0.188, -0.156, -0.125, -0.094, -0.062, -0.031, 0, 0.031, 0.062, 0.094, 0.125, 0.156, 0.188, 0.219, 0.25, 0.281, 0.312, 0.344, 0.375, 0.406, 0.438, 0.469, 0.5),
  posterior = c(1.052, 1.478, 2.025, 2.707, 3.533, 4.498, 5.59, 6.779, 8.023, 9.266, 10.444, 11.487, 12.33, 12.915, 13.202, 13.17, 12.821, 12.18, 11.293, 10.217, 9.021, 7.773, 6.536, 5.363, 4.295)
)
```


### (medium) Grid approximation for the linear model




On the planet Neptunus, a ball is dropped from $h=8$ meters $N=25$ times and the falling time $t$ is measured for each drop. From previous experiments, it is known that the measurement error is normally distributed with standard deviation of 0.2. The measurement times are as follows. 


```r
Time <- c(1.28, 1.18, 1.37, 1.17, 0.98, 1.02,
          1.16, 1.19, 1.55, 1.13, 1.57, 1.26,
          1.39, 1.1, 1.4, 0.92, 1.15, 1.19, 1.25,
          1.01, 0.82, 1.46, 1.06, 1.38, 1.24)
```


According to Newton's theory of gravity, $h$ and $t$ are connected via the relation of $h = \frac{1}{2}gt^2$, where $g$ is the gravitational constant. 

Implement the grid approximation for standard linear regression and estimate $g$. Give the posterior mode and 90% CIs. Use some reasonable prior distribution. 


#### Hint
Solve formula $h = \frac{1}{2}gt^2$ for $t$ so the model error measures time measurement error. Further, do a parameter transformation so the formula doesn't contain squares of variables.  

## Working with samples

### (easy) Sampling the posterior

Assume we model the following observations $X$ with the exponential likelihood:


```r
X <- c(0.166, 1.08, 1.875, 0.413, 1.369, 0.463, 0.735,
       0.24, 0.774, 1.09, 0.463, 0.916, 0.225, 0.889,
       0.051, 0.688, 0.119, 0.078, 1.624, 0.553, 0.523,
       0.644, 0.284, 1.744, 1.468)
```

If we add a $\Gamma(2, 1)$ prior, the posterior distribution can be shown to be 

$$p(\lambda | X) = \Gamma(2 + n, 1 + \sum_{i}^{n}X_i),$$
where $n$ is the number of observations. 

Produce 1000 samples from the posterior and compute
1. posterior mean and mode
2. 50%, 90% and 95% posterior intervals
3. the probabilities that $\lambda > 1$, $\lambda < 1.5$, and $1<\lambda<1.5$ 


### (easy) Marginal posterior

Suppose the following piece of code produces samples from a (bivariate) posterior for $\theta = (\theta_1, \theta_2).$


```r
posterior_samples <- mvtnorm::rmvnorm(1000,
                                      mean = c(1, 0),
                                      sigma = matrix(c(1, 0.65, 0.65, 2), 2, 2))

# Make into a data frame
posterior_samples <- data.frame(posterior_samples) %>%
  set_colnames(c("theta1", "theta2"))
```

Produce marginal posterior samples for $\theta_1$ and $\theta_2$. Plot the full and marginal posteriors. What information is lost if you only look at the marginal posteriors?


### (medium) Posterior of difference

Consider the posterior distribution of the previous exercise. What is the probability that $\theta_1$ is larger than $\theta_2$?

### (medium) Sample size estimation

Suppose we aim to estimate p, the proportion of left-handed people very precisely (assume true p = 0.094). Specifically, we want the 99% percentile interval of the posterior distribution of p to be only 0.05 wide. Approximately how large of a study sample do we need? Simulate, no need to do analytical calculations. Use a prior of your liking.


### (medium) Less data means bigger prior effect

Explore the effec of sample size on the location and variance of the posterior. 

Instructions: 
  - Set p ~ (0, 1) and generate data from the binomial model. Simulate a sequence of 200 throws. 
  - Draw samples form the the analytical posterior using the first 5, 10, 15,..., 200 throws
  - Compute the posterior mode and 90% CIs for each fit
  - Visualize the results by generating a figure with: 
    - the mode and CIs as the function of data size
    - true parameter value 
    - prior mode and CIs.
      - Check formulas e.g. from Wikipedia
    - true analytical posterior mode and CIs using all 200 throws
      
  
### (hard) Highest posterior density set

Assume the following piece of code generates 5000 samples from a posterior distribution.


```r
posterior_samples <- c(rnorm(2500, -1, 0.5), rnorm(2500, 1, 0.75))
```

Compute the 50% highest posterior density set (not necessarily an interval!) of this distribution. Plot the set over a histogram of the samples. 

#### Hint

- Generate the posterior density from the samples with the `density` function. 
- Starting from the highest density, scan the density values in a decreasing order. Determine the mass of the posterior corresponding to the posterior above the particular density values. What does this set represent?


## Stan

### (easy) Analyze Stan programs 

Write the statistical models implemented in the following Stan programs and give some explanation on what they model. 

1. 

```stan

data{
  int<lower=0> N;
  vector[N] X;
}

parameters {
  real mu;
  real<lower=0> tau;
}

model {
  X ~ normal(mu, 1/tau);
  
  mu ~ normal(0, 10);
  tau ~ Gamma(2, 1);
}

```


2. 

```stan

data{
  int<lower=0> N;
  vector[N] X;
}

parameters {
  vector[3] phi;
  real<lower=0> sigma;
}

model {

  for(i in 3:N) {
    X[i] ~ normal(phi[1] + phi[2]*X[i-1] + phi[3]*X[i-2], sigma);
    }

  phi ~ normal(0, 1);
}

```

3. 

```stan

data{
  int<lower=0> n_groups;
  vector[n_groups] X;
  int<lower=0> N;
}

parameters {
  vector[n_groups] theta;
}

model {

  for(g in n_groups) {
    X[g] ~ binomial(N, theta[g]);
  }
  
  theta ~ beta(alpha, beta);
  
  alpha ~ gamma(2, 0.1); 
  beta ~ gamma(2, 0.1);

}

```


### (easy) Complete Stan program

Fill the following Stan program for logistic regression


```r
logistic_model <- "
  data {
    vector x;
    int<lower=0,upper=1> y;
  }
  parameters {
    alpha;
    beta;
  }
  model {
    y ~ bernoulli_logit(alpha + beta * x);
    
    alpha ~ ;
    beta ~ ;
  }
"
```

### (medium) Estimate dice fairness

Estimate the fairness of a 6 sided dice, that is, the probabilities of getting each face on a random roll. The results from 99 rolls is stored in the vector `rolls`.


```r
rolls <- c(3, 2, 6, 3, 6, 2, 5, 6, 5, 6, 4, 1, 4,
           2, 5, 4, 6, 6, 5, 4, 1, 3, 3, 4, 2, 3,
           4, 4, 4, 1, 1, 3, 4, 4, 1, 6, 4, 6, 5,
           5, 2, 6, 1, 1, 4, 4, 1, 6, 6, 1, 6, 4,
           5, 5, 3, 4, 2, 6, 6, 5, 2, 6, 1, 1, 4,
           4, 4, 6, 3, 5, 3, 6, 5, 3, 3, 2, 3, 3,
           5, 3, 3, 4, 6, 4, 3, 6, 6, 4, 4, 6, 5,
           1, 3, 5, 1, 2, 4, 4, 1)
```

Write a Stan program that implements the following statistical model: 

\begin{align}
y & \sim categorial(\theta) \\
\theta & \sim Dir(\textbf{1}),
\end{align}

Where $Dir$ is the Dirichlet distribution with parameter $\alpha = 1, 1, \ldots, 1.$ Plot the marginal posteriors for each $\theta_i$. Is the dice fair? Quantify this: give some probability for the hypothesis "the dice is fair" (for example, compute the probability of for $\theta_i > \theta_j$ for some $i$ and $j$). 

### (medium) Basketbass throws

Implement Beta regression in Stan that assumes data is generated from a Beta(a, b) where a, b unknown parameters.

Use the program to analyze basketball free throw accuracy in the NBA based on a sample of $N=20$ players. Generate the posterior predictive distribution and compare to the full data. 



```r
## Data

# Data from https://www.kaggle.com/datasets/nicklauskim/nba-per-game-stats-201920?resource=download&select=nba_2020_advanced.csv

df <- read_csv("data/basketball.csv")
```

```{.output}
Rows: 651 Columns: 26
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
chr  (3): Player, Pos, Tm
dbl (23): Age, G, MP, PER, TS%, 3PAr, FTr, ORB%, DRB%, TRB%, AST%, STL%, BLK...

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
N <- 20

# remove NAS and 0, 1
df <- df %>% 
  filter(!is.na(FTr)) %>% 
  filter(FTr > 0 & FTr < 1)


# Choose random sample
df_sample <- df[sample(1:nrow(df), N, replace = FALSE), ]
```





### (medium) Normal mixture


The following Stan program `normal_mix` (from [M. Betancourt's blog](https://betanalpha.github.io/assets/case_studies/identifying_mixture_models.html#43_breaking_the_labeling_degeneracy_by_enforcing_an_ordering)) implements a one-dimensional normal mixture model with 2 components. 


```stan
data {
 int<lower = 0> N;
 vector[N] y;
}

parameters {
  ordered[2] mu;
  real<lower=0> sigma[2];
  real<lower=0, upper=1> theta;
}

model {
 sigma ~ normal(0, 2);
 mu ~ normal(0, 2);
 theta ~ beta(5, 5);
 for (n in 1:N)
   target += log_mix(theta,
                     normal_lpdf(y[n] | mu[1], sigma[1]),
                     normal_lpdf(y[n] | mu[2], sigma[2]));
}
```

a) Explain the Stan program: what does the ordered type mean? What does the `target +=` notation mean?
b) Modify the program so that is generates a posterior predictive distribution. 
c) Fit the model to the provided data and plot the predictive distribution and overlay the data. 

Data: 

<!-- Generated with -->





```r
X <- c(1.53, 1.16, 3.39, 1.17, 2.19, -1.72, 0.97, 1.08, 2.17, 1.36, 0.99, -0.08, -1.96, -1.06, 1.85, 2.8, 0.71, -0.56, 2.73, 3.38, -1.22, 1.22, -1.71, 0.02, 2.24, -1.57, -0.67, -0.77, 1.07, 1.21, 0.77, 1.08, 0.58, 2.33, 0.2, -1.47, 0, -0.34, 0.98, -0.66, 0.26, -0.44, -1.11, -1.66, 1.29, 0.39, 1.14, -0.96, -0.04, 0.8, 2.47, -0.32, 1.72, 1.24, -0.42, 3.36, 1.79, -0.2, -1.19, 2.01, -1.26, -0.61, 1.64, 1.76, -1.65, -0.54, 1.05, 1.39, -0.73, -1.67, -0.6, -1.15, 1.05, 2.47, -0.45, 2.93, -1.82, 1.75, -1.55, -1.63, 1.51, -1.79, -0.84, 2.11, -0.69, 1.55, -0.15, -1.55, -1.23, 1.62, 2.38, 1.21, 2.37, -0.77, -0.67, -1.52, 2.78, -0.93, 0.91, 1.13)
```

### (hard) Write Stan program: OUP

The Ornstein-Uhlenbeck process (OUP) can be used (among other things) as a part in algorithmic trading strategies. The model is a stochastic differential equation defined as

$$dX_t = \phi(\mu - X_t) + \sigma dW,$$
where $X_t$ is the state variable at time $t$, $\mu$ is the mean level, $\phi$ the mean-reversion rate, and $\sigma$ determines the level of stochastic variations of the process. $dW$ is a so-called Wiener process, of which you can read more about elsewhere. The parameter $\phi$ determines how quickly the process returns towards $\mu$ if the system is driven away from $\mu.$ 

The nice thing about the OUP is that it is analytically tractable, meaning that the transition density between different time points is known. In other words, $p(X_{t + dt} | X_t) = N(\mu - (\mu - X_t)e^{-\lambda t}, \frac{\sigma^2}{2\phi}(1-e^{-2\phi t})).$ This allows writing the likelihood for the data!

Implement the OUP in Stan and fit it on the data provided below. The data is the daily stock price of Exxon Mobil over the past 2 years (minus the 20 day exponential moving average, provided by Yahoo). Plot the posteriors of the model parameters. How could you use the analysis to make trading decisions (this is not an investment recommendation)?


```r
exxon <- c(-6.71, -8.31, -5.79, -7.72, -8.37, -5.16, -4.55, -4.51, -5.11, -4.32, -5.45, -3.66, -1.9, 0.24, 1.1, -0.35, -0.93, 1.79, 1.3, 2.93, 3.62, 7.16, 4.26, 3.48, 0.39, -3.11, -1.68, -1.06, 0.52, 1.25, 2.71, 3.18, 1.36, 0.45, 1.08, 2.95, 2.39, 2.1, 5.51, 5.51, 5.45, 3.83, 5.5, 1.53, 0.73, -0.9, 0.75, 0.1, -0.65, 0.11, 1.54, 2.4, 0.11, 2.21, -0.57, -1.98, -1.8, -2.32, -3.44, -3.46, -7.49, -8.38, -5.99, -2.6, -2.51, -3.51, 0.99, 3.93, 7.04, 9.03, 7.24, 4.57, 3.37, 3.44, 6.22, 3.21, 4.2, 3.96, 6.29, 5.81, 7.01, 7.01, 5.69, 6.29, 6.06, 8.33, 7.64, 7.91, 5.07, 5.94, 6.47, 7.05, 6.71, 1.46, 2.76, 5.62, 4.56, 4.82, 3.25, 3.08, 1.9, 0.71, 3.55, 2.7, 2.08, -1.2, -0.42, 0.34, -0.18, -1.01, -3.64, -5.98, -5.62, -4.39, -4.77, -2, -0.76, -1.41, -2.19, -2.66, -1.98, -0.41, 0.87, -1.19, 1.46, 2.68, 0.79, 1.46, 2.31, -1.34, -0.93, 1.32, 2.39, 0.3, 1.74, 2.73, 4.14, 3.69, 3.14, 0.74, 1.31, 3.02, 2.2, 2.94, 2.12, 6.03, 3.51, 1.32, 3.41, 1.94, -1.49, -0.65, -0.76, 2.2, 1.08, 1.37, 5.6, 3.83, 2.21, 1.69, 1.22, -2.92, -2.75, -3.79, -2.51, -2.26, -2.23, -2.6, -1.46, -0.86, 0.5, 1.35, -0.77, -2.17, -2.73, -3.69, -4.46, -3.68, -8.14, -7.9, -8.22, -5.08, -0.44, -2.62, -3.43, -3, -0.66, 0.6, 2.21, 2.48, 2.39, 8.02, 6.25, 7.44, 4.97, 4.04, 4.39, 3.86, 3.98, 3.86, 2.27, 4.08, 3.35, 2.19, 2.32, 4.08, 2.17, 1, 2.15, 3.31, -0.32, -4.43, -5.97, -7.11, -4.05, -3.27, -2.93, -3.92, -5.31, -4.81, -5, -6.83, -4.1, -2.85, -2.13, -3.1, -1.51, -0.29, -2.01, -2.44, -3.05)
```



### (hard) Implement logistic growth

In ecology, evolution of population size is often modeled using logistic growth which is defined by the differential equation 

$$\frac{dP}{dt} = rP(1-\frac{P}{K}),$$
where $P$ is the population size, $t$ time, $r$ the growth rate. The carrying capacity $K$ determines the maximum size of the population, and without it the population would grow exponentially. The solution to this differential equation is 

$$P(t) = \frac{K}{1 + (\frac{K}{P_0} - 1)e^{-rt}}.$$ 
In other words, the solution gives the population size at time $t$, given the initial size $P_0$ and the model parameters. 

The following data contains (noisy and scaled) measurements of an **E. coli** bacteria culture measured for 1 day (Sprouffske, Wagner, Growthcurver (2016): an R package for obtaining interpretable metrics from microbial growth curves (2016)). 


```r
E.coli <- data.frame(
  time = c(0, 0.167, 0.333, 0.5, 0.667, 0.833, 1, 1.167, 1.333, 1.5, 1.667, 1.833, 2, 2.167, 2.333, 2.5, 2.667, 2.833, 3, 3.167, 3.333, 3.5, 3.667, 3.833, 4, 4.167, 4.333, 4.5, 4.667, 4.833, 5, 5.167, 5.333, 5.5, 5.667, 5.833, 6, 6.167, 6.333, 6.5, 6.667, 6.833, 7, 7.167, 7.333, 7.5, 7.667, 7.833, 8, 8.167, 8.333, 8.5, 8.667, 8.833, 9, 9.167, 9.333, 9.5, 9.667, 9.833, 10, 10.167, 10.333, 10.5, 10.667, 10.833, 11, 11.167, 11.333, 11.5, 11.667, 11.833, 12, 12.167, 12.333, 12.5, 12.667, 12.833, 13, 13.167, 13.333, 13.5, 13.667, 13.833, 14, 14.167, 14.333, 14.5, 14.667, 14.833, 15, 15.167, 15.333, 15.5, 15.667, 15.833, 16, 16.167, 16.333, 16.5, 16.667, 16.833, 17, 17.167, 17.333, 17.5, 17.667, 17.833, 18, 18.167, 18.333, 18.5, 18.667, 18.833, 19, 19.167, 19.333, 19.5, 19.667, 19.833, 20, 20.167, 20.333, 20.5, 20.667, 20.833, 21, 21.167, 21.333, 21.5, 21.667, 21.833, 22, 22.167, 22.333, 22.5, 22.667, 22.833, 23, 23.167, 23.333, 23.5, 23.667, 23.833, 24), 
  abundance = c(0.01, 0.005, 0.013, 0.008, 0.002, 0.01, 0.006, 0.006, 0.003, 0.009, 0.01, 0.006, 0.011, 0, 0.001, 0.006, 0.007, 0.004, 0.006, 0.005, 0.003, 0.008, 0.006, 0.011, 0.008, 0.015, 0.008, 0.006, 0.013, 0.01, 0.013, 0.016, 0.018, 0.013, 0.015, 0.013, 0.019, 0.021, 0.024, 0.03, 0.03, 0.036, 0.04, 0.045, 0.059, 0.064, 0.072, 0.083, 0.093, 0.111, 0.123, 0.137, 0.154, 0.176, 0.183, 0.201, 0.218, 0.231, 0.248, 0.261, 0.267, 0.287, 0.288, 0.293, 0.299, 0.306, 0.309, 0.314, 0.321, 0.324, 0.326, 0.331, 0.328, 0.33, 0.325, 0.337, 0.331, 0.337, 0.333, 0.335, 0.338, 0.335, 0.332, 0.332, 0.333, 0.332, 0.332, 0.337, 0.336, 0.335, 0.33, 0.34, 0.333, 0.332, 0.337, 0.337, 0.336, 0.336, 0.336, 0.335, 0.334, 0.339, 0.335, 0.336, 0.337, 0.335, 0.332, 0.333, 0.334, 0.337, 0.334, 0.33, 0.337, 0.333, 0.339, 0.335, 0.329, 0.332, 0.338, 0.333, 0.329, 0.334, 0.338, 0.344, 0.335, 0.334, 0.337, 0.337, 0.334, 0.334, 0.331, 0.34, 0.336, 0.34, 0.34, 0.336, 0.337, 0.34, 0.337, 0.333, 0.328, 0.339, 0.334, 0.332, 0.336))
```

Write a Stan program for the logistic growth model and estimate the growth rate and carrying capacity for this **E. coli** strain. Plot the marginal posteriors for $P_0$, $r$ and $K.$ Generate a posterior of growth trajectories and plot it against the data. 






## MCMC 

### (medium) Gibbs sampler

Consider the distribution p(x, y) = exp{-(x^2y^2 + x^2 + y^2 -8x -8y)/2}/C, where C is a normalizing constant. It is known that the conditional distribution of x given y is the normal distribution p(x|y) = N(mu, sigma), where the mean mu = 4/(1 + y^2) and the standard deviation sigma = sqrt(1/(1 + y^2)). Due to symmetry, p(y|x) can be recovered simply by changing y's to x's in p(x|y). 
 
The Gibbs sampler is a special case of the Metropolis-Hastings algorithm. It can be stated as follows:
 1) Choose some initial values for the parameters. Let's call them x_0 and y_0 in our case. 
 2) The following samples are generated as follows:
  For i = 1, ..., N:
    a) Draw x_i from p(x|y_{i-1})
    b) Draw y_i from p(y|x_i)

(In this notation x_i refers to the x sample at step i, y_{i-1} to the y sample at step i-1 and so on)
 
Build a Gibbs sampler that draws samples from p(x, y). Visualize the resulting distribution. 



### (hard) Metropolis-Hastings: Normal

a) Generate data from the normal distribution. Implement a Metropolis sampler for the normal model that estimates the mean and variance. Use a normal distribution to generate the proposals.

b) Run 4 chains with distinct (e.g. random) initial values and plot the chain's trajectories in parameter space along with trace plots. How does the proposal distribution's variance affect the chains and the resulting posterior samples? 

#### Hint
See https://www.statisticshowto.com/metropolis-hastings-algorithm/ for a description of Metropolis
See Statistical Rethinking: Overthinking box p.45 for a binomial example. 
Work with logarithms to avoid underflow
Think of what do to with negative proposals for the variance parameter

### (hard) Rhat

Implement the $\hat{R}$ diagnostic and compute it for posterior samples generated from some model (e.g. the normal model of the previous exercise). Definition of the diagnostic can be found in BDA3 p. 285. 


## Hierarchical models 

### (easy) Analyze models

Are the following statistical models hierarchical? If not, make modifications that turn them into hierarchical. 

1. 

\begin{align}
  y_{group, i} & \sim Normal(a_{group} + b x_{group, i}, \sigma) \\
  a_{group} & \sim Normal(0, 1) \\
  b & \sim Normal(0, 1) \\
  \sigma & \sim Exponential(1)
\end{align}

2. 

\begin{align}
  y_i &\sim Binomial(1, p_i) \\
  logit(p_i) &= a_{group, i} + b x_i \\
  a_{group} &\sim N(\alpha, 1) \\
  \alpha &\sim N(0, 100) \\
  b & \sim Normal(0, 1)
\end{align}

3. 

\begin{align}
  X_i & \sim \Gamma(\alpha, \beta) \\
  \alpha &\sim \Gamma(1, 1) \\
  \beta &\sim \Gamma(1, 1) \\
\end{align}



### (medium, laborous) Hierarchical coin analysis

Estimate the fairness of a collection of coins. Fit pooled, unpooled and partially pooled binomial models to the provided data. Generate a figure that compares the posteriors. Plot also the population distribution. What information does the population distribution give?

Generate data

```r
## Data
G <- 15
N <- rep(25, G)
p_true <- rbeta(G, 50, 55) %>% sort
x <- rbinom(n = G, size = N, prob = p_true)
```


## Model critisism

### (easy) Explanation

Explain in your own words:
a) the purpose of information criteria
b) log pointwise predictive distribution (lppd)
c) what is the fundamental difference between DIC/AIC and WAIC?



### (medium) Posterior predictive check: normal 1 

Build a Stan model that estimates the mean and standard deviation of a normal distribution. Fit the model to the data in data1.txt and do a posterior predictive check (for example a visual one). What are your conclusions about the model's suitability on this data based on the PPD? What steps would you take next?

Hint: you can generate the PPD in R/Python or in the generated quantities block in Stan

### (medium) Posterior/prior predictive check

Consider linear regression with one dependent variable implemented in the Stan program:


```stan

data {
  int<lower=0> N; // Sample size
  vector[N] x; // x-values
  vector[N] y; // y-values
}
parameters {
  real alpha; // intercept
  real beta;  // slope
  real<lower=0> sigma; // noise
}

model {
  
  // Likelihood
  y ~ normal(alpha + beta * x, sigma);
  
  // Priors
  alpha ~ normal(0, 1);
  beta ~ normal(0, 1);
  sigma ~ gamma(2, 1);
}

```


and the following data:


```r
x <- c(-1.61, 1.55, -0.14, 0.27, -1.64, 0.61, -0.17, 1.56, -0.96, 1.33, 1.72, 1.76, 1.49, -1.55, 0.69, -1.39, 1.28, 0.63, -0.96, 0.13, -1.33, 1.07, 1.01, -1.94, 1.2, 0.36, 1.38, 0.08, 1.47, 0.32)
y <- c(-1.84, 2.06, 0.41, 1.09, -2.3, 1.22, 2.02, 4.03, 0.52, 2.76, 3.66, 0.43, 4.62, -0.03, 0.16, -0.1, 2.82, 1.63, -0.35, 0.89, 0.29, 0.78, 1.9, 0.26, 0.84, 1.06, 2.93, 3.38, 2.44, 2.48)

beta <- 0.75
alpha <- 1
sigma <- 1
```


Use Stan to do the following: 

1. Posterior predictive check (for same data):

a) Simulate output data (response variable y) from the posterior for the same input data (predictor variable x).
b) Compare the simulated and original responses that were used for model inference. There are many possible ways to compare original and simulated y, propose your own solution.

Tip: Stan manual page on posterior predictive distribution: https://mc-stan.org/docs/stan-users-guide/simulating-from-the-posterior-predictive-distribution.html


2. Posterior predictions (for new data):
a) Simulate data from the posterior of the linear model for new (different) input data (x_new).
b) Compare the simulated and original responses that were used for model inference. There are many possible ways to compare original and simulated y, propose your own solution.

3. Prior predictive checks:
a) Generate samples (for y) from the prior predictive distribution.
b) Compare prior and posterior simulations for y; try both informative and uninformative priors; is there any difference?

Tip: note that this can be implented in Stan but you do not necessarily need Stan to draw samples from the prior distribution.


### (hard) Posterior predictive check: normal 2

Consider the following data: 

<!-- Data generated with: -->



```r
X <- c(-0.05, 0.07, 0.14, 0.17, 0.25, 0.33, 0.35, 0.32, 0.41, 0.45, 0.43, 0.47, 0.48, 0.54, 0.68, 0.54, 0.45, 0.43, 0.43, 0.41, 0.29, 0.3, 0.29, 0.3, 0.28, 0.5, 0.44, 0.3, 0.21, 0.19, 0.11, 0.19, 0.34, 0.64, 0.55, 0.46, 0.38, 0.45, 0.33, 0.38, 0.2, 0.25, 0.28, 0.24, 0.25, 0.25, 0.23, 0.37, 0.21, 0.5, 0.36, 0.57, 0.56, 0.7, 0.61, 0.69, 0.5, 0.5, 0.44, 0.37, 0.42, 0.52, 0.47, 0.59, 0.65, 0.59, 0.54, 0.51, 0.58, 0.48, 0.42, 0.39, 0.57, 0.63, 0.51, 0.62, 0.54, 0.66, 0.84, 0.93, 0.85, 0.8, 0.88, 1.01, 1.16, 1.23, 1.14, 1.14, 1.02, 1.1, 1.01, 1.08, 0.91, 0.98, 1.02, 1.12, 1.19, 1.26, 1.34, 1.31)
```

Use a normal model and do a posterior predictive check to see how good of a choice the normal model is for the data. Use some statistic to do the PPD and compute the Bayesian P-value. 


#### Hint

Plot $X$ in sequential order. Do you see any structure? Does the normal model produce such structure?


### (hard) WAIC-based model selection 

Let us consider the polynomial regression models with 0 (intercept only), 1 (linear regression), 2 (quadratic), 3 (cubic) degrees. Which of these four models best describes the provided data in terms of the WAIC?

Use the provided Stan models and implement the WAIC in R/Python.

Hint: See Statistical Rethinking p.220 for definition of WAIC and Overthinking box on p.210. Can also be found in p. 173 in BDA3.

Data: 


```r
x <- c(-0.54, 0.72, 1.12, 0.57, 0.61, 0.89, -0.45, 0.66, 1.19, -1.26, -1.4, 0.28, -0.42, -1.24, 0.45, 0.6, -0.92, -1.42, 0.81, -0.41, -1.22, 1.31, -0.38, -1.22, -1.02)

y <- c(0.53, 2.15, 3.23, 3.48, 5.08, 4.07, 2.23, 3.06, 4.78, 1.35, 1.24, 2.87, 1.22, -0.48, 3.48, 4.03, -0.05, -0.19, 2.31, 0.96, 1.14, 6.85, -1.1, -1.32, -0.83)

df <- data.frame(x, y)
```


Stan programs: 


```stan
// intercept only
data{
  int N;
  vector[N] x;
  vector[N] y;
}

parameters{
  real a;
  real<lower=0> sigma;
}

model{
  
  // Likelihood
  y ~ normal(a, sigma);
  
  // Priors
  a ~ normal(0, 1);
  sigma ~ gamma(2, 1);
}
```


```stan
// linear
data{
  int N;
  vector[N] x;
  vector[N] y;
}

parameters{
  real a;
  real b;
  real<lower=0> sigma;
}

model{
  
  // Likelihood
  y ~ normal(a + b*x, sigma);
  
  // Priors
  a ~ normal(0, 1);
  b ~ normal(0, 1);
  sigma ~ gamma(2, 1);
}

```


```stan
// quadratic

data{
  int N;
  vector[N] x;
  vector[N] y;
}

transformed data {
  vector[N] x_sq;
  for(i in 1:N) x_sq[i] = x[i]*x[i];
}

parameters{
  real a;
  real b;
  real c;
  real<lower=0> sigma;
}

model{
  
  // Likelihood
  y ~ normal(a + b*x + c*x_sq, sigma);
  
  // Priors
  a ~ normal(0, 1);
  b ~ normal(0, 1);
  c ~ normal(0, 1);
  sigma ~ gamma(2, 1);
}
```


```stan
// cubic
data{
  int N;
  vector[N] x;
  vector[N] y;
}

transformed data {
  vector[N] x_sq;
  vector[N] x_cu;
  for(i in 1:N) x_sq[i] = x[i]*x[i];
  for(i in 1:N) x_cu[i] = x_sq[i]*x[i];
}

parameters{
  real a;
  real b;
  real c;
  real d;
  real<lower=0> sigma;
}

model{
  
  // Likelihood
  y ~ normal(a + b*x + c*x_sq + d*x_cu, sigma);
  
  // Priors
  a ~ normal(0, 1);
  b ~ normal(0, 1);
  c ~ normal(0, 1);
  sigma ~ gamma(2, 1);
}
```

## Other topics


### (hard) Missing observations: Brownian bridge


A time series is of length $N=200$ is assumed to have been generated by the random walk process

$$x_t \sim N(x_{t-1}, \sigma^2).$$
However, the observations at time points $T=76, \ldots , 125$ have been lost in a fire.

Write a Stan program that estimates these missing values along with the process variance $\sigma^2.$ 

Plot the time series and posterior draws for the missing values in the same figure. Color the missing observations (that were not available in the inference) in a different color.


Data can be simulated with the following piece of code: 

```r
# Brownian bridge: random walk the a portion in missing values 

# Generate data
N_obs_pre <- 75
N_mis <- 50
N_obs_post <- 75

sigma <- 0.5

y_full <- cumsum(rnorm(N_obs_pre + N_mis + N_obs_post, 0, sigma))
y <- y_full
y[(N_obs_pre + 1):(N_obs_pre + N_mis)] <- NA
```




::::::::::::::::::::::::::::::::::::: keypoints 

- Point

::::::::::::::::::::::::::::::::::::::::::::::::



### (hard) Gaussian process classification 

Let $z_i \in \{0, 1\}$ be a set of $N$ binary measurements and $x_i$ the corresponding covariate values. The observations are given and plotted here: 


<!-- Data generated with the following piece of code.  -->




```r
x <- c(1.55, -2.1, -2.27, -2.11, -2.38, 1.34, 2.09, 0.04, -1.34, 0.7, 0.13, -4.23, 0.05, -0.99, -1.05, -0.76, 0.38, -0.22, 3.24, -5.67, -0.15, -2.19, -1.8, 3.32, -0.6, -2.22, -2.87, -1.57, 0.14, -0.96, 0.7, 2.08, -0.77, 0.44, -1.33, 1.13, -3.48, -2.42, -0.48, -0.28, -0.35, -0.8, 0.72, 1.11, -4.35, 1.33, 1.41, 2.78, -1.68, -0.73, 0.19, -2.48, -3.45, -0.46, 3.38, 0.22, 0.03, -0.89, -1.77, 0, -1.02, 4.47, 1.03, 0.28, -2.02, -2.53, -3.33, -0.54, 1.76, -1.84, 5.01, 2.96, 0.09, -0.84, -0.36, 1.03, -0.24, -1.23, 0.14, 1.06, -0.75, -1.6, -2.15, 0.83, -1.41, 1.48, 0.26, -1.79, -0.38, 2.06, 1.17, 0.91, 3.31, -1.85, 3.37, 0.28, 0.84, -0.22, -0.82, -0.65)

z <- c(0, 0, 0, 1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 0, 0, 1, 0, 0, 0, 1, 0, 0, 1, 1, 1, 1, 0, 1, 1, 1, 1, 0, 0, 1, 1, 1, 1, 1, 1, 0, 1, 0, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 1, 1, 1, 1, 0, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 1, 1, 0, 1, 0, 1, 1, 1, 0, 0, 1, 0, 0, 1, 0, 1, 1, 0, 1, 0, 0, 0, 1, 1, 1, 1, 1)

df <- data.frame(x, z)

p <- ggplot(data = df) +
  geom_point(aes(x = x, y = z))

print(p)
```

<img src="fig/exercises-rendered-unnamed-chunk-30-1.png" style="display: block; margin: auto;" />

Implement a Gaussian process based binary classifier in Stan that works as follows: 

1. $z_i$ is generated as $z_i \sim \text{Bernoulli}(\text{logit}(f(x_i))),$ where $f$ is an unknown function of $x$.
2. $f$ has a Gaussian process prior, $f \sim GP(\mu, \Sigma).$
3. $y = \text{logit}(f) = \frac{1}{1 + \exp(-f)}$ is the probability of class 1. 

Plot the posterior for the class probability $y$ as a function of x.    

BONUS: why does the posterior seem to curve upward at the end of the available data range? How could you correct this behavior?    
