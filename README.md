<img align="right" width="156.5223" height="181.3" src="../master/figures/LightGBMLSS.png">


# LightGBMLSS - An extension of LightGBM to probabilistic forecasting
We propose a new framework of LightGBM that predicts the entire conditional distribution of a univariate response variable. In particular, **LightGBMLSS** models all moments of a parametric distribution, i.e., mean, location, scale and shape (LSS), instead of the conditional mean only. Choosing from a wide range of continuous, discrete, and mixed discrete-continuous distribution, modelling and predicting the entire conditional distribution greatly enhances the flexibility of LightGBM, as it allows to create probabilistic forecasts from which prediction intervals and quantiles of interest can be derived.

## Features
:white_check_mark: Estimation of all distributional parameters. <br/>
:white_check_mark: Automatic derivation of Gradients and Hessian of all distributional parameters using [PyTorch](https://pytorch.org/docs/stable/autograd.html). <br/>
:white_check_mark: Automated hyper-parameter search, including pruning, is done via [Optuna](https://optuna.org/). <br/>
:white_check_mark: The output of LightGBMLSS is explained using [SHapley Additive exPlanations](https://github.com/dsgibbons/shap). <br/>
:white_check_mark: LightGBMLSS provides full compatibility with all the features and functionality of LightGBM. <br/>
:white_check_mark: LightGBMLSS is available in Python. <br/>

## News
:boom: [2023-05-23] Release of v0.2.0. See the [release notes](https://github.com/StatMixedML/LightGBMLSS/releases) for an overview. <br/>
:boom: [2022-01-05] LightGBMLSS now supports estimating the full predictive distribution via [Expectile Regression](https://epub.ub.uni-muenchen.de/31542/1/1471082x14561155.pdf). <br/>
:boom: [2022-01-05] LightGBMLSS now supports automatic derivation of Gradients and Hessians. <br/>
:boom: [2022-01-04] LightGBMLSS is initialized with suitable starting values to improve convergence of estimation. <br/>
:boom: [2022-01-04] LightGBMLSS v0.1.0 is released!

## Installation
To install LightGBMLSS please first run
```python
pip install git+https://github.com/StatMixedML/LightGBMLSS.git
```
Then, to install the shap-dependency, run
```python
pip install git+https://github.com/dsgibbons/shap.git
```

## Available Distributions
LightGBMLSS currently supports the following [PyTorch distributions](https://pytorch.org/docs/stable/distributions.html).

| Distribution                                                                                                                         |   Usage                   |Type                                    | Support                         | Number of Parameters            |
| :----------------------------------------------------------------------------------------------------------------------------------: |:------------------------: |:-------------------------------------: | :-----------------------------: | :-----------------------------: | 
| [Beta](https://pytorch.org/docs/stable/distributions.html#beta)                                                                      | `Beta()`                  | Continuous <br /> (Univariate)         | $y \in (0, 1)$                  | 2                               |
| [Cauchy](https://pytorch.org/docs/stable/distributions.html#cauchy)                                                                  | `Cauchy()`                | Continuous <br /> (Univariate)         | $y \in (-\infty,\infty)$        | 2                               |
| [Expectile](https://epub.ub.uni-muenchen.de/31542/1/1471082x14561155.pdf)                                                            | `Expectile()`             | Continuous <br /> (Univariate)         | $y \in (-\infty,\infty)$        | Number of expectiles            |
| [Gamma](https://pytorch.org/docs/stable/distributions.html#gamma)                                                                    | `Gamma()`                 | Continuous <br /> (Univariate)         | $y \in (0, \infty)$             | 2                               |
| [Gaussian](https://pytorch.org/docs/stable/distributions.html#normal)                                                                | `Gaussian()`              | Continuous <br /> (Univariate)         | $y \in (-\infty,\infty)$        | 2                               |
| [Gumbel](https://pytorch.org/docs/stable/distributions.html#gumbel)                                                                  | `Gumbel()`                | Continuous <br /> (Univariate)         | $y \in (-\infty,\infty)$        | 2                               |
| [Laplace](https://pytorch.org/docs/stable/distributions.html#laplace)                                                                | `Laplace()`               | Continuous <br /> (Univariate)         | $y \in (-\infty,\infty)$        | 2                               |
| [LogNormal](https://pytorch.org/docs/stable/distributions.html#lognormal)                                                            | `LogNormal()`             | Continuous <br /> (Univariate)         | $y \in (0,\infty)$              | 2                               |
| [Negative Binomial](https://pytorch.org/docs/stable/distributions.html#negativebinomial)                                             | `NegativeBinomial()`      | Discrete Count <br /> (Univariate)     | $y \in (0, 1, 2, 3, ...)$       | 2                               |
| [Poisson](https://pytorch.org/docs/stable/distributions.html#poisson)                                                                | `Poisson()`               | Discrete Count <br /> (Univariate)     | $y \in (0, 1, 2, 3, ...)$       | 1                               |
| [Student-T](https://pytorch.org/docs/stable/distributions.html#studentt)                                                             | `StudentT()`              | Continuous <br /> (Univariate)         | $y \in (-\infty,\infty)$        | 3                               |
| [Weibull](https://pytorch.org/docs/stable/distributions.html#weibull)                                                                | `Weibull()`               | Continuous <br /> (Univariate)         | $y \in [0, \infty)$             | 2                               |

## Some Notes
### Stabilization
Since LightGBMLSS updates the parameter estimates by optimizing Gradients and Hessians, it is important that these are comparable in magnitude for all distributional parameters. Due to variability regarding the ranges, the estimation of Gradients and Hessians might become unstable so that LightGBMLSS might not converge or might converge very slowly. To mitigate these effects, we have implemented a stabilization of Gradients and Hessians. 

For improved convergence, an alternative approach is to standardize the (continuous) response variable, such as dividing it by 100 (e.g., y/100). This approach proves especially valuable when the response range significantly differs from that of Gradients and Hessians. Nevertheless, it is essential to carefully evaluate and apply both the built-in stabilization and response standardization techniques in consideration of the specific dataset at hand.

### Runtime
Since LightGBMLSS updates all distributional parameters simultaneously, it requires training ```[number of iterations] * [number of distributional parameters]``` trees. Hence, the runtime of LightGBMLSS is generally slightly higher as compared to XGBoost, which requires training ```[number of iterations]``` trees only. 


## Reference Paper
März, A. and Kneib, T.: (2022) [*Distributional Gradient Boosting Machines*](https://arxiv.org/abs/2204.00778). <br/>
März, Alexander (2019): [*XGBoostLSS - An extension of XGBoost to probabilistic forecasting*](https://arxiv.org/abs/1907.03178). 


<!---
[![Arxiv link](https://img.shields.io/badge/arXiv-Distributional%20Gradient%20Boosting%20Machines-color=brightgreen)](https://arxiv.org/abs/2204.00778) <br/>
[![Arxiv link](https://img.shields.io/badge/arXiv-XGBoostLSS%3A%20An%20extension%20of%20XGBoost%20to%20probabilistic%20forecasting-color=brightgreen)](https://arxiv.org/abs/1907.03178) <br/>
--->
