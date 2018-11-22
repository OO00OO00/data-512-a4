Introduction
------------

### Big Picture

**Premise**: *Researchers outside of machine learning should evaluate the predictive power of their statistical models when*:

-   *the underlying data represents people*, and
-   *the model may suggest consequences for them*.

------------------------------------------------------------------------

Many researchers focus on causal or explanatory models, but do not appreciate the real-life variability observed with individuals. To illustrate the importance of this issue, I want to evaluate the predictive power of models described in the controversial book "The Bell Curve" (hereafter referred to as *TBC*) by Charles Murray and Richard Herrnstein[1].

These models use logistic regression to quantify how certain factors (most notably IQ) contribute to a particular binary target. E.g., one of the most controversial models of TBC is that IQ -- moreso than socioeconomic status (SES) -- explains whether a teenager will be below the poverty line in calendar year 1989 (which was about 15 years after IQ was measured for these data subjects). The authors of TBC also examine targets like whether an individual will:

-   drop out of highschool
-   receive a GED within the next ~15 years
-   become unemployed for at least 1 month in 1989, or
-   ever be interviewed in jail.

The authors use conclusions from these models to claim that people with materially low IQ are bad for society and, ultimately, public policy should be changed to reflect that. Furthermore, the authors say that minorities and foreigners tend to have lower IQ and, implicitly from their prior argument, are also bad for society.

If the conclusions of these models are not consistent with the data used to build them, then the models are not meaningful or useful. I will examine the predictive power of 24 logistic regression models from TBC by measuring the consistency of classification performance on their training data.

### Why should we care about this?

Readers should care because it impacts how researchers build statistical models. Researchers who do not focus on prediction can conflate it with explanation or causation. While this may sound like an abstract semantics issue, statistical prediction is commonly evaluated in ways very different from those in statistical explanation. This can mean that a model with adequate explanatory components could have very poor predictive components. E.g., deciding whether a prisoner is released based on a model of recidivism. Even if this model explains its training data well, it would be very unfair for prisoners if the model could not accurately predict future recidivism.

Also, I think it's important for the general public to have higher standards when being persuaded by statistics. Otherwise, people may believe statements without adequate evidence.

### Why don't we know about this already?

The biggest motivation for me has been the work done by Dr. Claudia Krenz[2]. In 2007, she posted a [statistical review](http://www.claudiax.net/bell.html) of the logistic regression model for entering poverty in calendar year 1989. Although I intend to reproduce her work with this project, she focused on 1 model and did limited evaluations of its predictive power. What's new would be my analysis on 24 models from TBC with a detailed exploration of their predictive power (automated and summarized by code).

Also, I think this work would be novel in illustrating the differences in explanatory vs predictive power. Some components of these models have strong explanatory power, but impotent predictive power. I haven't seen these properties so prominent in one dataset before. Depending on how my project goes, it could be a useful learning tool for some. Similarly, [Dr. Galit Shmueli](https://arxiv.org/pdf/1101.0891) has written about these properties in great detail[3], but has not displayed them with explicit examples (that I'm aware of).

