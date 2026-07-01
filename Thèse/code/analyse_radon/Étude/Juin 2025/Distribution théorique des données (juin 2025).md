#python #distribution #fitting 
L'objectif de cette partie de l'exercice est de tracer les distributions de pics gamma observés dans chaque département et y attribuer une distribution théorique qui s'y ajuste avec les paramètres optimaux. 

Les [[Données]] utilisées sont filtrées par nombre de stations par département. Afin de limiter la convergence du système vers des solutions singulières, nous avons besoin d'une certaine quantité de données. Nous regardons donc les départements qui [[Données#Filtre 5 ou plus| contiennent 5 stations de mesure minimum]]. 

---
## Histogramme d'observations

On commence par tracer les histogrammes qui vont nous donner des idées sur les possibles distributions avec son allure. Voici deux exemples: département 10 et département 33.

![[dep10.png|446]]

![[dep33.png|447]]
Il semblerait qu'il s'agit d'une **distribution normale tronquée**.

---
## Fitting

Pour le vérifier on utilise le module `form scipy import stats`. Avec `scipy` on va utiliser une fonction appelée `distribution.fit()` qui va optimiser les possibles paramètres de la distribution essayée pour s'ajuster à nos données.

#### Normale tronquée
Il se trouve qu'en essayant  `truncnorm` les résultats étaient très mauvais puisque et donc l'optimisation ne marche pas comme on pouvait espérer. Voici nos deux exemples:

![[dep10_truncnorm.png|435]]

![[dep33_truncnorm.png|438]]

En cherchant des alternatives nos choix sont:
- normale
- weibull (min et max)
- gamma
- beta
#### Alternatives
Afin de voir quelle option optimisée s'adapte le mieux à nos données, on établit, dans un premier temps, un critère de décision dépendant de la vraisemblance: le coefficient [[Concepts nécessaires#AIC (Critère d’information d’Akaike)|AIC]]. On priorisera l'option qui aura le AIC le moins élevé. 

On différencie `weibull_min` et `weibull_max`, qui sont symétriques par rapport à l'axe $y$.

Pour le département 10, ainsi que pour une longue liste, la meilleure est `weibull_max`, et pas très loin derrière apparaît la distribution `gamma`. Voici le correspondant graphique:

![[dep10_all 2.png]]

Avec notre meilleure option nous avons un problème. Pour beaucoup d'autres départements elle propose un choix singulier. C'est à dire que la solution est mathématiquement correcte mais, physiquement, n'a aucun sens. Voilà donc un exemple au département 33:

![[dep33_all 2.png]]

