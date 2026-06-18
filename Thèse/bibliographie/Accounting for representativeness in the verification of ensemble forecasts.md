*Zied Ben Bouallègue*, [lien vers l'article](https://doi.org/10.1175/MWR-D-19-0323.1)

#observation #representativity #error #nonmodifiable

### Abstract

Characterization of representativeness error is made in probabilistic terms using parametric approaches, namely by fitting a normal, a truncated normal and a censored shifted gamma distribution to observation measurements for these three variables of interest: spatial variability of ==2m temperature, 10m wind speed==. Uncertainty associated with the scale mismatch between forecasts and observation is accounted for by applying a perturbed ensemble approach before the computation of scores.

> [!meaning]
> By $2$m and $10$m he means the height over the ground. We are therefore looking how these variables behave at a particular height (which makes sense because otherwise we would be comparing peras con manzanas) and daily precipitation.

### Introduction

The scale mismatch between in-situ observations and gridded numerical weather prediction forecasts is called *representativeness error* and is a challenge to be addressed in a number of applications. In forecasts verification, skill estimates can differ substantially when the forecast is compared against ==its own analysis field or against point-observations==. The presence of representativeness error in the latter case contributes to skill estimate differences. **Accuracy of the forecast in the short range approaches the accuracy of observation measurements.**

>[!Note]
>This happens because of the structure differences. When we compare with our own analysis, data structure in both the prediction and the analysis is the same since both come from the same algorithm. On the other hand, if we use an observation we frequently find difficulties to put the data at the same scale (different netting, component measured, etc).

Accounting for observation errors can have a large impact; in particular, when focusing on forecast reliability. In order to account for observation uncertainty in the ensemble verification process, we have first to characterize *observation errors*. Observation errors are the sum of *measurement errors* and *representativeness errors* (RE). In the following, we focus on **RE** which is assumed to be the dominant contribution to observation errors associated with ==synoptic station (SYNOP)== measurements in our applications. RE can be described in probabilistic terms as the \textbf{relationship between observations at two different spatial scales.}

> [!Note]
> They are meteorological official stations. They are usually managed by national meteorological services, which give standardized observations under the international code agreement SYNOP.

==The estimated uncertainty associated with station measurements== is then used in the process that compares ensemble forecasts against SYNOP observations. We follow here the so-called perturbed ensemble method which consists in adding observation uncertainty to the forecasts. Perturbations drawn from appropriate parametric distributions are added to each ensemble member before computing probabilistic scores and diagnostic measures. The impact of accounting for observation representativeness on ensemble verification results is assessed and discussed.

> [! Note]
> While we are trying to estimate a distribution that models RE observation error, measurement errors are compensated by a perturbation following a determinate distribution.

### General methodology

##### Data

The spatial coverage differs for each variable and from one day to another. Note that for precipitation, the focus is exclusively on an accumulation period of $24$h. The amount of RE will depend on the precipitation accumulation period, with less RE expected for longer accumulation periods than for shorter ones.

##### Parametric models

For each variable, we propose a parametric model that aims to capture the variability on unrepresented scales. Inspired by ==existing ensemble post-processing== methods, we consider the following probability distributions:
- a normal distribution for $2$m temperature,
- a truncated normal distribution for 10 m wind speed,
- a censored shifted gamma distribution for daily precipitation.

> [!note]
> Ensemble post-processing refers to statistical techniques applied after a weather ensemble forecast is produced in order to correct systematic biases, correct miscalibrated spread, adjust the forecast distribution to better reflect reality, or model sub-grid variability. Why does he choose these distributions?
> - **Temperature**: Temperature distributions over short time windows are usually: approximately symmetric, bell-shaped, not bounded by zero. So a normal distribution fits very well.
> - **Wind**: Wind speed has important characteristics: it cannot be negative, meaning that it needs truncation at $0$. Empirical distributions are often slightly skewed but not extremely. EMOS literature frequently uses truncated normal or log-normal.
> - **Rain**: Precipitation distribution has: many zeros (no rain); when it rains, values follow a positively skewed long-tailed distribution *(asimetría positiva: La mayor parte de los valores son pequeños (por ejemplo, lloviznas o poca lluvia). Pocos valores son muy grandes (tormentas fuertes). Gráficamente, es una distribución donde el “cuerpo” está a la izquierda y hay una cola larga hacia la derecha)*; and
intensity variance increases with mean.

Each parametric distribution is fitted in the form of a ==conditional distribution==  for an observed quantity at one spatial scale, say B, given the same quantity aggregated over a larger scale, say A. More precisely, we are interested in the conditional probability:
$$\mathbb{P}(Y_B|Y_A),$$
which is the probability of the random variable $Y_B$, representing the observation at a smaller scale, given the random variable $Y_A$, representing the observation at a larger scale.
The aim is to characterize the relationship between averaged values over an area A and point measurements at B, where B is a point within the area A. This characterization will define the RE associated with point observations (such as SYNOP measurements) and used later in forecast verification.

>[!note]
>We are treating with a conditional probability over the scale of the observed quantity. This means that the condition is taking the representativeness of the quantity into account rather than an event. The article wants to model how a mean value over a big area (like $40$$km^2$) behaves with respect to a mean value over a smaller area or a point (like a SYNOP station inside this grid).

>[!Example]
>Suppose that a numerical weather prediction model provides an area–averaged temperature over a grid box of $40\,\text{km} \times 40\,\text{km}$. Let this large–scale average be denoted by $$Y_A = 20^\circ\mathrm{C}.$$ 
>
>There is a SYNOP station located somewhere inside this grid box. However, the station does not measure exactly $20^\circ\mathrm{C}$. Instead, its measurement may deviate from the area average due to local effects such as terrain and topography, shading and land cover, proximity to the sea, valley orientation or local channeling, urban or vegetation effects. 
>
>The goal of the study is to characterize statistically how the point measurement $Y_B$ may differ from the area–averaged value $Y_A$. Thus, we ask the following question: Given that the area-averaged temperature is $20^\circ\mathrm{C}$, what is the expected conditional distribution of temperatures that a point station might observe?
>
>For example, the representativeness model may specify that $$Y_B \mid (Y_A = 20^\circ\mathrm{C})\sim \mathcal{N}(\mu = 20,\ \sigma = 1.4).$$

>[!Summary]
>In order to be able to build a model out of the observation error we establish the family to which our distribution belongs. And we are not describing directly the error, we are looking at a conditional probability depending on observations and their mean value. This way, the families established make sense because the distributions should belong to the same families as the forecasts and the obtained value describes the probability of certain differences, which will be computed by pairs ($y_A, y_B$).

##### Model fitting

Point measurements are compared with areal averages of observations. Each parametric model is fitted with ==the pairs== $(y_A,y_B)$, which form our sample, in order to describe in probabilistic terms the relationship between these two quantities, where $y_A$ is computed averaging \textit{at least} \footnote{Increasing this number has a little impact on the final results}{five} values inside the area $\Delta_A$. 

> [!question]
> What's the difference between these variables and $(Y_A, Y_B)$?
> - **Uppercase letters** $=$ random variables. These represent unknown quantities described by probability distributions. $Y_A =$ the random variable representing the value of the quantity at area scale $A$ (e.g., the grid-box average temperature). $Y_B =$ the random variable representing the value of the quantity at point scale $B$ (e.g., the point measurement at a station).
> - **Lowercase letters** $=$ realized values (observations or computed values). These represent actual numbers from data. $y_A =$ the computed average from high-density observations inside area $A$ (e.g., the area-mean computed from $\geq 5$ stations). This is an estimate of the unobserved true $Y_A$. $y_B =$ the actual single point observation at $B$. This is one realization of the random variable $Y_B$. Lowercase = after observing, deterministic.


The parameters are estimated for a ==range of neighborhood sizes==. The distribution parameters are described as a function of the size of the averaging area $\Delta_A$.

>[!note]
>They try many different values of $\Delta_A$ to see how variability changes with scale. Later, during forecast verification: They plug in the actual grid size of $ECMWF$ $ENS$ (e.g. $\Delta_A=18 km$).

The distribution parameters are estimated by minimizing the mean continuous ranked probability score ($CRPS$) over a test sample. ==CRPS== is defined for a distribution $F(y_A)$ and an observation $y_B$ as follows:
$$CRPS = \mathbb{E}_X|X-y_B|-\frac{1}{2}\mathbb{E}_{X,X'}|X-X'|,$$
where $X$ and $X'$ are independent random variables drawn from the corresponding parametrized distribution.

> [!note]
> The Continuous Ranked Probability Score ($CRPS$) measures how good a probabilistic forecast is. A probabilistic forecast gives a distribution $F$ for some quantity (here: the representativeness model gives a distribution of $Y_B$ conditional on the areal average $y_A$). We also have a real observed value $y_B$. $CRPS$ is smaller when the forecast distribution is concentrated near the observation and larger when it is spread out or misplaced.

> [!summary]
> Under our context we want to be able to compare an ensemble prediction $Y_A$ which is given as a mean value at a determined area $\Delta_A$ with a punctual observation $y_B$ coming from a national station. Before using the ensemble, we need to create a model that tells us: If we have this mean value $X$, how are the observation stations distributed? 
> 
> In order to do that, we take *high density observations* (meaning that there's a lot of punctual observations in a single area over which we are going to compute the mean value with our model) and we create the pairs $(y_A, y_B)$. Given an area $\Delta_A$, we take all stations inside that area and computing their mean value, we obtain $y_A$. Furthermore, we will take each station individually as $y_B$. This means that if in $\Delta_A$ we have seven stations, we will have seven pairs ($y_A, y_B$) where $y_A$ stays the same in all of them and $y_B$ corresponds to the value of each station by itself. We want to find a probabilistic distribution that describes the probabilities of getting each value knowing the average distribution $$\mathbb{P}(Y_B|Y_A = y_A).$$ 
> 
> For each parameter there is already a chosen family of distributions which make sense with the parameter's dynamic, so we only need to compute the parameters to define in the family in question (average and spread for the normal distribution, for example). These distribution parameters are determined by minimizing the $CRPS$ over a test sample. This score depends on the variability with respect to $y_B$ (since there's the term $|X - y_B|$), and on the variability of the distribution itself (since there's the term $|X - X'|$). The parameters will be a function of the main parameter of our problem: the area $\Delta_A$. It makes sense because since we are looking square by square, the bigger the square, the bigger the variance between punctual observations.

##### Model validation

The validity of the parametric models is checked by means of ==Probability Integral Transform histograms==. The following diagnostic procedure is applied: we consider percentiles associated with the parametric distributions of the test sample. Percentiles are derived for equidistant probability levels ranging from $5\%$ to $95\%$ with a $5\%$ interval. The rank of the observations when pooled with the distribution percentiles is aggregated and reported on a histogram. PIT histograms are interpreted similarly as rank histograms, where \textbf{a histogram close to a uniform distribution indicates reliability}.

> [!note]
> The Probability Integral Transform (PIT) histogram is used to assess whether a parametric predictive distribution is statistically consistent with the observations. For each case, the following procedure is applied: 
> 1. The parametric model associated with the averaged quantity $y_A$ produces a set of percentiles $p_{5}, p_{10}, p_{15}, \ldots, p_{95}$. 
> 2. These percentiles are pooled together with the corresponding point observation $y_B$. Thus, we form the set $S = \{ p_5, p_{10}, \ldots, p_{95}, \, y_B \}$. 
> 3. The set $S$ is sorted in ascending order and the \emph{rank} of $y_B$ within this ordered list is determined. The relative position of $y_B$ corresponds to the PIT value for that case. 
> 4. The PIT values obtained for all cases are aggregated into a histogram. Since each PIT value lies in $[0,1]$, the histogram bins represent intervals of the predictive CDF. The vertical axis of the PIT histogram represents the \emph{relative frequency} (or proportion) of observations falling into each bin. A perfectly calibrated model yields a uniform PIT histogram. 
> Normally we directly use the CDF, but since our goal is to compare them with punctual observations, we rather discretize the set on quantiles.
> 
> **Numerical example**: Assume that, for a given day, the parametric model generates the following percentiles:  $$p_{10} = 5, \qquad p_{30} = 10, \qquad p_{50} = 12, \qquad p_{70} = 14, \qquad p_{90} = 20.$$
> The corresponding point observation is $y_B = 12$. 
> Step 1: pooled set $S = \{5,\, 10,\, 12,\, 14,\, 20,\, y_B = 12\}$. 
> Step 2: sort the values $S_{\text{sorted}} = \{5,\, 10,\, 12,\, 12,\, 14,\, 20\}$.
> Step 3: determine the rank of the observation. The observation $y_B = 12$ appears in positions $3$ and $4$ of the six-element list. Its average rank is therefore $$\text{PIT} = \frac{3.5}{6} \approx 0.58.$$  
> In a binned PIT histogram, this PIT value would be counted in the bin covering approximately the $55\%$--$60\%$ interval.
> 
> **Interpretation of the Vertical Axis:** Let $N$ be the total number of observation forecast pairs and let $n_k$ denote the number of PIT values falling into bin $k$. The height of the $k$th bar in the PIT histogram is then $\frac{n_k}{N}$, that is, the *relative frequency* (between $0$ and $1$) of observations whose PIT values fall inside that bin. A well-calibrated predictive distribution produces a histogram close to a uniform distribution. U-shaped histograms indicate under-dispersion (the model distribution is too narrow), while hump-shaped histograms indicate over-dispersion. Skewness in the histogram reveals systematic bias.

In addition, PIT histograms are generated separately for ==sub-samples of the validation data-sets==. Stratification is based on the value of the area averaged quantity $y_A$ . Stratified PIT histograms are produced for equi-populated categories (using terciles) corresponding to cases with low, intermediate, and high $y_A$ values. **Stratified PIT histograms help diagnose potential limitations of the parametric models.**

>[!note]
>Taking sub-samples of the data-set, we can evaluate the same things for different averages. We don't use new data, we take some of the values we already used so that we can validate the model for other values on average. We do it because a model can be validated with great results using intermediate values, and not having so much performance for lower or higher values. This allows us to check that our model will be on point for any meteorological possibility.}

Finally, we perform a visual inspection of ==Quantile-Quantile==. $(Q-Q)$ plots for random draws of the parametric distribution and a set of points observations. $Q-Q$ plots help diagnosing whether the two sets (model draw and point observation) are drawn from the same marginal distribution.

> [!note]
> Quantile-Quantile is a graphic tool that allows us to compare two distributions and quantifies their similarity by pairing up their quantiles.

>[!summary]
>   After fitting our model with the best possible parameters (with respect to the $CRPS$), we need to evaluate if it is adequate to our problem. Indeed, choosing the parameters that minimize our reference score, doesn't automatically imply that our model fits our situation well enough. Additionally, we have been faced into the choice of a family of distributions, which we might be able to improve. In general, the chosen family has a sense and a logic, but who knows. In order to validate the resulting model, we use PIT histograms, which show us the variance between the punctual observations $y_B$ with respect to the average $y_A$. We also evaluate the similarities between the theoretical (from the model) and true (from the observations) distributions using $Q-Q$.

##### Perturbed ensemble approach

>[!context]
>    Now that we have a determined and validated error distribution, we describe one way to use it in order to take into account the observation error into our score. This is not our personal goal but remains a good example and has good results. 

In order to account for RE in the **verification process of ensemble forecasts**, we apply the so-called perturbed-ensemble approach which consists in ==convolving== the forecast and observation error distributions. This approach leads to scoring rules that favor forecasts of the truth, and it is therefore recommended as a generic method to be applied in the presence of observation errors.

> [!note]
> Doing this we perturb the observations following the distribution of the error computed. For example, if we are initially treating a normal distribution $\mathcal{N}(0, \sigma^2)$, this step converts it into $\mathcal{N}(0, \sigma^2 + s^2$), where $s^2$ is the error's variance. This makes the score proper with respect to the truth but still has a bias. 

Each ensemble member gets assigned a random value drawn from the fitted parametric distribution whose scale and shape parameters are a function of the original forecast value: the distribution is centered over the forecast value and its spread accounts for representativeness uncertainty.

>[!summary]
>    We perturb our predictions randomly following the observation error distribution. Both distributions belong to the same family. It's not mandatory but it makes sense that the observations follow the same type of distribution as the forecast.

