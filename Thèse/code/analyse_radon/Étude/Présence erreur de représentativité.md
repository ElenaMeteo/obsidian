#paramètres #fitting #distribution #erreur #representativity #representativité 

Notre objectif dans ce document est de justifier la présence d'erreur de représentativité et donc sa correction sur le document [[Paramètres de la distribution (bases)]]. Tout cela est basé sur la méthode utilisée sur l'article [[Accounting for representativeness in the verification of ensemble forecasts]]. 

Dans cet article, Zied utilise les "carreaux" définis avec le maillage en tant que zones d'intérêt. Une fois les zones établies, les valeurs observées dans chaque station appartenant à la zone sont comparées avec les valeurs observées moyennes de la zone. C'est à dire que, donné un temps $t$, supposons que  notre zone $A$ appartenant à un maillage de longueur $\Delta$, contient les stations $\{B_1,...,B_n\}$. Soient $\{y_{B_1},...y_{B_n}\}$ les observations correspondant à chaque station et $y_A$ la moyenne de ces dernières. Notre but est de donner une description du comportement de chaque valeur $y_{B_{i}}$ par rapport à $y_A$. On va donc étudier la déviation individuelle de chaque station par rapport à la moyenne. Cela sera réalisé pour plusieurs maillages différents afin de faire explicite la dépendance de la justesse des résultats au maillage. 

### Données

Cette étude a été réalisée avec les [[Données|données]] de juin 2025. On a du changer cette base là afin d'élargir nos possibilités ([[Carnet de Bord#**06/05/2026**|à cause de ça]]). Cela change forcément les résultats donc on réaliser la même étude éventuellement pour les données de 2023/2024.

### Stations et maillage

Considérons un maillage régulier de longueur $\Delta$ sur l'ensemble de la France, où les coordonnées extrêmes de début de maille sont déterminées par les stations les plus éloignées du centre (par exemple, la latitude la plus haute est déterminée par la station le plus au Nord). 

