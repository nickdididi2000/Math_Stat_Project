---
<<<<<<< HEAD:Final-Project.knit.md
title: "Penalized Regression"
author:
- name: Will Orser
- name: Ellery Island
- name: Nick Di
date: "5/02/2022"
output:
  pdf_document:
    toc: yes
  html_document:
    toc: yes
    toc_float: yes
    df_print: paged
---



title: "Final Project"
output:
  html_document: default
  pdf_document: default
---


Ellery to do list:

  - finish background section!
  - relationship between s and lambda ?? 
  - citations for background section and deriving estimators
  

## I. Introduction

|     Lasso, an abbreviation for “least absolute shrinkage and selection operator”, was developed independently in the field of geophysics in 1986 (“Lasso (statistics)”). The technique was rediscovered, named, and popularized by statistician Robert Tibshirani in 1996, in his paper “Regression Shrinkage and Selection via the Lasso”. The topic of lasso stood out to our group as an option for the final project because we have all had experiences applying the technique in our Machine Learning courses. Lasso is also connected to the section of our Mathematical Statistics course devoted to linear models. In particular, lasso was developed as a method to overcome certain complaints that data analysts had with ordinary least squares (OLS) regression models, namely, prediction accuracy and interpretation. OLS estimates often have low bias but high variance, meaning that prediction accuracy can sometimes be improved by shrinking or setting to zero some regression coefficients. Further, OLS models typically contain a large number of predictors; we often would like to narrow this down to a smaller subset that exhibits the strongest effects (Tibshirani 1).
|     Lasso falls under the category of penalized or regularized regression methods. Penalized regression methods keep all the predictor variables in a model but constrain or regularize their regression coefficients by shrinking them towards zero. In certain cases, if the amount of shrinkage is large enough, these methods can also serve as variable selection techniques by shrinking some coefficients to zero (Gunes 3). This is the case with lasso, which provides both variable selection and regularization to enhance the prediction accuracy and the interpretability of the resulting statistical model. Lasso was originally developed for use on linear regression models, but is easily extended to other statistical models including generalized linear models, generalized estimating equations, and proportional hazards models (“Lasso (statistics)”). 
|     The sources we explored to learn about lasso in greater depth were “LASSO regression”, a brief overview of the technique written by J. Ranstam and J.A. Cook, Tibshirani’s paper mentioned above, and the chapter on lasso in An Introduction to Statistical Learning (ISLR; a statistics textbook commonly used in Machine Learning courses) by Gareth James et al. 
|     Ranstam and Cook provide a nice introductory look into lasso, explaining the motivation behind the method (standard regression models often overfit the data and overestimate the model’s predictive power), a general description of how lasso works including the role of cross-validation in selecting the tuning parameter $\lambda$, and some of the limitations of the method. 
|     Tibshirani’s paper proposes a new method for estimation in linear models (“the lasso”), explains the mathematical derivation of this method, and presents the results of various simulation studies, comparing the novel method to more established methods of variable selection and regularization, subset selection and ridge regression. Tibshirani concludes by examining the relative merits of the three methods in different scenarios, stating that lasso performs best in situations where the predictors represent a small to medium number of moderate-sized effects.
|     ISLR provided us with the most comprehensive (and understandable) look into lasso. ISLR explains the mathematics involved in lasso and provides an in-depth comparison to ridge regression at the mathematical, geometrical, and functional levels. The textbook concludes that neither method will universally dominate the other, but that lasso tends to perform better in situations where only a relatively small number of predictors have substantial coefficients, while ridge regression tends to perform better when the response variable is a function of many predictors, all with coefficients of relatively equal size. Finally, ISLR proved extremely useful to us because it included various graphs and visualizations that illustrate how and why lasso works the way it does.
|     In Section II of this report, we will describe the mathematical underpinnings of the lasso method. This will include the general form and notation used in lasso, an explanation of the “penalty term”, and alternate interpretations of how lasso achieves regularization and variable selection. In Section III, we will provide our main results, essentially showing lasso in action. This will include an illustrative example of the lasso technique on a simulated dataset. We will introduce the set-up for a simulation experiment using R that demonstrates the merits and drawbacks of using lasso in comparison to OLS regression. Then, we will compare relevant aspects of the models: regression coefficients, error metrics, and the bias and variance of model predictions. Section IV will be concluding remarks and a restatement of the main takeaways from our research.



## II. Background

### Ordinary Least Squares Estimation