Ce problème est difficilement évitable (un essai a été fait en faisant des petites altérations sur les données pour essayer d'avoir un truc qui converge vers la bonne solution), donc nous devons en choisir une autre.

Celle qui semble y correspondre le mieux est la distribution `gamma`. Mais vérifions cela auprès des autres départements. On va conter en quels cas la distribution `gamma` est meilleure que la `weibull_min` et inversement. Pour cela nous allons utiliser plusieurs [[Concepts nécessaires#AIC et BIC|critères]] afin d'avoir une vision globale sur nos possibilités.

#### Évaluation des alternatives
En tenant compte de `weibull_max` les distributions choisies sont précisées sur le tableau ci-dessous. Il ne faut pas oublier que les fois où `weibull_max` n'est pas choisie comme la meilleure c'est souvent car la solution choisie est dégénérée. C'est donc pour cela qu'on rajoute la colonne "nº invalides" de nombre de fois où chaque distribution a forcé une mauvaise solution.

| nº de dep en faveur | AIC | BIC | nº invalides |
| ------------------- | --- | --- | ------------ |
| `norm`              | 0   | 0   | 0            |
| `weibull_min`       | 8   | 8   | 0            |
| `weibull_max`       | 10  | 10  | 6            |
| `gamma`             | 15  | 15  | 0            |
| `beta`              | 1   | 1   | 0            |
D'une autre part, avec le tableau qui suit on regarde quelles sont les meilleures options dans le cas où `weibull_max`n'en soit pas une.

| nº de dep en faveur | AIC      | BIC      | Écart  AIC moyen à la meilleure (%) | Écart BIC moyen à la meilleure (%) | nº invalides |
| ------------------- | -------- | -------- | ----------------------------------- | ---------------------------------- | ------------ |
| `norm`              | 0        | 0        | 6.45                                | 6.41                               | 0            |
| `weibull_min`       | 7 (25%)  | 7 (25%)  | inf                                 | inf                                | 0            |
| `gamma`             | 25 (75%) | 25 (75%) | 0.27                                | 0.27                               | 2 (6%)       |
| `beta`              | 1        | 1        | 0.17                                | 0.2                                | 0            |
|                     |          |          |                                     |                                    |              |
On voit que `gamma` "absorbe" les meilleurs de `weibull_max` quand on enlève ce choix. Cela pourrait s'interpréter comme `gamma` étant le second meilleur choix. On voit aussi que `gamma`est meilleure que `weibull_min` dans le $75\%$ des choix. Néanmoins, `gamma`étant plus stable que `weibull_max`, a quand-même un $6\%$ de cas où on obtient une approximation dégénérée. D'un autre côté, `beta`n'est jamais la meilleure mais par les écarts moyens à la meilleure on en déduit qu'elle est vachement proche de gamma, et ne donne pas des solutions dégénérées.

--- 

Voici les données des deux départements concernés en incluant les paramètres choisis par le code qui devraient optimiser notre choix:

`dep: 10`
`moyenne y: 2.1749332067980793`
`ect y:1.642925089111859`
norm
`params best: (np.float64(2.0721091154090723), np.float64(1.2218049940198186))`
weibull_min
`params best: (np.float64(1.862770001686025), np.float64(-0.1254318489707245), np.float64(2.471946965734392))`
weibull_max
`params best: (np.float64(53.933922259844465), np.float64(54.04094266182477), np.float64(52.511502968105475))`
gamma
`params best: (np.float64(4.982744856033504), np.float64(-0.6276765539253075), np.float64(0.5418277576693051))`

`dep: 33`
`moyenne y: 2.036231840612671`
`ect y:1.5170828265611498`
norm
`params best: (np.float64(1.94060153686678), np.float64(1.1116331240435484))`
weibull_min
`params best: (np.float64(1.933064614754754), np.float64(-0.12904789223721821), np.float64(2.331195996068466))`
weibull_max
`params best: (np.float64(0.5526036059439197), np.float64(7.259998321533205), np.float64(1.3028098025135924))`
gamma
`params best: (np.float64(6.776633077905256), np.float64(-0.900908360587237), np.float64(0.4193101695546468))`


Maintenant qu'une distribution peut être établie, il faut trouver un moyen de faire dépendre ses paramètres du maillage. Cela est fait sur le document [[Présence erreur de représentativité]].

--- 
### Correction de concepts

Une remarque a été faite sur la réunion de suivi du [[Carnet de Bord#**06/05/2026**|06/05/2026]] par Arnaud. On ne peut pas traiter les données comme on l'a fait, il faut prendre une autre approche. On peut juste faire confiance aux observations dés qu'il y a un événement parce qu'on ne sait pas modéliser le bruit de fond. Afin d'avoir assez de données pour faire l'analyse, on va d'abord élargir le domaine, puis le rang de temps. Jusqu'à maintenant on travaillait exclusivement sur les observations de juin et on séparait par département.
#### Toute la France
On va regarder la distribution de pics sur toute la France et on va refaire des fittings sur ces données. 
>[!Possibilité de test]
>Si les données totales sont cohérentes avec la "somme" des distributions par département, peut être que cela serait un bon moyen de tester et valider l'ensemble des données (pas seulement celles où un pic est détecté).

On va faire des essais avec plusieurs paramètres: 
- Toutes les données de [[Données#Summary all peaks|Summary_all_peaks.csv]]

![[all_peaks_france 1.png|390]]

- Filtre qui impose obs > 10 pour enlever les faux positifs

![[all_peaks_france_filtre.png|393]]

- Filtre qui impose `obs > 10` ou `obs > 10 - tol_obs si simu > 10 + tol_simu`. Pour celui-là on a fixé `tol_obs = 3` et `tol_simu = 2`. C'est à dire qu'on peut aller jusqu'à `obs = 7` tant que la simulation est au moins `simu = 12`. 

![[all_peaks_france_filtre_tol.png|398]]

La distribution `gamma`semble être encore le meilleur compromis. 