Lastly, TBC has been primarily discussed in the context of social psychology. Various researchers have criticized myriad aspects of TBC (with James Heckman[4] providing one of the most thorough [responses](https://www.jstor.org/stable/2138756)), but most have been domain specific, e.g., most notably:

-   the factor used as "IQ" is not in fact a reasonable measure of general intelligence
-   the factor used as "SES" is inappropriately constructed

My work would entirely focus on the exact models built in TBC, i.e., temporarily accepting all the data and modeling assumptions from authors of TBC. The appendix of TBC lists model diagnostics and data descriptions which I've used to reproduce the models within great precision (i.e., coefficient values agreeing within 1e-4). If my results show poor predictive power for these models *while granting the authors' controversial assumptions*, the conclusions and public policy recommendations by TBC authors would be supported less by these models, if at all.

### What are some human-centered aspects about this project?

The core human-centered focus of this project is fairness for people who could be harmed by conflating statistical philosophies (i.e., explanatory vs predictive power). From a statistical perspective, you could interpret explanatory modeling of people as understanding the *average person*, whereas predictive modeling considers *individuals*. When a model is based on individual people (e.g., each row represents a person), I think the researcher ought to consider the predictive power of the original model, even if it was created in the context of an explanatory or causal model. If the model explains well but is not predictive (in a reasonable context dependent on the data and type of model, etc.), then the researcher should improve the model or accept an insignificant result. Otherwise, the model's conclusions and societal impacts may, at best, not be meaningful when applied and, at worst, harmful to vulnerable populations.

Project Plan
------------

### Research Topics

1.  Are the conclusions and public policy recommendations from TBC authors supported by the logistic regression models they built?
2.  To what extent should an explanatory model be predictive, when its underlying data directly describes people?
3.  How strict should we evaluate statistical models that could negatively impact people?

Underlying Data
---------------

The underlying data comes from the publicly available National Longitudinal Survey of Youth 1979 (NLSY79) prepared by the US Bureau of Labor Statistics[5]. In this study, 12,686 young Americans of varying backgrounds were interviewed each year between 1979 through 1994. Although the survey continued after this time, the last relevant year for TBC was 1990. These interviews covered a large number of topics which were tabulated and, more recently, released online.

### Data in Practice

Although the source data is available, the authors of TBC did not make clear exactly which variables were used. I reached out to the assistant of Charles Murray for clarification but have had no response. Also, they derived some new fields (like SES), which were not explained enough for me to recreate them from source data. To credit the authors, however, they did release their [modified datasets online](http://www.rasmusen.org/xpacioli/bellcurve/NATION.TXT) and included a [data dictionary](http://www.rasmusen.org/xpacioli/bellcurve/nation.hdr)[6]. In order to reproduce TBC results as closely as possible, I have opted to use the authors' data.

### Data Preparation

The only data prepartion needed is to find the exact data subsets and model formulae to reproduce TBC results. Currently, I've matched 22 models very closely and 2 other models that are reasonably close. These models come from the authors' "nation.txt" dataset. Although there are more datasets considered in TBC, I think I can only cover these 24 in the time I have.

Anticipated Approach
--------------------

### Background

I plan on discussing explanatory and predictive models to motivate their differences. This discussion will likely cover material from Shmueli's paper, "To Explain or To Predict?".

### Analysis

In R, I will develop functions that automate bootstrap regression as well as summarize the performance of each bootstrapped model. I know of the bootstrap package in R, but decided to build my own functions to facilitate a custom process. Also, I plan on collecting key measurements across these models to find overall trends (because no one wants to read diagnostics for 24 models). I would have an appendix that lists relevant diagnostics for all models reviewed.

For more detail, I intend on doing the following:

1.  Specify a training dataset and formula consistent with a TBC model.
2.  Fit 10K logistic regression models via bootstrapping.
3.  Score the training data with each bootstrapped model.
4.  Map the modeled probabilities to a classification by selecting a probability threshold.
5.  Build a confusion matrix with the modeled classifications and actual target data.
6.  Calculate various performance metrics for a binary target, in particular, Matthew's correlation coefficient (MCC).
7.  Repeat steps 4 - 6 for a large range of possible probability thresholds (I've selected a grid of length 500 between 0 and 1).
8.  Inspect the distribution of MCC across all probability thresholds to find the maximum MCC value.
9.  With the IQ factor on the x-axis, plot the average observed target and bootstrapped classifications for a set of probability thresholds (including that which maximizes MCC). Use the plot to build intuition on the maximum MCC value.
10. Repeat all prior steps for each of the 24 training datasets and formulae.
11. Summarize the maximum MCC values across all reproduced TBC models.

References
----------

[1] Herrnstein and Murray. (1994). The Bell Curve. New York: The Free Press.

[2] Krenz, C. (2007, August 8). Anatomy of an Analysis. Retrieved from <http://www.claudiax.net/bell.html>

[3] Shmueli, G. (2010). To Explain or To Predict? Statistical Science, 25(3), 289–310.

[4] Heckman, J. (1995). Lessons from The Bell Curve. Journal of Political Economy, 103(5), 1091–1120.

[5] Bureau of Labor Statistics, U.S. Department of Labor. National Longitudinal Survey of Youth 1979 cohort, 1979-2012 (rounds 1-25). Produced and distributed by the Center for Human Resource Research, The Ohio State University. Columbus, OH: 2014.

[6] Herrnstein and Murray (1994). Nation.txt, Nation.hdr, and 2TBC\_Documentation.ascii. Retrieved from <http://www.rasmusen.org/xpacioli/bellcurve>