In ordinary least squares estimation (OLS), we attempt to find a linear model that best fits the data. Our model is a polynomial $\hat{y} = \beta_0 +\beta_1x_1 + \beta_2x_2 + \space ...  \space + \beta_nx_n$ with unknown coefficients $\beta_0, \space \beta_1, \space \beta_2, \space .., \space \beta_n$. In the method of least squares, we find the values of these coefficients that minimize the distance between the true $y$ values and the predicted $y$ values $\hat{y}$. We define this distance as a residual: $y_i- \hat{y}$. To get an overall estimate of the prediction error of our model, we compute the residual for each observation, square the residuals and sum these values. We can write this as:  

$$
\sum_{i=1}^n (y_i - \hat{y}_i)^2 = \sum_{i=1}^n (y_i - [\beta_0 +\beta_1x_1 + \space ...  \space + \beta_nx_n])^2 \\
= \sum_{i=1}^n ( y_i +\beta_0 - \sum_{j=1}^p \beta_jx_{ij} )^2
$$
We can summarize the least squares method as: 
$$
\text{argmin}_{\beta_0,..., \beta_n}\sum_{i=1}^n ( y_i +\beta_0 - \sum_{j=1}^p \beta_jx_{ij} )^2
$$
Instead of using standard mathematical notation, we can write linear models and the least squares method in matrix notation. In matrix notation, a linear model is written as: 

$$\mathbf{y} = \mathbf{X}\boldsymbol\beta  + \boldsymbol\epsilon, \text{ where } E[\boldsymbol\epsilon] = \mathbf{0}$$.

$\mathbf{y}$ is the vector of outcomes, $\boldsymbol\beta$ is the vector of covariates, and $\mathbf{X}$ is the matrix of covariates: 
$$\mathbf{y} = \begin{pmatrix} y_1 \\ y_2 \\ \vdots \\ y_n \end{pmatrix}; \space\boldsymbol\beta = \begin{pmatrix} \beta_0 \\ \beta_1 \\ \vdots \\ \beta_p \end{pmatrix}; \space \mathbf{X} = \begin{pmatrix} 1 & x_{11} & \cdots & x_{p1} \\ 1 & x_{12} & \cdots & x_{p2} \\ \vdots & \vdots & \ddots & \vdots \\ 1 & x_{1n} & \cdots & x_{pn} \end{pmatrix}.$$ 
The least squares estimation method then becomes: 

$$\text{argmin}_{\boldsymbol\beta} (\mathbf{y} - \mathbf{X}\boldsymbol\beta)^\top(\mathbf{y} - \mathbf{X}\boldsymbol\beta)$$.

### Problems with Ordinary Least Squares Estimation 
#### Overfitting

### Lasso
  Lasso is an adjustment to the linear regression framework. In a lasso model, the goal is the same as for OLS model: minimize the RSS. However, we add an additional penalty term, shown in red below, that limits the values of the coefficients. Specifically, lasso is defined as: 
  
  $$\text{argmin}_{\beta_j}\sum_{i=1}^n ( y_i +\beta_0 - \sum_{j=1}^p \beta_jx_{ij} )^2 + \color{red}{\lambda \sum_{j=1}^p |\beta_j|}$$
When minimizing this quantity as a whole, we are minimizing each component -- both the RSS and the penalty term. Minimizing the penalty term, for a given $\lambda$, has the effect of reducing the values of the coefficients towards zero. The constant $\lambda$ allows us to control how much the coefficients are shrunk towards zero and is thus considered a tuning parameter for lasso models. Large $\lambda$ values weight the penalty term heavily, so the coefficient values must be very small to minimize the overall function. Small $\lambda$ values reduce the importance of the penalty term allowing the coefficients to be larger. In the extreme, if $\lambda$ is infinitely large, the coefficients would all become zero; if $\lambda$ is zero, the coefficients would be the OLS solution. We discuss how to choose $\lambda$ in the next section.  

  There is an alternate formulation of lasso that reveals how it is a constrained optimization problem. In this formulation, we define lasso as: 
