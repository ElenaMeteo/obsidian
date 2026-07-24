Notre objectif avec cette partie est de déterminer les paramètres de la [[Distribution théorique des données (juin 2025)]] que nous traitons en fonction du maillage. Pour le faire, la méthode exposée sur [[Accounting for representativeness in the verification of ensemble forecasts]] est mise en place.  

Dans ce document on trouve l'étude pratique qui a été faite avec la méthode 

---
### Détermination des yB et yA

Cela est fait à partir de la théorie écrite sur [[Présence erreur de représentativité]], mais en mettant un filtre additionnel. Le travail sera réalisé avec les grilles qui ont cinq stations ou plus. Les stations contenues dans ces grilles seront regardées en parallèle. Les observations des stations seront sélectionnées seulement s'il est déterminé qu'au moins une d'elles a observé un pic. 
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

![[dist_yB_7.32_50_2324.png|349]]

![[dist_yB_46.29_50_2324.png|350]]

On voit bien que ces graphiques nous disent rien. C'est parce que les données pour faire ces distributions ont été mal choisies donc on a dû le refaire. Pour les faire on a rassemblé toutes les valeurs de yB, certes filtrées, mais non classifiées avec ses correspondants yA et on les a tracé. Au moment de faire le précédent histogramme on perdait la cohérence des données: sa correspondance. L'approche a donc été changée: On a fait des quantiles sur les valeurs de yA et pas de la totalité de yB, en gardant la relation entre ces yA et ses yB. 

Voici le résultat pour 4 quantiles:

![[dist_yB_all4q 1.png]]

Voici les résultats pour 10 quantiles:

![[dist_yB_all10q 1.png]]

On remarque une double modalité qui converge vers une seule. Cela a du sens sur les premiers quantiles (surtout quand on en traite beaucoup) parce qu'on est entrain de classer tous les yB rattachés aux petits yA. Un yA est petit quand il regroupe un grand (>10) yB et tous les autres sont très petits. Donc forcément on va retrouver un double pic de fréquences en traçant la pdf. On classe des nombreuses valeurs très petites (qui font descendre la moyenne de yA) et quelques valeurs assez grandes comme pour que le pic soit observé.

Ces graphiques ont beaucoup plus de sens que les précédents, ils ont tous l'air de suivre un peu près la même distribution, chose qui est très positive pour le déroulé de notre étude. N'empêche là il faut prendre quelques décisions par rapport au fitting de ces distributions:
- Combien de quantiles utilise-t-on? Cette quantité fera varier le nombre de graphiques qu'on obtient avec un "double événement"
- Quelles données utilise-t-on? Pour éviter l'effet double il serait pas mal d'utiliser l'intégralité des données sans les faire passer par le filtre. on se demande si y a vraiment un impact négatif sur les données si on fait ça. On met en doute la légitimité du filtre.

--- 
### Fitting
Pour l'instant on va tout faire avec 4 et 10 quantiles et avec les données filtrées. En cas de changement d'avis il faudra qu'on se débrouille pour garder la même structure de données.

Les distributions qu'on va essayer sont celles qui nous ont donné des bons résultats dans l'étude précédente: 
`norm, weibull_min, gamma` et `log-norm`. 

Voici les résultats des fittings avec 4 quantiles:

![[dist_yB_all4q_eval 1.png]]

Voici les résultats des fittings avec 10 quantiles:

![[dist_yB_all10q_eval 1.png]]

On voit que nos deux meilleurs candidats, `gamma` et `log-norm`,  explosent plusieurs fois. Cela complique la décision parce qu'il nous faut des variables stables pour chaque cas. 

En réalité si on réalise une régression linéaire avec nos résultats de moyenne et écart-type, peut être qu'on pourrait garder des cas conflictifs pour la validation: on les ajoute pas dans la régression mais on lit les valeus qui leur correspondraient et on regarde ce que donne ce résultat. 

Il est aussi possible de mélanger des quantiles et observer ce qu'il se passe, mais pour l'instant on va laisser cela de côté. Il est nécessaire de faire une étude statistique large comme on avait fait pour les données de Juin 2025 afin d'estimer si on réalise les fittings avec la double modalité ou si on adapte les distribution simples et existentes. 

Cela va dépendre aussi de la décision prise auprès du filtrage des données. 

L'étude réalisée afin de prendre la décision auprès de la distribution théorique et le maillage utilisés : [[Distribution théorique des données (2023 et 2024)]]

---
### Quel fitting?

On retrouve plusiers possibilités de fitting: simple automatique, simple manuelle ou double. Dans cette partie on va voir une comparaison des 3 résultats pour chaque quantile. Voici les charactéristiques théoriques de chacune:
- La simple automatique: la plus facile à faire, déjà programmée mais explose à des certains endroits. On a quand-même une possible solution pour les endroits où ça explose en forme d'interpolation des paramètres qui vont définir le fitting (shape, loc, scale).
- La simple manuelle: plus facile de corriger quand ça explose, mais on ne sait pas à quel point elle est précise par rapport à l'automatique. 
- La double: plus complexe, plus de paramètres et à voir si ça marche partout, mais potentiellement beaucoup plus précise.



---
### Paramètres de base déterminés

Maillage:
Quantile(s):
Mélange de plusieurs quantiles:  (oui/non)
Distribution théorique:
Méthode de validation avec les graphiques conflictifs: (oui/non)

---
### Tracer les moyenne et écart-type en fonction de yA
Une fois nous avons déterminé, pour chaque quantile, les correspondants paramètres $\mu$ et $\sigma$, on veut voir son comportement en fonction de yA artificiels crées avec les moyennes des bins de l'histogramme de quantiles.


![[mu_sigma_yA_gamma_2324.png|697]]