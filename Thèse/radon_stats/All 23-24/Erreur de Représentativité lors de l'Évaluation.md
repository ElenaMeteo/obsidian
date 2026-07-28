Le but de cette étude est de prendre en compte l'**erreur de représentativité** lors de l'évaluation de nos prévisions. On veut voir comment cela influence nos résultats et comparer les résultats à ceux obtenus lorsque cette erreur n'est pas prise en compte.
# Introduction

## Contexte

La chaîne de prévision de dispersion de polluants repose, essentiellement, sur deux étapes:
- Modélisation des entrées
- Dispersion atmosphérique

En effet, avant de pouvoir lancer le modèle de dispersion, il est nécessaire de disposer d'une description de l'état de l'atmosphère ainsi que de son l'évolution au cours du temps. 
Les données d'entrée se répartissent en deux grandes catégories: l'état de la source du polluant et la prévision météorologique. Ces deux composantes influencent fortement les prévisions de la dispersion, puisqu'elles déterminent les conditions initiales ainsi que les mécanismes de transport des polluants dans l'atmosphère.

Plus précisément, notre travail repose sur la dispersion du radon 222. 

Lors d'une prévision météorologique ou de dispersion, les calculs se font sur un maillage prédéfini sur la zone d'intérêt. *Préciser km Arome, Arpege, Ldx et Px*

Pour notre étude, nous réalisons nos prévisions tout en combinant les modèles AROME et LDX. 
## Erreur d'observation

Une fois la prévision sur la dispersion du radon, notée $z$, effectuée, il convient de l'évaluer en la confrontant aux observations. Néanmoins, un décalage apparaît.

Lorsqu'une observation, $y$, est réalisée et intégrée au modèle, on retrouve ce qu'on appelle "*erreur d'observation*", que l'on note $e_O$. 
L'erreur d'observation est définie par $$e_O = e_m + e_r,$$ où:
- $e_m$, est l'erreur de mesure, qui correspond à la différence entre la mesure faite par l'appareil et la réalité; 
- $e_r$ est l'erreur de représentativité, qui entre en jeu lorsqu'on rattache l'observation à un modèle, et correspond au décalage au niveau de la modélisation entre une prévision et une observation.

L'erreur de mesure est relativement facile à estimer, les caractéristiques de précision des instruments étant généralement fournies par le fabricant. Elle est, de plus, déjà prise en compte dans les modèles de prévision. 

En revanche, l'erreur de représentativité est plus difficile à quantifier. En effet, les prévisions se font sous une structure de maille, chaque valeur représentant une moyenne ou une estimation sur une cellule de la grille, tandis que les observations correspondent à des mesures ponctuelles réalisées en des positions précises. Cette différence de représentation spatiale induit une erreur supplémentaire lors de la comparaison entre prévisions et observations et nous empêche d'avoir une évaluation réaliste de nos prévisions.

C'est pour cette raison que notre étude se centre sur l'erreur de représentativité.

![[Images - Ch. Erreur Obs.png|455]]

Actuellement, le processus est le suivant:  ***préciser méthode actuelle et pourquoi elle suppose un problème.***

Alors comment prendre en compte ce décalage au moment d'évaluer une prévision? 
Nous allons tout d'abord présenter le fonctionnement de la méthode utilisée, puis les résultats obtenus.  
# Théorie de la méthode

Une manière de tenir compte de l'erreur de représentativité, $e_r$​, consiste à perturber nos prévisions. Pour cela, on combine la distribution des prévisions avec celle du modèle d'observation décrivant la détection du radon. Cette combinaison relève, en théorie, d'une convolution entre les deux distributions; nous verrons cependant qu'en pratique, on lui préfère une approximation par Monte-Carlo, plus simple à mettre en œuvre. Cette partie présente d'abord le principe mathématique de la convolution, avant de détailler la construction concrète des données perturbées.
## Fonctionnement de la convolution

La convolution est une opération mathématique qui, à partir de deux fonctions définies sur un domaine $\mathbb{K}$, produit une troisième fonction décrivant la manière dont elles se combinent. Les valeurs étant représentées avec une précision finie, on considère un domaine discret; c'est à dire que: $\mathbb{K}\subseteq \mathbb{Q}$. Nous travaillerons donc sur un ensemble discret de valeurs.

En théorie des probabilités, la convolution discrète permet de déterminer la distribution de la somme de deux variables discrètes aléatoires indépendantes. Plus précisément, si $A$ et $B$ sont deux variables aléatoires indépendantes de lois $p_A$ et $p_B$​, alors la loi de leur somme$$C = A +B,$$ est donnée par la convolution de leurs distributions :$$p_C = p_A * p_B$$Dans le cas discret, cette convolution s'écrit $$(p_A*p_B)(x) = \sum_{k\in\mathbb{K}}p_A(k)p_B(x-k)$$
## Construction des données: Convolution ou Monte-Carlo?

