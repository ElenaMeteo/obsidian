*Zied Ben Bouallègue*, [lien vers l'article](https://doi.org/10.1175/MWR-D-19-0323.1)

#observation #representativity #error #precipitation #nonmodifiable

### Abstract

Spatial variability of precipitation is analyzed to characterize to what extent precipitation observed at a single location is representative of precipitation over a larger area. Characterization of precipitation representativeness is made in probabilistic terms using a parametric approach, namely, by fitting a censored ==shifted gamma distribution to observation measurements==.

>[! Note]
>This is the distribution we are choosing in our study of radon [[Pilote analyse du radon]].

Uncertainty associated with the scale mismatch between forecast and observation is accounted for by applying a perturbed-ensemble approach before the computation of scores. Verification results reveal a large impact of representativeness error on precipitation forecast reliability and skill estimates.

### Introduction

In forecast verification, skill estimates can differ substantially when the forecast is compared against its own analysis field or against point observations. The presence of representativeness error contributes to skill estimate differences. 
A reliable ensemble forecast ensures statistical consistency between the dispersion of the ensemble (which represents the forecast uncertainty) and the forecast error with respect to the observations. If observation errors are not accounted for during the ensemble verification process, then the investigator may draw inappropriate conclusions about the quality of the prediction system.
To account for observation uncertainty in the ensemble verification process, observation errors must first be characterized. This characterization is one objective of this paper with a focus on **precipitation.**

The representativeness of precipitation observations can be described in probabilistic terms as the relationship between observations at two different spatial scales. Statistical models are used to estimate the properties of *precipitation representativeness error* and its peculiar characteristics: ==a probability distribution with a long tail and an uncertainty that grows with precipitation intensity.==

> [!Note]
> Description of how the precipitation distribution should look like. ==Why should uncertainty grow with precipitation intensity?==
> 
> **Spatial variability increases with intensity**  
> 	Light precipitation tends to be fairly uniform over large areas. But intense precipitation comes in localized bursts, with sharp gradients over short distances. A rain gauge might record 30 mm/h, while a station 2 km away records 5 mm/h or nothing. That spatial heterogeneity means the “true” area-average is harder to infer from a single point.
> 	
> **Scaling mismatch (point vs. area)**  
> 	Representativeness error is essentially about comparing a point measurement to an area average. When rainfall is weak and uniform, the mismatch is small. When it’s intense and patchy, the mismatch grows, so uncertainty increases with intensity.

The latter approach, which relies on a parametric model based on the gamma distribution, is employed in this study. 

### Parametric model 

The parametric model of variability on unrepresented scales consists of fitting a censored, shifted gamma distribution (CSGD). The gamma distribution is a two-parameter distribution, with scale parameter $k$ and shape parameter $\theta$. The shift of the gamma distribution associated with a left censoring to 0 allows us to better represent the probability of no precipitation. The skewness of the gamma distribution depends only on its shape parameter $\theta$. The two parameters $k$ and $\theta$ are related to the mean $\mu$ and standard deviation $\sigma$ of the gamma distribution by
$$k = \mu^2/\sigma^2\ \text{  and  }\ \theta = \sigma^2/\mu$$
The cumulative distribution function of CSGD (with left censoring at zero, denoted $\tilde{F}_{k, \theta, \delta}$) takes the form
$$\begin{equation}
		\begin{cases}
			\tilde{F}_{k, \theta, \delta}(y) = F_k(\frac{y+\delta}{\theta}), &\text{ for } y\geq 0\\
			0, &\text{ for } y<0
		\end{cases}
\end{equation}$$
where $F_k$ is the cumulative distribution function of *gamma distribution* with unit scale and shape parameter $k$, and with $\delta > 0$, the shift parameters that controls the probability of zero precipitation. 

The **CSGD** is fitted in the form of a conditional distribution for observed precipitation at one spatial scale, say $B$, given the observed precipitation at a larger scale, say $A$. More precisely, we are interested in the conditional probability $P(Y_B|Y_A)$.

We assume that this conditional distribution takes the parametric form described by a CSGD.
Exploratory analysis of the model sensitivity to the number of parameters suggests that five coefficients are required to describe this distribution of $Y_B$ accurately for the three studied datasets. Two coefficients ($\alpha_0$ and $\alpha_1$) are associated with the mean of the distribution $\mu_B$:
$$\mu_B(y_A) = \alpha_0 +\alpha_1y_A,$$
which is a function of the observed precipitation at scale $A\ (y_A)$. Two other coefficients ($\beta_0$ and $\beta_1$) are associated with the standard deviation of the distribution $\sigma_B$:
$$\sigma_B(y_A) = \beta_0 +\beta_1 (y_A)^{1/2},$$
which is a function of the square root of the observed precipitation at scale $A\ [(y_A)^1/2]$.  The fifth coefficient corresponds to $\delta$, which defines the shift associated with the CSGD.

The ==five distribution parameters== ($\alpha_0$, $\alpha_1$, $\beta_0$, $\beta_1$ and $\delta$) are estimated by minimizing the mean continuous ranked probability score ([[Concepts nécessaires#Score CRPS|CRPS]]) over a test sample. Optimization is performed using squared parameters to ensure that they are positive, and with $\alpha_0=0.1$, $\alpha_1=1$, $\beta_0=0.1$, $\beta_1=1$ and $\delta= 0.1$.

>[! Note]
>In our case we only have four since there is no probability of "zero precipitation" ($\delta$) in our radon study.

### PIT histograms

The validity of the parametric method described in the previous section is checked by means of probability integral transform histograms. 
We apply the following diagnostic procedure: we consider percentiles associated with the **CSGD** for each element of the test sample. Percentiles are derived for equidistant probability levels ranging from 5% to 95% with a 5% interval. The rank of the observations when pooled with the distribution percentiles is aggregated and reported on a histogram. PIT histograms are interpreted in the same way as rank histograms, where a histogram close to a uniform distribution indicates reliability. 
We com- pute the reliability index:
$$RI = \frac{1}{m}\sum_i^{m+1}|\zeta_i-\frac{1}{m+1}|,$$
where $m+1$ is the number of equally sized bins and $\zeta_i$ is the frequency of observations in the $i-th$ bin. $RI$ takes a minimum value of 0 when the system is perfectly calibrated. In addition, we assess reliability with an entropy measure
$$\psi = \frac{-1}{\log(m+1)}\sum_{i}^{m+1}\zeta_i\log(\zeta_i),$$
which takes an optimum value of 1 when the system is perfectly reliable and the sample size is infinite.