$$
\text{argmin}_{\beta_j}\sum_{i=1}^n ( y_i +\beta_0 - \sum_{j=1}^p \beta_jx_{ij} )^2  \text{; subject to }  \sum_{j=1}^p |\beta_j| \le s.
$$
In this formulation it is clear that the goal remains to minimize the RSS; however, the values of the coefficients are subjected to an additional constraint. Instead of using the tuning parameter $\lambda$, the tuning parameter $s$ is used. For large values of $s$, the coefficients are unconstrained and can have large values. Small values of $s$ impose a tight constraint on the coefficients, forcing them to be small. 
  With this formulation of lasso, we can visualize the relationship between the RSS and the constraint in a two predictors setting. With two predictors, the constraint region is defined as $|\beta_1| + |\beta_2| \le s$; this is a diamond with height $s$. In the graph below, the blue diamond is the constraint region, the red ellipses represent contour lines of the RSS, and $\hat{\beta}$ is the OLS solution (the absolute minimum of the RSS). In a lasso model, the goal is to find the smallest RSS that is within the constraint region; in this graph, that is the point where the ellipses intersect the diamond at its top corner. 
  
![](lasso_vis.png)

**relationship between s and lambda???**

### Selecting the Tuning Parameter


### Comparison to Ridge Regression
  Ridge regression is another technique that modifies the OLS framework by constraining the values of the coefficients. Ridge regression is defined as: 
 $$\text{argmin}_{\beta_j}\sum_{i=1}^n ( y_i +\beta_0 - \sum_{j=1}^p \beta_jx_{ij} )^2 + \color{red}{\lambda \sum_{j=1}^p (\beta_j)^2}$$.
We can see that ridge regression is nearly identical to lasso; the only difference is in the penalty term (shown above in red). Instead of taking the absolute value of the coefficients, ridge regression squares the coefficients. 
  We can consider the constrained optimization formulation of ridge regression, as we did for lasso: 
$$
\text{argmin}_{\beta_j}\sum_{i=1}^n ( y_i +\beta_0 - \sum_{j=1}^p \beta_jx_{ij} )^2  \text{; subject to }  \sum_{j=1}^p (\beta_j)^2 \le s.
$$
With two predictors, the constraint region becomes a circle: $\beta_1^2 + \beta_2^2 \le s^2$. We can construct a very similar graph to the one above: 

![](ridge_vis.png)
  The only difference between lasso and ridge regression are their constraint regions. 
  
### Variable Selection

### Benefits of Lasso and Ridge Regression
  Both lasso and ridge regression are able to make more accurate predictions than OLS in many contexts. Lasso and ridge regression are often more accurate than OLS because they sacrifice a small increase in bias for a significant reduction in variance. Both ridge regression and lasso perform well in a variety of contexts, but the variable selection property of lasso is a significant advantage. Lasso models have fewer predictors, making them easier to interpret. Ridge regression, because it includes every variable in the model, outperforms lasso when all of the predictors are related to the outcome. On the other hand, lasso outperforms ridge regression when only a few of the predictors are related to the outcome. 
  In the main results section, we will derived the variance of OLS and ridge regression estimators and perform a simulation to examine bias and variance in lasso estimators.





## III. Main Results

### Deriving OLS, Ridge Regression and Lasso Estimators

#### OLS

As described above, the OLS problem can be written as $\text{argmin}_{\boldsymbol\beta} (\mathbf{y} - \mathbf{X}\boldsymbol\beta)^\top(\mathbf{y} - \mathbf{X}\boldsymbol\beta)$. 

We can derive the OLS estimate for $\boldsymbol\beta$: 
$$
\begin{aligned}

&\text{argmin}_{\boldsymbol\beta} (\mathbf{y} - \mathbf{X}\boldsymbol\beta)^\top(\mathbf{y} - \mathbf{X}\boldsymbol\beta) \\

&= \frac{\partial}{\partial \boldsymbol\beta} (\mathbf{y}^\top \mathbf{y} - \mathbf{y}^\top\mathbf{X}\boldsymbol\beta  - \boldsymbol\beta^T\mathbf{X}^Ty + \boldsymbol\beta^\top \mathbf{X}^\top \mathbf{X} \boldsymbol\beta) \\ 


&= \frac{\partial}{\partial \boldsymbol\beta} (\mathbf{y}^\top \mathbf{y} - 2\mathbf{y}^\top\mathbf{X}\boldsymbol\beta + \boldsymbol\beta^\top \mathbf{X}^\top \mathbf{X} \boldsymbol\beta) \\

&= -2\mathbf{X}^\top\mathbf{y} + 2 \mathbf{X}^\top \mathbf{X} \boldsymbol\beta \\

0 &\stackrel{set}{=} -2\mathbf{X}^\top\mathbf{y} + 2 \mathbf{X}^\top \mathbf{X} \boldsymbol\beta \\