Une fois on possède les coordonnées du maillage et de toutes nos stations de mesure, il faut regarder quelles zones de ce maillage contiennent 5 stations ou plus (afin d'avoir une moyenne pertinente).
Pour le faire on utilise la [[Concepts nécessaires#Méthode Ray-Casting|méthode ray-casting]] qui sert à évaluer si un point est à l'intérieur d'un un polygone. Et donc, pour chaque maille on va parcourir toutes les possibilités et compter combien de stations sont à l'intérieur.
Si y en a plus de 5, on fera un graphique de valeurs en comparant $y_A$ avec les correspondants  $y_B$. Cela va nous donner une façon de voir comment les valeurs individuelles sont distribuées autour de la moyenne, qu'on va transmettre au modèle, grandeur qui nous donne une idée de **l'erreur de représentativité**.

### Choix des $y_B$

Afin d'avoir une représentation de nos choix sur les $y_B$ on va réaliser des graphiques qui montrent la relation entre ceux là et son correspondant $y_A$. Le $y_A$ sera en abscisses et les $y_B$ en ordonnées. Plus la distribution se rapproche de l'identité, le mieux c'est. 

Pour cela on a eu a fixer une maille. Nous avons choisi $\Delta = 20$. 

La première chose essayée a été $y_B$ = {*les observations par heure d'une même journée pour une même station*}. Cela nous donnait donc un $y_A$ pour 24 $y_B$. Le graphique résultant a été:

![[yAyB_20_24.png|358]]

Après avoir remarqué que Zied le faisait avec beaucoup moins de stations, on a changé à un $y_A$ pour 3 $y_B$. Cela a donné le graphique suivant: 

![[yAyB_20_3.png|358]]

Remarquons que le deuxième est beaucoup plus "juste", en tenant compte que **l'identité est l'idéal**. Ça peut s'expliquer par le fait que, moins y a de valeurs, moins probable c'est qu'il y en ait une qui sorte vraiment de la norme. 

Après réflexion on s'est rendu compte que, ce qui était pertinent pour estimer l'erreur de représentativité, était pas de regarder la moyenne entre valeurs de la même station mais entre **toutes les stations qui sont dans la maille**.  Et donc, en gardant toujours le même $\Delta$ , on a réalisé le même graphique où le groupe de $y_B$ est formé par les valeurs de toutes les stations à la même heure. Si dans une maille il y a $n_m$ stations de mesure (avec $n_m>5$), chaque $y_A$ est une moyenne de $n_m$ valeurs. Le total de $y_A$ tracés sur le graphique est de $24\times 30\times N$. 24 parce qu'on en fait a chaque heure du jour, 30 parce qu'on regarde du 1er juin au 30 juin et N c'est le nombre de mailles contenant cinq stations de mesure ou plus.

Voici le graphique obtenu avec cette configuration:

![[yAyB_20_1h_all2.png|358]]

Malgré l'allure légèrement diagonale, ce graphique nous montre que l'erreur de représentativité est présente sur notre problème

Maintenant voyons comment agissent nos données en faisant varier le $\Delta$. Il est normal d'observer une augmentation des points quand on l'augmente. En effet, y aura sans doute plus de mailles pour lesquelles y aura cinc stations de mesure ou plus. 

Voici le graphique pour $\Delta = 40$km:

![[yAyB_40_1h_all.png|362]]

*Avec les données de 2023/2024*
![[yAyB_40_all_2324 2.png|364]]

Et enfin, le graphique pour $\Delta = 60$km:

![[yAyB_60_1h_all.png|364]]

On remarque une certaine similarité à des différentes échelles. 

### Quantifier l'erreur: MSE

D'une autre part, pour quantifier l'erreur dans chaque cas, on a calculé le MSE moyen pour chaque delta en suivant les formules suivantes:
$$MSE_{all}(\Delta) = \frac{1}{N_{A}}\sum_{i=1}^{N_A} MSE(y_{A_i})$$
$$MSE(y_{A_i}) = \frac{1}{N_{B_i}}\sum_{j=1}^{N_{B_i}} MSE(y_{A_i}, y_{B_j})$$

Où: $N_A$ est le nombre de $y_A$ calculés et $N_{B_i}$ est le nombre de stations de mesure dans la maille $i$.

En faisant un premier test on obtient une valeur `nan` en résultat. Pour cette raison, on fait un `print` des MSE de chaque maille et on voit que y en a beaucoup, notamment au début de la liste, qui montrent que certains vecteurs de $y_B$ contiennent au moins une valeur `nan`.
Afin de voir comment ces `nan` se distribuent, on **compte combien on en retrouve** à la simple lecture des documents au moment de constituer le vecteur qui va déterminer les valeurs des $y_B$ et donc des $y_A$.

>[!Résultats]
>`Nombre de mailles avec au moins 5 stations: 4`
>`Nombre de nan sur gama_obs pour la maille maille_12_40: 0`
>`Nombre de nan sur gama_obs pour la maille maille_30_2: 48`
>`Nombre de nan sur gama_obs pour la maille maille_32_27: 893`
>`Nombre de nan sur gama_obs pour la maille maille_33_27: 0`

Il y a clairement un problème de lecture ou de constitution de données. 
Globalement nos résultats avaient quand-même du sens, donc on va filtrer les valeurs `nan` et **calculer le $MSE$ global** avec ce qui reste. 

> [!Résultats]
$\Delta = 20km$: 
`Nombre de mailles avec au moins 5 stations: 4`
`Erreur quadratique moyenne totale: 2.7719`
$\Delta = 40km$: 
`Nombre de mailles avec au moins 5 stations: 27`
`Erreur quadratique moyenne totale: 1.5907`
$\Delta = 60km$: 
`Nombre de mailles avec au moins 5 stations: 28`
`Erreur quadratique moyenne totale: 1.5522`

Il semblerait qu'il y a une tendance décroissante sur l'erreur au fur et à mesure qu'on augmente $\Delta$. Ce phénomène a forcément une limite donc on va tester d'autres $\Delta$'s jusqu'à la voir.

>[!Résultats]
>$\Delta=80km$: 
>`Nombre de mailles avec au moins 5 stations: 27`
>`Erreur quadratique moyenne totale: 1.7266`
>$\Delta = 100km$: 
>`Nombre de mailles avec au moins 5 stations: 21`
>`Erreur quadratique moyenne totale: 1.6561`

On voit déjà une variation. Voici donc un tableau avec plus d'éléments:

| $\Delta$ (km)  |   20   |   30   |   40   |   45   |   50   |   55   |   60   |   70   |   80   |   90   |  100   |
| -------------- | :----: | :----: | :----: | :----: | :----: | :----: | :----: | :----: | :----: | :----: | :----: |
| > 5 stations   |   4    |   19   |   27   |   27   |   26   |   26   |   28   |   24   |   27   |   22   |   21   |
| erreur moyenne | 2.7719 | 1.7384 | 1.5907 | 1.6199 | 1.5388 | 1.6077 | 1.5522 | 1.7179 | 1.7266 | 1.8285 | 1.6561 |

Le $\Delta$ optimal semblerait être proche de $\Delta=50km$.

![[delta_MSE.png|564]]

---
---
---

### Données 2023/2024

| $\Delta$ (km)  | 20  | 30  | 40  | 45  | 50  | 55  | 60  | 70  | 80  | 90  | 100 |
| -------------- | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| > 5 stations   |     |     | 27  |     |     |     |     |     |     |     |     |
| erreur moyenne |     |     |     |     |     |     |     |     |     |     |     |

Voici le graphique scatter-plot des données de 2023/2024 en considérant seulement les données où il y a au moins une station de la grille où un pic a été pbser