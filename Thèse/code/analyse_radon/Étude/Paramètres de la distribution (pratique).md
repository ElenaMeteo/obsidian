Notre objectif avec cette partie est de déterminer les paramètres de la [[Distribution des données]] que nous traitons en fonction du maillage. Pour le faire, la méthode exposée sur [[Accounting for representativeness in the verification of ensemble forecasts]] est mise en place.  

Dans ce document on trouve l'étude pratique qui a été faite avec la méthode 

---
### Détermination des yB et yA
Cela est fait à partir de la théorie écrite sur [[Présence erreur de représentativité]], mais en mettant un filtre additionnel. Le travail sera réalisé avec les grilles qui on cinq stations ou plus. Les stations contenues dans ces grilles seront regardées en paralléle. Les observations des stations seront sélectionnées seulement s'il est déterminé qu'au moins une d'elles a observé un pic. 
Ce cas se donne quand la station a une observation majeure que notre seuil où quand une simulation a été faite par dessus de `seuil + tol_simu` et que l'observation est par dessus de `seuil - tol_obs`. 
Dans un premier temps, les lignes contenant au moins une valeur `NaN`ont été supprimées. 

---
### Scatter-plot des yB en fonction des yA

Voici le scatter-plot résultant de la sélection de données précédente où nous avons mis en ordonnée tous les `yB` d'une même station et en abscisse, sa correspondante valeur `yA`.

Ce calcul a été fait pour $\Delta = 40km$.

![[yAyB_40_all_2324 4.png|697]]

On remarque que, en sachant nos conditions de sélection des données il y a trop de valeurs proches de 0. On va regarder plus en détail ces valeurs là. 

Tout d'abord, on a fait un `print` qui nous montre le nombre de valeurs `yA < 2` et `yA < 3`:
Nombre d'apparitions `yA < 2` : 1 
Nombre d'apparitions  `yA < 3` : 114

On a écrit tout le dictionnaire qui regroupe les valeurs yA et yB par maille et événement. On a mis un filtre aussi d'événements avec un yA < 2 et yA < 3 et toutes les possibilités ont été déterminées correctes. En effet pour le seul cas où `yA < 2`, on a à faire à une maille qui a 10 stations. Et pour les cas où `yA < 3`, on a un pic et 5/6 stations qui sont suffisantes pour faire tomber la moyenne en cas de non-détéction.

Les données sont donc validés par rapport à cette inquiétude. 
 
---
### Histogramme de fréquences régulier
Un histogramme de fréquences avec des colonnes équilibrées (fait en base au nombre de quantiles qu'on demande) est nécessaire afin de voir la répartition de la totalité des données yB qu'on traite. Notre but est de classer ces données la en groupes équilibrés associés à un certain intervalle de yA pour pouvoir faire un fitting et donc relier les paramètres d'une distribution de valeurs yB à la valeur yA. C'est à dire que nous serons en mesure de mettre $\mu$ et $\sigma$ en fonction de yA. 

Dans ce histogramme ce qui est pertinent à voir est l'ampleur de la colonne attribuée à chaque cas (vu que la hauteur est fixée en fonction du nombre de quantiles qu'on va demander).

Dans notre cas on a fixé `BINS_ALL_PEAKS = 20`.

![[hist_yB_all_20_2324 2.png|438]]


---
### Fitting des yB 
Maintenant que les yB ont été sépares en différentes catégories (correspondant à ses quantiles), on va tracer la distribution par quantile et essayer de faire un fitting avec, dans un premier temps, la distribution gamma.

Voici quelques exemples d'histogrammes (où on a fixé `BINS = 100`):

![[dist_yB_0.8_50_2324.png|347]]

![[dist_yB_2.71_50_2324.png|345]]

![[dist_yB_3.4_50_2324.png|341]]

![[dist_yB_5.38_50_2324.png|349]]

![[dist_yB_7.32_50_2324.png|353]]

![[dist_yB_9.55_50_2324.png|358]]

![[dist_yB_11.77_50_2324.png|365]]

![[dist_yB_14.5_50_2324.png|367]]

![[dist_yB_46.29_50_2324.png|374]]


---
### Tracer les paramètres en fonction de yA
Une fois nous avons déterminé, pour chaque quantile, les correspondants paramètres $\mu$ et $\sigma$, on veut voir son comportement en fonction de yA artificiels crées avec les moyennes des bins de l'histogramme de quantiles.



![[mu_sigma_yA_gamma_2324.png|697]]