2 \mathbf{X}^\top \mathbf{X} \boldsymbol\beta &= 2\mathbf{X}^\top\mathbf{y} \\ 

(\mathbf{X}^T\mathbf{X})^{-1}\mathbf{X}^\top \mathbf{X} \boldsymbol\beta &= (\mathbf{X}^T\mathbf{X})^{-1} \mathbf{X}^\top\mathbf{y} \\ 

 \hat{\boldsymbol\beta}& = (\mathbf{X}^T\mathbf{X})^{-1} \mathbf{X}^\top\mathbf{y}

\end{aligned}
$$
#### Ridge Regression

In ridge regression, the formula we are trying to minimize is $\sum_{i=1}^n(y_i - \beta_0 - \sum_{j=1}^p\beta_j x_{ij})^2 + \lambda\sum_{j=1}^p \beta_j^2$. We can write this in matrix notation as: $(\mathbf{y} - \mathbf{X}\boldsymbol\beta)^\top(\mathbf{y} - \mathbf{X}\boldsymbol\beta) + \lambda \boldsymbol\beta^T\boldsymbol\beta$. We can minimize this in much the same way as in OLS: 

$$
\begin{aligned}

&\text{argmin}_{\boldsymbol\beta} (\mathbf{y} - \mathbf{X}\boldsymbol\beta)^\top(\mathbf{y} - \mathbf{X}\boldsymbol\beta) + \lambda \boldsymbol\beta^T\boldsymbol\beta \\ 

&= \frac{\partial}{\partial \boldsymbol\beta} (\mathbf{y}^\top \mathbf{y} - 2\mathbf{y}^\top\mathbf{X}\boldsymbol\beta + \boldsymbol\beta^\top \mathbf{X}^\top \mathbf{X} \boldsymbol\beta + \lambda \boldsymbol\beta^T\boldsymbol\beta) \\

&= -2\mathbf{X}^\top\mathbf{y} + 2 \mathbf{X}^\top \mathbf{X} \boldsymbol\beta + 2\lambda\boldsymbol\beta \\

0 &\stackrel{set}{=} -2\mathbf{X}^\top\mathbf{y} + 2 \mathbf{X}^\top \mathbf{X} \boldsymbol\beta + 2\lambda\boldsymbol\beta\\

\mathbf{X}^\top \mathbf{X} \boldsymbol\beta + \lambda\boldsymbol\beta &= \mathbf{X}^\top\mathbf{y} \\ 

(\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) \boldsymbol\beta &= \mathbf{X}^\top\mathbf{y} \\ 

