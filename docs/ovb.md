
# Omitted Variable Bias

## Prerequisites

This chapter uses the [car](https://www.rdocumentation.org/packages/car/topics/Duncan) dataset in the **[car](https://cran.r-project.org/package=car)** package.

```r
library("car")
library("tidyverse")
```

## Simpson's Paradox

**UC Berkeley Graduate Admissions** 
@BickelHammelOConnell1975a analyze a claim of sex bias in graduate admissions against UC-Berkeley in the 1970s.
In 1973, 8,442 men and 4,321 women applied for admission to Berkeley graduate programs.
In aggregate, UC Berkeley admitted 44% of men and 35% of women applicants, seemingly supporting that claim.
However, when the admissions rates were disaggregated by graduate department, the acceptance rates by department were not, on average, different.
What is going on?
On average, more women applied to more selective (higher rejection rate) departments than men. 

The dataset [datasets](https://www.rdocumentation.org/packages/datasets/topics/UCBAdmissions) in the **[datasets](https://cran.r-project.org/package=datasets)** package
contains data for the largest 6 programs.

```r
data("UCBAdmissions", package = "datasets")
admissions <- as_tibble(UCBAdmissions) %>%
  spread(Admit, n) %>%
  mutate(applicants = Admitted + Rejected,
         accepted = Admitted / applicants)

ggplot(admissions, aes(x = Dept, y = accepted, size = applicants, colour = Gender)) +
  geom_point()
```

<img src="ovb_files/figure-html/unnamed-chunk-3-1.svg" width="672" />

```r
select(admissions, Dept, Gender, applicants, accepted) %>%
  arrange(Dept, Gender)
```

```
## # A tibble: 12 × 4
##     Dept Gender applicants   accepted
##    <chr>  <chr>      <dbl>      <dbl>
## 1      A Female        108 0.82407407
## 2      A   Male        825 0.62060606
## 3      B Female         25 0.68000000
## 4      B   Male        560 0.63035714
## 5      C Female        593 0.34064081
## 6      C   Male        325 0.36923077
## 7      D Female        375 0.34933333
## 8      D   Male        417 0.33093525
## 9      E Female        393 0.23918575
## 10     E   Male        191 0.27748691
## 11     F Female        341 0.07038123
## 12     F   Male        373 0.05898123
```


An interactive visualization is this UC Berkeley VUD Lab visualization:  [impson's Paradox](http://vudlab.com/simpsons/).
**Stop. Go to that link. Explore that visualization, and build your intution**

Simpson's Paradox is an example of a more general phenomena in regression:
omitted variable bias due to confounding variables.[^simpson1]
In the UC-Berkeley case, the confounding variable was the department.


[^simpson1]: However, omitted variable bias only makes sense as a concept when the
  regression has a causal or structural interpretation. Simpson's paradox is
  an aggregation affect that can occur with conditioning even when there is 
  no causal interpretation of the model.
  
For some other examples see @Moore2005a, @Wagner1982a, [Wikipedia](https://en.wikipedia.org/wiki/Simpson%27s_paradox), @JuliousMullee1994a (Kidney stone treatment).


## Omitted Variable Bias

Suppose that the population model is,
$$
Y_i = \beta_0 + \beta_1 X_i + \beta_2 Z_i + \epsilon_i ,
$$
but given a sample, we run a regression with only $\vec{x}$ and not $\vec{z}$.
$$
y_i = \hat{\beta}_0 + \hat{\beta}_1 x_i + \hat{\epsilon}_i .
$$

What is the relationship between $\beta_1$ and $\hat{\beta}_1$?
Is $\hat{\beta}_1$ an unbiased estimator of $\beta_1$ ? 



$$
\text{omitted variable bias} =  (\text{effect of $Z_i$ on $Y_i$}) \times (\text{effect of $X_i$ on $Z_i$})
$$


What does the omitted variable bias 
An *irrelevant variable* is one that is uncorrelated with $Y_i$, meaning that its 
population coefficient is 0.
Suppose $Z_i$ is an irrelevant variable,
$$
Y_i = \beta_0 + \beta_1 X_i + 0 \times Z_i = \epsilon_i
$$

In this case OLS is unbiased ...
$$
\begin{aligned}[t]
\E(\hat\beta_0) &= \beta_0 \\
\E(\hat\beta_1) &= \beta_1 \\
\E(\hat\beta_2) &= 0
\end{aligned}
$$

However, including an irrelevant variable will increase the standard errors for $\hat{\beta}_1$. Why?


## Multicollinearity

### Sampling Variance for simple linear regression

In bivariate linear regression, the sampling distribution of the slope coefficient is
$$
\Var(\hat{\beta}_1) = \frac{\sigma^2}{\sum_{i = 1}^n (X_i - \bar{X}_i)^ 2},
$$
and the standard error is $\se(\hat{\beta}_1} =\sqrt{\Var{\hat{\beta}_1}}$.

What factors affect the standard errors and how? 

- The error variance: $\uparrow \sigma^2 \to \uparrow \se(\hat{\beta})_1$
- The variance of $X$: $\uparrow \Var(X) \to \downarrow \se(\hat{\beta})_1$


Consider the linear regression model,
$$
Y_i = \beta_0 + \beta_1 X_i + \epsilon_i.
$$
What if we included $X_i$ twice?
$$
Y_i = \tilde\beta_0 + \tilde\beta_1 X_i + \tilde\beta_2 X_i + \epsilon_i.
$$
Clearly, any combination of $\tilde\beta_1$ and $\tilde\beta_2$ where
$$
\tilde\beta_1 + \tilde\beta_2 =\beta_1 
$$
will fit the model as well as any other.

Consider cases of 

- bivariate OLS with "effective" number of observations
- continuous OLS


----------------- ------------------- ------------------ ------------------
$\Cov(X_1, X_2)$  $\Cov(X_2, Y) > 0$  $\Cov(X_2, Y) = 0$ $\Cov(X_2, Y) < 0$
----------------- ------------------- ------------------ ------------------
$> 0$             +                   0                  -
$0$               0                   0                  0
$< 0$             -                   0                  +
----------------- ------------------- ------------------ ------------------

Summary:

1. OVB is intrinsic to observational methods relying on selection on observables---not just regression.
2. Control for all plausible "pre-treatment" variables
2. Reason about possible biases due to OVB
3. Sensitivity of coefficients to inclusion of control variables is an indication of the plausibility of OVB. @AltonjiElderTaber2005a formalize this.

In practice, this is the primary problem of many papers and papers.
That is because it biases the coefficient of interest.
Reviewers and discussants will often ask about whether you have considered "controlling"
for *insert variable here*.

Although these may be legitimate concerns, not all commenters understand the purpose of controls variables so some of these may not be legitimate, and in fact harmful.
There two arguments to consider when controlling for a variable.

1. The omitted variable has to plausibly be correlated with *both* the variable of interest *and* the outcome variable, and the burden is on the commenter to provide at a confounding variable and plausible relationships. Simply stating that there *could* be an unobservable variable is trivially true, uninteresting, and not a fatal critique. That said, the plausibility of a causal claim would be higher if with methods less susceptible to unobserved confounders, such as experiments, instrumental variables, regression discontinuity, and difference-in-differences.

2. The omitted variable should be not be "post treatment" variable. If the omitted variable should not be one of the causal pathways by which $X$ affects $Y$, it should not be controlled for. If $Z$ affects the values of $X$ and also affects $Y$, then it needs to be controlled for.

How to assess 

There are two common ways of assessing plausibility.

1. **Informal method**. This is the methods that you see in many empirical papers. They estimate the model including different control variables. The less sensitive the coefficient(s) of the variables of interest are to the inclusion of control variables, the more plausible it is that the variable of interest also not sensitive to unobserved confounders [@AngristPischke2014a]. @Oster2016a states

    > A common heuristic for evaluating the robustness of a result to omitted variable bias concerns is to look
    > at the sensitivity of the treatment effect to inclusion of observed controls. In three top general interest
    > economics journals in 2012, 75% of non-experimental empirical papers included such sensitivity analysis. The
    > intuitive appeal of this approach lies in the idea that the bias arising from the observed controls is informative
    > about the bias that arises from the unobserved ones.

    Note that what is important is that the magnitude of the *coefficient* is stable to the inclusion of controls, not that the coefficient remains statistically significant.

2. **Formal methods:** There are several attempts to  @AltonjiElderTaber2005a, @BellowsMiguel2009a, and @Oster2016a, formalize the intuition behind the heuristic of coefficient stability to assess the sensitivity of the treatment to OVB.  

@BellowsMiguel2009a propose the following simple variable:
$$
\delta = \frac{\hat{\beta}_F}{\hat{\beta}_R - \hat{\beta}_C},
$$
The statistic $\delta$ is interpreted as the magnitude of covariance between the unobserved part of the controls and the treatment variable necessary to explain away the entire treatment effect of $X$ on $Y$.
A larger ratio suggests it is implausible that omitted variable bias could explain away the entire observed effect.[^ovb1]

[ovb1]: See @BellowsMiguel2009a [Appendix A.] for the derivation. @BellowsMiguel2009a generalizes @AltonjiElderTaber2005a from binary to continuous treatment variables. @Oster2016a further generalizes that estimator.


OVB is a intrinsic problem in observational research, and there is nothing you can do to ever ensure that you have controlled for all relevant variables.
However, all inference is uncertain, all models are wrong and misspecified, and so really people should learn to deal with uncertainty.


Note that methods such as *matching*, *propensity scores*, or *inverse weighting* still depend on assumptions about selection on observables. The differ from regression in the estimand or their sensitivity to model misspecification.

Preferences for "design based" inference is mostly driven by a desire to find situations (designs) where other assumptions can substitute for "selection on observables".
Apart from experiments, these include instrumental variables, regression discontinuity, and difference-in-differences.


@PeiPischkeSchwandt2017a suggest two tests:

- Coefficient Comparison Test: Add confounder and test that coefficient of interest does not change.
- Balancing Test: Regress confounder on treatment.
  $$
  Z = \alpha + \beta X + \epsilon
  $$
  Test that $\beta$ is 0.

Regrssion without the confounder:
$$
y_i = \tilde\alpha + \tilde\beta x_i + \epsilon_i
$$
Regression with the confounder:
$$
y_i = \alpha + \beta x_i + \gamma z_i + \epsilon_i
$$
The balancing test regression is the regression of $x$ on $z$:
$$
z_i = \delta_0 + \delta x_i + \eta_i
$$
The change in coefficient from adding $x_i$ to the regression comes from the OVB formula,
$$
\beta - \tilde\beta = \gamma \delta
$$

The balancing test is testing:
$$
H_0: \delta = 0
$$
The Coefficient Comparison Test is testing:
$$
H_0: \tilde\beta - \beta = 0 
$$
which is true if either $\gamma = 0$ or $\delta = 0$. 

The balancing test if more powerful if $z_i$ is measured with error.





Adding covariates sequentially, to see which confounders influence the coefficient of interest, is not appropriate. The order is arbitrary, but important for interpretation. See Ge

@Gelbach2016a.

## Measurement Error

### What's the problem?

It biases coefficients:

1. Variable with measurement error: biases $\beta$ towards zero (**attenuation bias**)
2. Other variables: Biases $\beta$ similarly to omitted variable bias. In other words, when a variable has measurement error it is an imperfect control. You can think of omitted variables as the limit of the effect of measurement error as it increases.


### What to do about it?

There's no easy fix within the OLS framework.

1. If the measurement error is in the variable of interest, then the variable will be biased towards zero, and your estimate is too large.
2. Find better measures with lower measurement errors. If the variable is the variable of interest, then perhaps combine multiple variables into a single index. If the measurement error is in the control variables, then include several measures. That these measure correlate closely increases their standard errors, but the control variables are not the object of the inferential analysis.
3. More complicated methods: errors in variable models, structural equation models, instrumental variable (IV) models, and Bayesian methods.

# Multiple Regression

## Omitted Variable

$$
Y_i = \beta_0 + \beta_x X + \beta_z Z 
$$
Suppose that 
$$
y_i = x_i \beta + z_i \delta + \epsilon_i
$$
Use least squares calculation,
$$
\hat{\beta} = (\mat{X}\T \mat{X})^{-1} \mat{X}\T \vec{y}
$$
Sustitute $y$,
$$
\begin{aligned}[t]
\hat{\beta} &= (\mat{X}\T \mat{X})^{-1} \mat{X}\T (\mat{X} \vec{\beta} + Z\delta + \epsilon) \\
&= \mat{X}\T \mat{X})^{-1} \mat{X}\T \mat{X} \vec{\beta} 
+ \mat{X}\T \mat{X})^{-1} \mat{X}\T Z \delta +
+ \mat{X}\T \mat{X})^{-1} \mat{X}\T \epsilon
\end{aligned}
$$
Taking the expectation and noting that $\E(X' \epsilon) = 0$,
$$
\begin{aligned}[t]
\E(\hat{\beta} | X) &= \beta + \mat{X}\T \mat{X})^{-1} \mat{X}\T Z \delta \\
&= \beta + \text{bias}
\end{aligned}
$$
There is no bias if either of these is true

1. $\delta$ is 0 (Z has no effect on $Y$)
2. $Z$ is uncorrelated with $X$


## What to do about Omitted Variable Bias

1. There are two old (but simple) preliminary tests 

    1. Regress $y$ on $\hat{y}$ and $\hat{y}^2$ (Stata command linktest) - tests that the dependent variable has the correct functional form
    2. Ramsey RESET test (stata command ovtest)
    
2. Coefficient Inclusing - will it reduce selection bias

    1. Oster and similar tests - e.g. Coefficient Comparison Tests
    2. Balancing Tests - regress x on the treatment.
    
3. Adding covariates sequentially doesn't make sense (but see Gelbach)

## Regression Anatomy

$$
\hat{\beta}_k = \frac{\cov{y_i, \tilde{x}_{ki}}}{\var(\tilde{x}_{ki})}
$$

See https://mpra.ub.uni-muenchen.de/23245/1/reganat.pdf for a proof.

## More Information

### Simpson's Paradox

- See @Samuels1993a for more discussion of Simpson's Paradox
- @Moore2005a collects and succinctly describes several examples of Simpson's Paradox
- An interactive visualization of the [Simpson's Paradox](http://vudlab.com/simpsons/)
- Horton. 2015. [Fun with Simpson's Paradox: Simulating Confounders](http://blog.revolutionanalytics.com/2015/11/fun-with-simpsons-paradox-simulating-confounders.html)
- Horton. 2012. [Example 9.20: visualizing Simpson’s paradox](https://www.r-bloggers.com/example-9-20-visualizing-simpsons-paradox/)
- See the [Wikipedia Page](https://en.wikipedia.org/wiki/Simpson%27s_paradox)
- [US Median Wage by Education Level](https://economix.blogs.nytimes.com/2013/05/01/can-every-group-be-worse-than-average-yes/). Overall wages have risen, but within every group, the wage has fallen.
- Nielsen. [Reinventing Explanation](http://michaelnielsen.org/reinventing_explanation/index.html) has a visual explanation of the Simpson's paradox
- Gelman. [Understanding Simpson's Praradox Using a Graph](http://andrewgelman.com/2014/04/08/understanding-simpsons-paradox-using-graph/). April 8, 2014. Discusses the Nielsen post, provides other visualizations, and notes how aggregation problems arise even in non-causal cases.
- @ArmstrongWattenberg2014a introduce the Comet Chart for visualizing Simpson's Paradoxes.
  See this [page](https://www.zanarmstrong.com/#/research-1/) for code and examples, including an [R implementation](https://gist.github.com/zanarmstrong/6c2855a34f504029847485c690692e75).



```r
reganatomy <- function(model, variable) {
    variable <- if (is.character(variable) & 1 == length(variable)) {
      variable
    } else {
      deparse(substitute(variable))
    }
    mod.mat <- model.matrix(model)
    var.names <- colnames(mod.mat)
    var <- which(variable == var.names)
    if (0 == length(var))  {
      stop(paste(variable, "is not a column of the model matrix."))
    }
    response <- model.response(model.frame(model))
    if (is.null(weights(model))) {
      wt <- rep(1, length(response))
    } else {
      wt <- weights(model)
    }
    res0 <- as_tibble(lm(cbind(mod.mat[, var], response) ~ 1, weights = wt)$residual)
    names(res0) <- c("y", "x")
    res <- as_tibble(lsfit(mod.mat[, -var], cbind(mod.mat[, var], response), 
                           wt = wt, intercept = FALSE)$residuals)
    names(res) <- c("y_partial", "x_partial")
    bind_cols(res0, res)
}
data("Bfox", package = "car")
m1 <- lm(partic ~ tfr + menwage + womwage + debt + parttime, data = Bfox)
an <- reganatomy(m1, "womwage")
ggplot(an, aes(x = x, y = y)) + geom_point()
```

<img src="ovb_files/figure-html/unnamed-chunk-5-1.svg" width="672" />

```r
ggplot(an, aes(x = x_partial, y = y_partial)) + geom_point()
```

<img src="ovb_files/figure-html/unnamed-chunk-5-2.svg" width="672" />

```r
betawt <- function(model, variables = NULL) {
  X <- model.matrix(model)
  var_names <- colnames(X)
  variables <- variables %||% setdiff(colnames(X), "(Intercept)")
  badvars <- setdiff(variables, var_names)
  if (length(badvars)) {
    stop("Variables not found in model matrix: ", paste0(badvars, collapse = ", "))
  }
  wt <- model$weights
  offset <- model$offset
  f <- function(i) {
    ii <- which(i == var_names)
    mod2 <- if (!is.null(wt)) {
      lm.wfit(X[ , -ii], X[, ii], wt, offset = offset)
    } else {
      lm.fit(X[ , -ii], X[, ii], offset = offset)
    }
    weighted.residuals(mod2) ^ 2
  }
  as_tibble(set_names(map(variables, f), variables))
}
data("Bfox", package = "car")
m1 <- lm(partic ~ tfr + menwage + womwage + debt + parttime, data = Bfox)
an <- betawt(m1, c("womwage", "tfr", "debt", "parttime"))
```

Comments on Aronow and Samii

- https://tompepinsky.com/2012/02/27/regression-estimates-the-conditional-variance-weighted-zzzzzzzzzzz/
- https://tompepinsky.com/2013/08/08/regression-representativeness-and-external-validity/
- https://tompepinsky.com/2012/02/08/identification-is-neither-necessary-nor-sufficient-for-policy-relevance/
- https://afinetheorem.wordpress.com/2016/02/26/does-regression-produce-representative-estimates-of-causal-effects-p-aronow-c-samii-2016/



```r
ytilde <- lm(prestige ~ type, data = Duncan)$residuals
xtilde <- lm(income ~ type, data = Duncan)$residuals
lm(ytilde ~ xtilde - 1)
```

```
## 
## Call:
## lm(formula = ytilde ~ xtilde - 1)
## 
## Coefficients:
## xtilde  
## 0.6758
```

```r
lm(prestige ~ income + type, data = Duncan)
```

```
## 
## Call:
## lm(formula = prestige ~ income + type, data = Duncan)
## 
## Coefficients:
## (Intercept)       income     typeprof       typewc  
##      6.7039       0.6758      33.1557      -4.2772
```


```r
Duncan <- Duncan %>%
  group_by(type) %>%
  mutate(prestige_res = prestige - mean(prestige),
         income_res = income - mean(income)) 
lm(prestige_res ~ income_res - 1, data = Duncan)
```

```
## 
## Call:
## lm(formula = prestige_res ~ income_res - 1, data = Duncan)
## 
## Coefficients:
## income_res  
##     0.6758
```

```r
lm(prestige ~ income_res, data = Duncan)
```

```
## 
## Call:
## lm(formula = prestige ~ income_res, data = Duncan)
## 
## Coefficients:
## (Intercept)   income_res  
##     47.6889       0.6758
```
