
# Discrete Outcome Variables

The linear regression model is:
$$
Y_i = \beta X_i + \epsilon_i
$$

But what if the outcome, $Y_i$, is binary $Y_i \in \{0, 1}$?
When OLS is used to estimate a binary outcome variable it is called the [linear probability model](https://en.wikipedia.org/wiki/Linear_probability_model)).
This chapter considers the properties of OLS estimation with a binary outcome variable, and introduces logit, a generalized linear model (GLM), for binary outcome variables.

## Linear Probability Model


## Logit Model

The logit model is a model of the probability that $y = 1$,
$$
\Pr(y_i = 1) = \logit^{-1}(\vec{x}_i\T \vec{\beta}) ,
$$
with the assumption that $y_i$ are independent given these probabilities.
The value $\vec{x}_i\T \vec{\beta}$ is called the *linear predictor*.

The *logit function* transforms values between 0 and 1 to be between $-\infty$ and $\+infty$,
$$
\logit(p) = \log\left( \frac{p}{1 - p} \right) = \log(p) - \log(1 - p) .
$$

The *logistic function* or *inverse logit function* transforms continuous values $(-\infty, +\infty)$ to be between 0 and 1,
$$
\logit^{-1}(x) = \frac{e^x}{1 + e^x} = \frac{1}{1 + e^{-x}} .
$$
The logistic function is the inverse of the logit function, so,
$$
\logit^{-1}(\logit(p)) = p ,
$$
and
$$
\logit(\logit^{-1}(x)) = x .
$$

**TODO** Plots of logit and logistic functions.

Thus, in logit functions 


To estimate the logit model, minimize the following objective function,
$$
\begin{aligned}[t]
\hat{\vec{beta}} &=
\argmax_{\vec{b}} \sum_i (y_i \log \Pr(y_i = 1) + (1 - y_i) \Pr(y_i = 0) \\
&= \argmax_{\vec{b}} \sum_i y_i \log(logit^{-1}(\vec{x}_i\T \vec{b})) + (1 - y_i) \log(1 - logit^{-1}(\vec{x}_i\T \vec{b})) .
\end{aligned}
$$
While OLS minimizes squared errors, the logit model minimizes a different objective function, which minimizes the log difference between the obeserved value ($y_i$) and the predicted probability of that value, $\Pr(y_i = 1)$ or $\Pr(y_i = 0)$.
There are several methods fo find the values of $\hat{\beta}$, including iterated least squares (cite), but these details are handled by the software.

While the coefficients of OLS are (usually) directly interpretable as the marginal effect of $x$ on $E(y)$, those of logit regression models are not.
The marginal effect of a logit model is,
$$
\frac{\partial\,\Pr(y = 1)}{\partial x_k} = \frac{1}{1 + e^{- \vec{x}\T \vec{\beta}}} = \beta_k \frac{e^{-\vec{\beta} \vec{x}}}{(1 + e^{- \vec{x} \vec{\beta}})^2}\T = \beta_k \Pr(y = 1) \Pr(y = 0) .
$$
In an OLS model, the marginal effect of $x$ does not depend on the the specific values of $x_i$.
In a logit model it does.


Interpretation short-cuts

- Calculate the Average Marginal Effects or the Marginal Effects at the Mean (or other Representative Values). CITES
- *The divide by four rule* Divide $\beta$ by four to get an upper bound on the marginal effect (in probability) of $x$.  The slope of the logistic curve is steepest at its center where $\logit^{-1}(\alpha + \beta x) = 0.5$ and $\alpha + \beta x = 0$. The slope of this curve at this point is $\beta e^0 / (1 + e^0)^2 = \beta / 4$ [@GelmanHill2007a, p. 82]. See [this blog post] (http://www.r-bloggers.com/divide-by-4-rule-for-marginal-effects/) for some examples.
- *log-odds* While the marginal effects of the logit model are non-linear on the probability scale, they are linear on the log-odds scale. A one unit change in $x$ is associated with a $\beta$ change in the log-odds $\log(\Pr(y = 1) / Pr(y = 0))$.

**TODO** Inference: standard errors, confidence intervals, statistical significance.

Predictions: For each observation, there is a predictive probability,
$$
\tilde{p}_i = Pr(\tilde{y}_i = 1) = \logit^{-1}(\tilde{\vec{x}}_i\T \hat{\vec{\beta}}) .
$$n

**TODO** Diagnostics: leverage, outliers, what isn't relevant from OLS.