(\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) (\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\boldsymbol\beta &= \mathbf{X}^\top\mathbf{y}(\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\\ 

\boldsymbol\beta &= \mathbf{X}^\top\mathbf{y}(\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\\ 

\end{aligned}
$$


#### Considering a Simple Case
  We can consider a simple case: $\mathbf{X}$ is a diagonal matrix with 1's on the diagonals and 0's on all the off diagonals, the number of predictors equals the number of cases, and we force the intercept to go through the origin. This case allows us simplify our OLS and ridge regression estimators. For OLS, the solution is $\boldsymbol\beta = \mathbf{y}$ and for ridge regression the solution becomes $\boldsymbol\beta = \frac{\mathbf{y}}{1+\lambda}$. 
  Applying this simple case to find the estimators is helpful particularly for Lasso. Unlike OLS and Ridge Regression, there is no closed form solution for $\boldsymbol\beta$ for Lasso. To derive any estimators for Lasso, we must consider this simple case. 
  
#### Lasso Estimators in a Simple Case

For lasso, we can not find a general closed form solution for $\boldsymbol\beta$, so we will derive the lasso estimates for $\boldsymbol\beta$ for the simple case described above. We will not use matrix notation in order to easily apply the assumptions of our simple case. 

Remember that we can write the general form of lasso as: 

$$
\begin{aligned}

\text{argmin}_{\beta}\sum_{i=1}^n(y_i - \beta_0 - \sum_{j=1}^p\beta_j x_{ij})^2 + \lambda\sum_{j=1}^p |\beta_j|

\end{aligned}
$$
If we apply our simplifying assumptions, we can write:

$$
\begin{aligned}

\text{argmin}_{\beta}\sum_{j=1}^p(y_i - \beta_1)^2 + \lambda|\beta_1| 

\end{aligned}
$$

With these assumptions, we can find a closed form solution for $\beta$: 

$$
\begin{aligned}

&\text{argmin}_{\beta}(y_i - \beta_1)^2 + \lambda|\beta_1| \\ 

&= \frac{\partial}{\partial \beta} \left( (y_j - \beta_1)^2 + \lambda|\beta_1| \right) \\

&= \frac{\partial}{\partial \beta} \left( y_j^2 - 2y_j\beta_1 + \beta_1^2 + \lambda|\beta_1| \right) \\

&=  - 2y_j + 2\beta_1 + \lambda sign(\beta_1) \\

\end{aligned}
$$
To solve for $\beta_1$, we must consider different regions: (1) when $\beta_1 < 0$, (2) when $\beta_1 > 0$ and (3) when $\beta_1 = 0$.  

(1) when $\beta_1 < 0$ or when $y_j < - \lambda/2$: 

$$
\begin{aligned}

0 &\stackrel{set}{=} - 2y_j + 2\beta_1 - \lambda \\

\beta_1 &= y_j + \lambda/2 \\

\end{aligned}
$$
(2) when $\beta_1 > 0$ or when $y_j > \lambda/2$: 
$$
\begin{aligned}

0 &\stackrel{set}{=} - 2y_j + 2\beta_1 + \lambda \\

\beta_1 &= y_j - \lambda/2 \\

\end{aligned}
$$
(3) when $\beta_1 = 0$ ASK KELSEY
$$

\text{when } \beta_1 = 0 \text{ or when } |y_i| \le \lambda/2 : \\

0 


$$


#### Visualizing the Simple Case Estimators
The graph below shows the simple case coefficient estimates for OLS, ridge regression and lasso as a function of the data $y_j$. We can see from that graph, and from the equations derived above, that ridge regression scales the coefficient estimates by the same factor, $1/(1+\lambda)$, regardless of the value of $y_j$. Since it is impossible to divide a non-zero number by any value and get 0, ridge regression cannot set any coefficient to zero unless it is already 0. However, lasso performs shrinkage in a different way, allowing some coefficients to be 0. Lasso changes the values of the coefficients by adding or subtracting $\lambda/2$, depending on the corresponding $y_j$. If $y_j$ is inside the region $(-\lambda/2, \lambda/2)$, the coefficient is shrunk to 0. 


```r
library(ggplot2)
lambda <- 5
ols <- function(x) x
ridge <- function(x) x/(1+lambda)
lasso <-function(x) ifelse(x > lambda/2, x-lambda/2,
   ifelse(x < -lambda/2, x+lambda/2, 
   ifelse( -lambda/2 <= x & x <= lambda/2, 0, 0)))


ggplot() +
  xlim(-10, 10)+
  geom_function(fun = ols,
                aes(color = 'OLS'),
                linetype = "dashed") +
  geom_function (fun = ridge,
                 aes(color = 'Ridge'),
                 lwd = 1.2)+
  geom_function(fun = lasso,
                aes(color = 'Lasso'),
                lwd = 1.2) +
  theme_bw()+
  theme(panel.grid.minor.x = element_blank(),
        panel.grid.minor.y = element_blank())+
  scale_color_manual(name = 'Models',
                     breaks = c('OLS', 'Ridge', 'Lasso'),
                     values = c('OLS'='gray54', 'Ridge'='olivedrab3', 'Lasso'='tan2'))+
  labs(y = "Coefficient Estimates", x = "yj")
```

![](Final-Project_files/figure-latex/unnamed-chunk-1-1.pdf)


## Deriving Bias and Variance of OLS and Ridge Regression Estimators

### OLS 

#### Bias
We will assume that $\mathbf{y} = \mathbf{X}\boldsymbol\beta  + \boldsymbol\epsilon$ and that $E[\boldsymbol\epsilon] = \mathbf{0}$. We can show that the least squares estimator $\hat{\boldsymbol\beta} = (\mathbf{X}^T\mathbf{X})^{-1} \mathbf{X}^\top\mathbf{y}$ is an unbiased estimator of $\boldsymbol\beta$: 

$$
\begin{aligned}

E[\hat{\boldsymbol\beta}_{OLS}] &= E[(\mathbf{X}^T\mathbf{X})^{-1} \mathbf{X}^\top\mathbf{y}]\\
&= (\mathbf{X}^T\mathbf{X})^{-1} \mathbf{X}^\top E[\mathbf{y}], \text{ since X is fixed} \\
&= (\mathbf{X}^T\mathbf{X})^{-1} \mathbf{X}^\top E[\mathbf{X}\boldsymbol\beta  + \boldsymbol\epsilon], \text{ by assumption}\\
&= (\mathbf{X}^T\mathbf{X})^{-1} \mathbf{X}^\top (\mathbf{X}\boldsymbol\beta  + E[\boldsymbol\epsilon])\\
&= (\mathbf{X}^T\mathbf{X})^{-1} \mathbf{X}^\top (\mathbf{X}\boldsymbol\beta  + 0), \text{ by assumption}\\
&=(\mathbf{X}^T\mathbf{X})^{-1} (\mathbf{X}^\top\mathbf{X})\boldsymbol\beta \\
&= \boldsymbol\beta
\end{aligned}
$$
#### Variance
We will assume that $\mathbf{y} = \mathbf{X}\boldsymbol\beta  + \boldsymbol\epsilon$, $E[\boldsymbol\epsilon] = \mathbf{0}$ and that $Var[\boldsymbol\epsilon] = \sigma^2 \mathbf{I}$. We can show that the variance of the least squares estimator $\hat{\boldsymbol\beta} = (\mathbf{X}^T\mathbf{X})^{-1} \mathbf{X}^\top\mathbf{y}$ is $Var[\hat{\boldsymbol\beta}] = \sigma^2(\mathbf{X}^T\mathbf{X})^{-1}$: 

$$
\begin{aligned}

Var[\hat{\boldsymbol\beta}_{OLS}] &= Var[(\mathbf{X}^T\mathbf{X})^{-1} \mathbf{X}^\top\mathbf{y}]\\

&= (\mathbf{X}^T\mathbf{X})^{-1} \mathbf{X}^\top Var[\mathbf{y}]((\mathbf{X}^T\mathbf{X})^{-1} \mathbf{X}^\top)^\top, \text{ since } Var(\mathbf{Ax}) = \mathbf{A}Var(\mathbf{x})\mathbf{A}^\top \\

&= (\mathbf{X}^T\mathbf{X})^{-1} \mathbf{X}^\top Var[\mathbf{y}] \mathbf{X}(\mathbf{X}^T\mathbf{X})^{-1}, \text{ since } (\mathbf{AB})^\top = \mathbf{B}^\top\mathbf{A}^\top \text{ and } (\mathbf{A}^{-1})^\top = (\mathbf{A}^\top)^{-1} \\

&= (\mathbf{X}^T\mathbf{X})^{-1} \mathbf{X}^\top Var[\mathbf{X}\boldsymbol\beta  + \boldsymbol\epsilon] \mathbf{X}(\mathbf{X}^T\mathbf{X})^{-1}, \text{ by assumption}\\

&= (\mathbf{X}^T\mathbf{X})^{-1} \mathbf{X}^\top Var[\boldsymbol\epsilon] \mathbf{X}(\mathbf{X}^T\mathbf{X})^{-1}, \text{ since } \mathbf{X} \text{ and } \boldsymbol{\beta} \text{ are fixed}\\

&= (\mathbf{X}^T\mathbf{X})^{-1} \mathbf{X}^\top (\sigma^2\mathbf{I}) \mathbf{X}(\mathbf{X}^T\mathbf{X})^{-1}, \text{ by assumption} \\

&= \sigma^2(\mathbf{X}^T\mathbf{X})^{-1} (\mathbf{X}^\top \mathbf{X})(\mathbf{X}^T\mathbf{X})^{-1} \\

&= \sigma^2(\mathbf{X}^T\mathbf{X})^{-1} \\

\end{aligned}
$$

### Ridge Regression
#### Bias
We will assume that $\mathbf{y} = \mathbf{X}\boldsymbol\beta  + \boldsymbol\epsilon$ and that $E[\boldsymbol\epsilon] = \mathbf{0}$. We can show that the ridge regression estimator $\boldsymbol\beta = (\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top\mathbf{y}$ is a biased estimator of $\boldsymbol\beta$: 

$$
\begin{aligned}

E[\hat{\boldsymbol\beta}_{ridge}] &= E[(\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top\mathbf{y}]\\

&= E[(\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top (\mathbf{X}\boldsymbol\beta + \boldsymbol\epsilon)], \text{ by assumption} \\

&= E[(\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top (\mathbf{X}\boldsymbol\beta) + (\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top (\boldsymbol\epsilon)] \\

&= E[(\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top (\mathbf{X}\boldsymbol\beta)] + E[(\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top (\boldsymbol\epsilon)] \\

&= (\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top (\mathbf{X}\boldsymbol\beta) + (\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top E[(\boldsymbol\epsilon)], \text{ since } \mathbf{X} \text{ and } \boldsymbol{\beta} \text{ are fixed} \\

&= (\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top (\mathbf{X}\boldsymbol\beta) + (\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top (0), \text{ by assumption } \\

&= (\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top \mathbf{X}\boldsymbol\beta  \\

\end{aligned}
$$
Since $E[\hat{\boldsymbol\beta}_{ridge}] = (\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top \mathbf{X}\boldsymbol\beta$, the ridge regression estimator for $\boldsymbol{\beta}$ will always be biased, unless $\lambda = 0$. If $\lambda = 0$, the ridge regression estimator is equal to the OLS estimator, which we showed above is unbiased. 

### Variance
We will assume that $\mathbf{y} = \mathbf{X}\boldsymbol\beta  + \boldsymbol\epsilon$, $E[\boldsymbol\epsilon] = \mathbf{0}$ and that $Var[\boldsymbol\epsilon] = \sigma^2 \mathbf{I}$. We can show that the variance of the ridge regression estimator is $\sigma^2(\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top \mathbf{X} (\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I})^{-1}$: 

$$
\begin{aligned}
Var[\hat{\boldsymbol\beta}_{ridge}] &= Var((\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top\mathbf{y})\\

&= (\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top Var(\mathbf{y}) ((\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top)^\top, \text{ since } Var(\mathbf{Ax}) = \mathbf{A}Var(\mathbf{x})\mathbf{A}^\top \\

&= (\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top Var(\mathbf{X}\boldsymbol\beta + \boldsymbol\epsilon) ((\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top)^\top, \text{ by assumption } \\

&= (\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top (Var(\mathbf{X}\boldsymbol\beta) + Var(\boldsymbol\epsilon)) ((\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top)^\top \\

&= (\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top Var(\boldsymbol\epsilon) ((\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top)^\top, \text{ since } \mathbf{X} \text{ and } \boldsymbol{\beta} \text{ are fixed} \\

&= (\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top (\sigma^2\mathbf{I}) ((\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top)^\top, \text{ by assumption }  \\

&= \sigma^2(\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top) ((\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top)^\top \\

&= \sigma^2(\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top \mathbf{X} ((\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1})^\top \\

& = \sigma^2(\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top \mathbf{X} (\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I})^{-1} \\

\end{aligned}
$$
We can show that the variance of the ridge regression estimator is equal to the variance of the OLS estimator when $\lambda = 0$: 

$$
\begin{aligned}

Var[\hat{\boldsymbol\beta}_{ridge}] \text{ when } \lambda = 0: \\
&= \sigma^2(\mathbf{X}^\top \mathbf{X} + 0\mathbf{I}) ^{-1}\mathbf{X}^\top \mathbf{X} (\mathbf{X}^\top \mathbf{X} + 0\mathbf{I})^{-1} \\
&= \sigma^2(\mathbf{X}^\top \mathbf{X}) ^{-1}\mathbf{X}^\top \mathbf{X} (\mathbf{X}^\top \mathbf{X})^{-1} \\
&= \sigma^2(\mathbf{X}^\top \mathbf{X}) ^{-1} = Var[\hat{\boldsymbol\beta}_{OLS}]

\end{aligned}
$$
Importantly, the variance of the ridge regression estimator is always smaller than the variance of the OLS estimator when $\lambda>0$. To see that this is true, we can consider the case when $\mathbf{X}$ is a 1 by 1 matrix with value 1 ([1]) and $\lambda = 1$:

$$
\begin{aligned}

Var[\hat{\boldsymbol\beta}_{ridge}] &= \sigma^2(\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I}) ^{-1}\mathbf{X}^\top \mathbf{X} (\mathbf{X}^\top \mathbf{X} + \lambda\mathbf{I})^{-1} \\

&= \sigma^2(1 *1 + 1) ^{-1}1*1 (1*1 + 1)^{-1} \\

&= \sigma^2(2) ^{-1}(2)^{-1} \\

&= \frac{\sigma^2}{4} 

\end{aligned}
$$
$$
\begin{aligned}

Var[\hat{\boldsymbol\beta}_{OLS}] &= \sigma^2(\mathbf{X}^T\mathbf{X})^{-1} \\

&= \sigma^2(1 *1) ^{-1} \\

&= \frac{\sigma^2}{1} = \sigma^2

\end{aligned}
$$
From this simple case, we can see that $Var[\hat{\boldsymbol\beta}_{ridge}]$ is smaller than $Var[\hat{\boldsymbol\beta}_{OLS}]$. This holds true for all cases when $\lambda>0$, but the proof of that is beyond the scope of this project. 
 
### Lasso
Lasso, unlike OLS and ridge regression, does not have closed form solutions for the bias and variance of its estimator. To examine the bias and variance of lasso estimators, we constructed a simulation and we discuss the results of the simulation in the next section. 

## Simulation


## IV. Discussion


|     To conclude our report, we will briefly discuss the relevance, limitations, and applications of lasso regression. Lasso is relevant because of its ability to address the shortcomings of OLS regression models. Specifically, lasso is able to account for multicollinearity of predictor variables and correct for overfitting in situations with a large number of predictors. Furthermore, unlike some penalized regression methods (e.g., ridge regression) lasso has the ability to perform variable selection, by shrinking the regression coefficients of certain predictors to zero, thus improving model interpretability. 
	
|     In Section III, we included relevant outputs and visualizations from a simulation experiment in which we compared the performance of lasso and OLS in modeling a fictitious dataset. There were two main takeaways from our simulation experiment. First, lasso, unlike OLS, performs variable selection by shrinking the coefficients of uninformative predictors to zero. In the coefficient output tables, we saw that lasso set the coefficients of uninformative predictors (which we had given a true value of zero in the data creation stage) to zero, while OLS gave these variables very small nonzero coefficient values. Thus, lasso helps to simplify the model (and prevent overfitting) by eliminating predictors with negligible effects on the output. The second main takeaway was that lasso, in comparison to OLS, provides an advantage in terms of the bias-variance tradeoff. The density plots from our simulations show how lasso returns predictor coefficient estimates that are slightly more biased, but much less variable. 

|     In spite of the results of our simulation, it is important to recognize that lasso is not a cure-all for the issues of overfitting and multicollinearity and does not remove the need to validate a model on a test dataset. The primary limitation of lasso is that it trades off potential bias in estimating individual parameters for a better expected overall prediction. In other words, under the lasso approach, regression coefficients may not be reliably interpreted in terms of independent risk factors, as the model’s focus is on the best combined prediction, not the accuracy of the estimation and interpretation of the contribution of individual variables. Also, lasso may underperform in comparison to ridge regression in situations where the predictor variables account for a large number of small effects on the response variable. 

|     In the real world, lasso is commonly used to handle genetic data because the number of potential predictors is often large relative to the number of observations and there is often little prior knowledge to inform variable selection (Ranstam & Cook 1). Lasso also has applications in economics and finance, helping to predict events like corporate bankruptcy. Besides these specific fields of application, lasso is also implementable in any situation where multiple linear regression would apply. Multiple linear regression has wide-ranging applications, but to provide a specific example, it is often used in medical research. Researchers may want to test whether there is a relationship between various categorical variables (e.g., drug treatment group, patient sex), quantitative variables (e.g., patient age, cardiac output), and a quantitative outcome (e.g., blood pressure). Multiple linear regression allows researchers to test for this relationship, as well as quantify its direction and strength. Lasso regression may come into play in scenarios where multicollinearity exists (e.g., patient height and weight), there are a large number of predictors (and it is likely some are uninformative), and when it is important to have less-variable predictions for model coefficients. 



## V. References

1. Gunes, Funda. “Penalized Regression Methods for Linear Models in SAS/STAT®.” SAS
 Institute, Inc. 2015, p. 1. 
 
2. James, Gareth, et al. “The Lasso.” An Introduction to Statistical Learning: with Applications in R. New York, Springer, 2013.

3. “Lasso (statistics).” Wikipedia. Wikimedia Foundation, 13 Feb. 2022,
https://en.wikipedia.org/wiki/Lasso_(statistics)

4. Ranstam, J. Cook, J. A. “LASSO regression.” British Journal of Surgery, vol. 105, iss. 10, 2018,
 p. 1348.

5. Tibshirani, Robert. “Regression Shrinkage and Selection via the Lasso.” Journal of the Royal
Statistical Society. Series B (Methodological), vol. 58, no. 1, 1996, pp. 267–88.
