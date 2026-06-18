Notre objectif avec cette partie est de déterminer les paramètres de la [[Distribution des données]] que nous traitons en fonction du maillage. Pour le faire, la méthode exposée sur [[Accounting for representativeness in the verification of ensemble forecasts]] est mise en place. 

--- 

Dans ce document on pose les bases de notre méthode pour estimer les paramètres de notre loi en fonction de la maille.

--- 

### Choix de la loi 

Après l'analyse statistique faite sur la [[Distribution des données]], nous avons quelques candidats pour établir la loi suivie par les données de Radon 222. Celles qui s'y ajustent le mieux sont la loi *gamma* et la loi *beta*. 
La loi gamma est plus précise mais un peu moins stable que beta. Le choix va aussi dépendre de la difficulté du fitting des paramètres. Si y en a une beaucoup plus simple on va la favoriser. Ça dépend aussi des études faites précédemment.
Dans l'article "[[Accounting for representativeness in the verification of ensemble forecasts]]", Zied attribue la loi gamma au cas de la pluie. En sachant qu'on se base sur cet article c'est possible qu'on privilégie cette-là, étant les raisons scientifiques legitimes.

---
### Modification de la loi

Dans l'article sur lequel on se base, ils n'utilisent pas une gamma tel-quel, mais une "*shifted*, *censored* gamma distribution". Cela implique un changement au niveau de la distribution. Nous partons de $$Y\sim\text{Gamma}(k,\theta),$$ on réalise un déplacement (shift) sur la loi
$$X = Y + \gamma,$$
puis, on applique la censure (on ne sait pas les valeurs des observations exactes, on sait seulement si les valeurs sont plus grandes ou plus petites qu'une certaine constante, différente pour chaque cas). 

---
### Paramètres

Les paramètres de la loi gamma sont $k$ et $\theta$. Les deux dépendent de la moyenne $\mu$ et l'écart-type $\sigma$: $$k = \mu^2/\sigma^2\ \text{  et  }\ \theta = \sigma^2/\mu.$$
Au moment de programme la distribution du CSGD on va enlever la dépendance au paramètre $\delta$ puisque notre situation de dispersion de radon ne le concerne pas ($\delta$ étant la probabilité qu'il n'y ait pas de précipitation). La distribution qu'on cherche est donc:
$$\begin{equation}
		\begin{cases}
			\tilde{F}_{k, \theta}(y) = F_k(\frac{y}{\theta}), &\text{ for } y\geq 0\\
			0, &\text{ for } y<0
		\end{cases}
\end{equation}$$
où $F_k$ est la distribution cumulée de la fonction CSGD.

Le modèle paramétrique auquel on va s'adapter est défini par les équations et paramètres suivants: $$\mu_B(y_A) = \alpha_0 +\alpha_1y_A,$$$$\sigma_B(y_A) = \beta_0 +\beta_1 (y_A)^{1/2},$$ Avec une initialisation telle que: 
$$\alpha_0=0.1,\ \alpha_1=1,\ \beta_0=0.1,\ \beta_1=1$$
---
### Méthodes pour trouver les paramètres

- [ ] Librerie de python: `from scipy.optimize import curve_fit`
- [ ] Petite ia

Les paramètres sont initialisés.
On part d'une valeur de $y_A$ pour déterminer $\mu$ et $\sigma$. Avec cela on aura une loi gamma. On tire au sort sur cette loi et on évalue avec le [[Concepts nécessaires#Score CRPS|score CRPS]]. Une fois on a les résultats, on recommence avec des nouveaux paramètres. 