Notre objectif est d'obtenir la distribution des prévisions perturbées par l'erreur de représentativité, c'est-à-dire de combiner la distribution des prévisions avec celle du modèle d'observation. Nous allons voir que si cette combinaison correspond formellement à une convolution, elle sera en réalité approchée numériquement par une méthode de Monte-Carlo.

Soit $\mathcal{M}$ une maille définie sur la France de longueur $\Delta$, où on appelle $m_i$ les cellules limitées par la grille, et où $i\in I =\{1,...,N_m\}$, $N_m$ étant le nombre de cellules inclues dans $\mathcal{M}$.

Soit $Z$ une variable aléatoire qui décrit la prévision d'ensemble avec $n$ membres sur $N_m$ mailles différentes. On note $[z_{i,1}, z_{i,2},..., z_{i,n}]$ l'ensemble des $n$ prévisions sur la maille $m_i$. On aura donc, pour tout $i\in I$, $$p_{Z_i}(z) = \frac{1}{n}\sum_{k=1}^n\delta(z-z_{i,k}).$$On introduit également une variable aléatoire $E$, désignant l'erreur de représentativité du modèle, dont la distribution est supposée connue (elle sera définie dans la section consacrée au modèle d'observation).

>[!Observation]
>Les variables aléatoires $Z$ et $E$ sont indépendantes (à démontrer)

En théorie, la distribution des prévisions perturbées s'obtiendrait en calculant exactement la convolution $p_{Z_i}∗p_E$​. En pratique, $Z$ étant une distribution empirique à $n$ points, ce calcul reviendrait à évaluer $p_E(x−z_{i,k})$ pour chaque membre $k$ et chaque valeur $x$ du support: un coût qui croît vite avec la finesse de discrétisation de $\mathbb{K}$. On utilise donc une approximation par Monte-Carlo, qui ne nécessite pas de discrétiser $p_E$ sur tout son support. En effet, pour chaque membre de l'ensemble de prévisions, une réalisation aléatoire de l'erreur sera générée, puis une nouvelle prévision sera construite selon $$x_{i,k} = z_{i,k} + e_{i,k}.$$En ajoutant cette erreur aléatoire à notre prévision, on obtient un nouvel ensemble perturbé $[x_{i,1}​,...,x_{i,n}​]$ dont la distribution empirique converge vers $p_{Z_i}∗p_E$ lorsque le nombre de tirages augmente. C'est cette collection qui sert ensuite de référence perturbée pour l'évaluation des prévisions.

# Modèle d'observation
 
Le modèle d'observation consiste, essentiellement, en une distribution probabiliste ayant ses paramètres entièrement déterminés et suivant la distribution de notre variable: la dispersion du radon. 

Compte tenu de la construction de l'erreur de représentativité, l'intérêt de cette méthode est qu'il y ait une dépendance à la longueur de la maille, $\Delta$, au niveau des paramètres qui déterminent le modèle d'observation.

Le nombre de paramètres à optimiser varie en fonction de la distribution choisie. Notre étude va surtout se centrer sur la moyenne $\mu$ et l'écart-type $\sigma$, mais tous les paramètres seront optimisés en fonction de $\Delta$. 
## Structure du modèle

Pour construire le modèle il faut déterminer deux choses: la distribution principale de la variable aléatoire $E$ et ses correspondants paramètres. La distribution choisie doit être cohérente avec la distribution de la grandeur étudiée, et les paramètres doivent être adaptés aux données d'observation avec lesquelles on travaille. 

>[!Exemple de distributions]
> On peut retrouver plusieurs exemples de choix faits auprès des distributions sur [[Accounting for representativeness in the verification of ensemble forecasts]], comme le sont:
>- Distribution normale pour la température à 2m,
>- Distribution normale tronquée pour la vitesse du vent à 10m,
>- Distribution gamma décalée et censurée pour les précipitations journalières.
>

Avant de pouvoir être plus précis sur la façon de calculer les paramètres qu'on cherche et de, simultanément, prendre en compte les erreurs générées par les différentes échelles de travail, il faut qu'on définisse deux éléments qui seront essentiels pour cette construction: $y_A$ et $y_B$.
## $y_A$ et $y_B$

Considérons la notation décrite sur [[Erreur de Représentativité lors de l'Évaluation#Construction des données Convolution ou Monte-Carlo?|Construction des données Convolution ou Monte-Carlo?]].

Dans chaque cellule $m_i$ de la maille $\mathcal{M}$, on aura $S_i$ stations de mesure, qui fournissent les observations de la détection de radon 222. Pour chaque station de mesure $s_{i,j}$ (où $j\in J=\{1,...,S_i\}$), on aura un certain nombre d'observations qui seront notées $y_B$, quantifiant la présence de radon sur sa localisation. 

Le nombre d'observations de chaque station de mesure dépend de la fréquence d'enregistrement $freq$ établie. Les $y_B$ vont donc dépendre de la maille dans laquelle ils se trouvent, de la station qui l'a mesuré, et de l'instant $t\in T=\{1,...,N_T\}$, où $N_T$ est le nombre total d'instants mesurés,  auquel la mesure a été faite. C'est à dire que:
$$y_B = y_B^{i, j, t}$$
>[!Exemple]
Reprenons la Figure $\ref{img:maille}$ pour un exemple. 
À gauche nous avons la maille $\mathcal{M}$ désignée sur la France, et à droite une des aires $m_{i_0}$ où figurent ses $S_{i_0}$ stations de mesure. Si l'on considère $freq = 1h$, l'ensemble des stations $\{s_{i_0,j}\}_{j\leq S_{i_0}}$ donnera une observation $y_B^{i_0,j,t}$ toutes les heures. Cela composera la base de données d'observations sur laquelle on travaillera.  

>[!Remarque]
>La valeur $S_i$ est un numéro et $s_{i,j}$ fait référence à une station.

Avec cette construction on va pouvoir faire référence aux observations individuelles des stations. Le modèle n'est cependant pas défini à cette échelle d'observation. Il manipule des valeurs moyennes par maille $m_i$ et non des observations ponctuelles avec des localisations plus précises que la maille définie. 

En conservant les notations précédentes, on définit $y_A^{i,t}$ comme la moyenne des valeurs $y_B^{i,j,t}$ sur $j$. C'est-à-dire qu'on va inclure les observations faites avec toutes les stations d'une même maille $m_i$ et à un même instant $t$. Plus précisément,
$$y_A^{i,t} = \frac{1}{S_i}\sum_{j=1}^{S_i} y_B^{i,j,t}.$$
Voici une représentation graphique de notre notation:

![[2.png|339]]

Le but de cette méthode est de voir à quel point les valeurs moyennes $y_A$, qu'on cherche à estimer au moment des prévisions, sont différentes des valeurs ponctuelles $y_B$ réelles.

## Calcul des paramètres

Ayant établi les définition de $y_A$ et $y_B$, on peut compléter la description de la structure du modèle d'observation.

Soit $\mathcal{D}$ la distribution choisie liée à la variable aléatoire $E$. $\mathcal{D}$ dépend de la moyenne $\mu$, l'écart-type $\sigma$ et un possible ensemble de paramètres additionnel $\{q_k\}_{k}$ où $k\in\mathbb{Z}_{\geq 0}$ propre à chaque distribution. 

On définit nos paramètres principaux $\mu$ et $\sigma$ comme une fonction affine de $y_A$ et $\sqrt{y_A}$, respectivement:
$$\mu(y_A) = \alpha_0 +\alpha_1y_A,$$
$$\sigma(y_A) = \beta_0 + \beta_1\sqrt{y_A},$$
où les valeurs de $\alpha_0$, $\alpha_1$, $\beta_0$, $\beta_1$, ainsi que l'ensemble $\{q_k\}$ sont des fonctions linéaires ou affines de $\Delta$ ou de $\sqrt{\Delta}$, $\Delta$ étant la variable qui défini la longueur de la maille. Conformément à la définition de $e_r$​ donnée en introduction, on modélise ainsi la relation entre l'observation ponctuelle $y_B^{i,j,t}$​ et son estimateur de maille $y_A^{i,t}$ par la distribution $\mathcal{D}(\mu(y_A),\sigma(y_A), \{q_k(\Delta)\}_k)$. 

# Mise en place de la méthode

## Base de données

La base de données va nous servir à déterminer la distribution $\mathcal{D}$ qu'on va attribuer à la dispersion de radon et à valider notre modèle. Réalisons donc une description complète de la base de données sur laquelle on travaille.

### Structure des données

Nos données sont constituées d'un fichier *.csv* par station de mesure, classifiés par département. Ceux-ci contiennent la quantité de radon ([[rayonnement gamma]]) mesurée par heure, c'est à dire que dans notre cas on a $freq = 1h$. Le tableau suivant montre la structure générale des archives.

| Données |             |                   |          |       |            |
| ------- | ----------- | ----------------- | -------- | ----- | ---------- |
|         | Département |                   |          |       |            |
|         |             | Station de mesure |          |       |            |
|         |             |                   | Jour     | Heure | Dose Gamma |
|         |             |                   |          | Heure | Dose Gamma |
|         |             |                   |          | ...   | ...        |
|         |             |                   | Jour + 1 | Heure | Dose Gamma |
|         |             |                   |          | ...   | ...        |
|         |             |                   | ...      |       |            |
### Cellules de la maille

Donnée une maille $\mathcal{M}$ , nous avons un certain nombre de cellules qui contiennent un certain nombre de stations qui à son tour fourniront les observations qui vont composer es données.

Notre objectif est d'avoir un maximum de données tout en gardant la cohérence au moment de la sélection. Maintenir le sens dans nos analyses, nous réalisons un filtrage logique des données. Rappelons nous que $$y_A^{i,t} = \frac{1}{S_i}\sum_{j=1}^{S_i} y_B^{i,j,t}.$$Comme on peut le voir sur la formule, au moment de considérer une cellule, on a besoin de plusieurs points de mesure des $y_B$ afin d'avoir un $y_A$ plus représentatif. Nous avons donc établi la condition suivante:
$$\text{$m_i$ entre dans la base de donn\'ees $\Leftrightarrow$ $S_i$ $\geq$ $5$ }.$$
Autrement dit, seulement les cellules contenant cinq stations de mesure ou plus prennent partie dans notre étude. 
### Pics de radon

Une fois les cellules sélectionnes, nos données vont passer un deuxième filtre concernant les valeurs observées. 

Rappelons-nous qu'on travaille sur la présence de radon dans l'atmosphère. Et ce qui nous intéresse est une présence anormalement haute. Le radon 222 est présent en permanence dans l'atmosphère, cela crée un bruit de fond difficile à modéliser qui va probablement polluer nos données. C'est por cela qu'on définit un seuil, $P_{radon}$, à partir duquel on va considérer avoir observé un pic. Cette condition étant un peu stricte en vue de la complexité du modèle, on ajoute aussi une tolérance.

On considère donc avoir observé un pic si une de ces conditions se valide:
- $y_B^{i,j,t} \geq P_{radon}$
- $\big(z^{i,j,t} \geq P_{radon} + tol_{z}\big) \text{ et } \big(y_B^{i,j,t} \geq P_{radon} - tol_y\big)$ 
C'est à dire que si la prévision détecte un pic avec un peu de marge (déterminée par $tol_z$) et que le pic n'a pas été observé mais le taux était quand même assez élevé (déterminé par $tol_y$) alors on considère aussi avoir observé un pic. 

Le filtre au niveau des données est donc défini de la manière suivante:
$$\text{La touple $(y_A^{i,t}, \{y_B^{i,j,t}\}_j)$ est dans la base de donn\'ees $\Leftrightarrow$ $\exists j\in J$: un pic a \'et\'e observ\'e sur  $y_B^{i,j,t}$.}$$
## Construction de la maille

La maille, dans notre algorithme est totalement déterminée par la variable $\Delta$ que, comme on a précisé antérieurement, correspond à la longueur de cette dernière. 

Au moment de faire l'étude statistique concernant les paramètres de la distribution $\mathcal{D}$ rattachée à la variable aléatoire $E$, on va faire varier $\Delta$. Il n'y a donc rien à établir pour cette partie là. 
En revanche, il faut fixer $\Delta$ afin faire l'étude qui va nous aider à déterminer $\mathcal{D}$.

On a choisit de faire cela en maximisant les données desquelles on allait disposer concernant le filtre décrit dans la partie [[Erreur de Représentativité lors de l'Évaluation#Cellules de la maille|Cellules de la maille]]. C'est à dire qu'on a mis le kilométrage de la maille en relation au nombre de cellules avec cinq stations de mesure ou plus. 

| $\Delta$ (km) | 20  | 30  | 40  | 45  | 50  | 55  | 60  | 70  | 80  | 90  | 100 |
| ------------- | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| > 5 stations  |  4  | 19  | 27  | 27  | 26  | 26  | 28  | 24  | 27  | 22  | 21  |

Dans le tableau on voit que, le meilleur compromis est $\Delta=40km$ avec $27$ cellules, puisqu'on est pas loin du nombre maximum de cellules traitées (atteint à 28 cellules pour $\Delta =80km$) et on a le double de précision. 

On établit donc pour la suite $\Delta =40km$, jusqu'à préciser autrement. 
# Traitement des Données
# Nos Fittings
# Étude Statistique des Résultats

