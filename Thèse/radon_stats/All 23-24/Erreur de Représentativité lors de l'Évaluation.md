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

La base de données nous servira à déterminer la distribution $\mathcal{D}$ que nous attribuerons à la dispersion de radon, ainsi qu'à valider notre modèle. Nous commençons donc par une description détaillée de la base de données utilisée.

### Structure des données

Nos données sont constituées d'un fichier *.csv* par station de mesure, étalé sur les registres de 2023 et 2024 et classés par département. Chaque fichier contient la quantité de radon ([[rayonnement gamma]]) mesurée toutes les heures. Dans notre cas, la fréquence d'échantillonnage est donc $freq = 1h$. 

Le tableau suivant montre la structure générale des archives.

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

Étant donnée une maille $\mathcal{M}$ , celle-ci est composée d'un ensemble de cellules contenant chacune un certain nombre de stations de mesure. Les observations fournies par ces stations constituent les données exploitées dans notre étude.

Notre objectif est de disposer d'un volume de données aussi important que possible tout en garantissant la cohérence des analyses. Pour cela, nous appliquons un premier filtrage des cellules.

Rappelons que $$y_A^{i,t} = \frac{1}{S_i}\sum_{j=1}^{S_i} y_B^{i,j,t}.$$Cette expression montre que le calcul de $y_A$ repose sur l'ensemble des observations $y_B$ disponibles dans une cellule. Il est donc nécessaire de disposer d'un nombre suffisant de stations afin que $y_A$ soit représentatif de la cellule considérée. Nous retenons ainsi le critère suivant:
$$\text{$m_i$ entre dans la base de donn\'ees $\Leftrightarrow$ $S_i$ $\geq$ $5$ }.$$
Autrement dit, seulement les cellules contenant au moins cinq stations de mesure sont conservées dans l'étude.
### Pics de radon

Une fois les cellules sélectionnes, nos données vont passer un deuxième filtre concernant les valeurs observées. 

Nous nous intéressons aux épisodes de forte concentration en radon dans l'atmosphère. Le radon 222 étant présent en permanence, il génère un bruit de fond difficile à modéliser et susceptible de perturber l'analyse. Nous définissons donc un seuil $P_{radon}$ à partir duquel une observation est considérée comme un pic de radon.

Cette définition étant relativement stricte au regard de la complexité du phénomène, nous introduisons également une tolérance afin de prendre en compte certains cas limites. 

Un pic est considéré comme observé si l'une des deux conditions suivantes est vérifiée :
- $y_B^{i,j,t} \geq P_{radon}$
- $\big(z^{i,j,t} \geq P_{radon} + tol_{z}\big) \text{ et } \big(y_B^{i,j,t} \geq P_{radon} - tol_y\big)$ 

Autrement dit, si la prévision détecte un pic avec une marge déterminée par $tol_z$ et que l'observation est légèrement inférieure au seuil, mais reste suffisamment élevée (à moins de $tol_y$ du seuil), nous considérons également qu'un pic a été observé.

Le filtrage des données est donc défini par la condition suivante :
$$\text{Le touple $(y_A^{i,t}, \{y_B^{i,j,t}\}_j)$ appartient à la base de donn\'ees $\Leftrightarrow$ $\exists j\in J$: un pic ait \'et\'e observ\'e sur  $y_B^{i,j,t}$.}$$
## Construction de la maille

Dans notre algorithme, la maille est entièrement déterminée par le paramètre $\Delta$, qui représente la longueur du côté des cellules.

Lors de l'étude statistique des paramètres de la distribution $\mathcal{D}$ associée à la variable aléatoire $E$, nous ferons varier la valeur de $\Delta$. Aucun choix définitif n'est donc nécessaire à cette étape.

En revanche, il est indispensable de fixer une valeur de $\Delta$ pour mener les analyses préliminaires permettant d'estimer la distribution $\mathcal{D}$.

Nous avons choisi cette valeur en maximisant le nombre de cellules satisfaisant le critère défini dans la section [[Erreur de Représentativité lors de l'Évaluation#Cellules de la maille|Cellules de la maille]]. Plus précisément, nous avons étudié l'évolution du nombre de cellules contenant au moins cinq stations de mesure en fonction de la taille de la maille.

| $\Delta$ (km) | 20  | 30  | 40  | 45  | 50  | 55  | 60  | 70  | 80  | 90  | 100 |
| ------------- | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| > 5 stations  |  4  | 19  | 27  | 27  | 26  | 26  | 28  | 24  | 27  | 22  | 21  |

Le tableau montre que $\Delta = 40\mathrm{km}$ constitue un bon compromis. Cette valeur permet de conserver 27 cellules, soit presque le maximum observé (28 cellules pour $\Delta = 60\mathrm{km}$), tout en offrant une résolution spatiale sensiblement plus fine.

Nous fixons donc, pour la suite de cette étude, $\Delta = 40\mathrm{km}$, sauf mention contraire.
# Traitement des Données

## Erreur sur nos données

Une fois les données filtrées, nous pouvons comparer les observations $y_B$ aux valeurs agrégées correspondantes $y_A$. La figure suivante représente, en ordonnée, les valeurs $y_B$ mesurées pour une station et, en abscisse, les valeurs correspondantes de $y_A$:

![[yAyB_40_all_2324 4.png|382]]

Ce graphique donne une première estimation de l'erreur de représentativité présente dans les données. Dans un cas idéal, tous les points seraient alignés sur la diagonale noire. On constate cependant que les observations s'en écartent fortement, ce qui met en évidence une dispersion importante.

## Quantiles de $y_A$

Afin de construire les fonctions affines décrivant l'évolution de la moyenne $\mu$ et de l'écart-type $\sigma$ en fonction de $y_A$, comme présenté dans la section [[Erreur de Représentativité lors de l'Évaluation#Calcul des paramètres|Calcul des paramètres]], il faut pouvoir réaliser une régression linéaire à partir de plusieurs estimations de ces paramètres pour différentes valeurs de $y_A$.

Pour cela, nous regroupons les données en un certain nombre $N_q$ quantiles de $y_A$, $\{q_n\}_n^{N_q}$. Autrement dit, les observations sont réparties en plusieurs groupes en fonction de la valeur de $y_A$ à laquelle elles sont associées. La valeur de $N_q$ est aussi à déterminer. Avec $N_q=10$ y en a assez pour faire une régression linéaire représentative.

Rappelons que nous avons un $y_A$, calculé à partir d'au moins cinq $y_B$'s, par cellule de la maille pour chaque instant marqué par $freq$. Cela fait $27$ $y_A$'s par heure avant de passer le filtre des pics de radon. En sachant que notre base de données s'étale sur deux ans, on peut en conclure qu'elle sera assez riche malgré les filtres imposés.

Nous allons donc regrouper toutes les valeurs $y_B$ associées aux $y_A$ appartenant au quantile correspondant et cela va nous donner une distribution.

![[Images - Ch. Erreur Obs 1.png|484]]

# Configuration du fitting

Un fitting consiste à approximer nos données à l’aide d’une distribution théorique. L’objectif est d’optimiser les paramètres de cette distribution afin qu’elle s’ajuste au mieux à la densité des valeurs de radon observées.

Pour commencer, il faut définir un ensemble de distributions à tester et déterminer laquelle s’ajuste le mieux aux données en quantifiant l’erreur commise.

## Distributions plausibles

Le graphique ci-dessous met en évidence l’ensemble des pics observés en France afin de nous aider à sélectionner les distributions candidates :

![[all_peaks_france_sans_dist.png|340]]

Notre liste initiale de distributions était donc la suivante :
- Distribution normale
- Distribution de Weibull minimale
- Distribution de Weibull maximale
- Distribution bêta
- Distribution gamma
- Distribution log-normale

Ces distributions ont été sélectionnées car elles semblaient être celles qui s’adaptaient le mieux à l’ensemble de nos données (voir graphique de tous les pics). Très rapidement, nous avons constaté que seules les deux dernières pouvaient être considérées comme de véritables candidates.

Néanmoins, dans les tests qui seront présentés par la suite, nous avons conservé les résultats du fitting de la distribution normale afin de disposer d’un point de comparaison, même si celle-ci n’est jamais retenue comme le meilleur ajustement.

## Évaluation des possibilités

D’autre part, il est également nécessaire de pouvoir quantifier dans quelle mesure nos candidats sont légitimes et/ou meilleurs que les autres. Pour cela, nous avons mis en place une échelle basée sur les scores **AIC** et **BIC**, que nous allons détailler par la suite.

Le **AIC** (_Critère d’information d’Akaike_) et le **BIC** (_Critère d’information bayésien_) sont des critères utilisés pour **comparer des modèles statistiques** (par exemple, des modèles de régression) et déterminer lequel est le plus adapté.

Ils ne mesurent pas directement l’erreur du modèle (comme le MSE ou le RMSE), mais permettent de trouver un compromis entre :
- La qualité d’ajustement du modèle aux données ;
- La complexité du modèle (c’est-à-dire le nombre de paramètres utilisés).

### AIC

Ce critère pénalise la complexité des modèles et privilégie donc leur capacité prédictive. Sa formule de calcul est la suivante :

$$  
AIC = - 2\ln(L) + 2k,  
$$

où $k$ correspond au nombre de paramètres du modèle et $L$ à sa vraisemblance. Plus la valeur de l’AIC est faible, meilleur est le modèle.

### BIC

Le BIC favorise les modèles plus simples et cherche à identifier le modèle le plus probable parmi ceux considérés. Sa formule de calcul est la suivante :

$$  
BIC = - 2\ln(L) + k\ln(n),  
$$

où $k$ correspond au nombre de paramètres, $n$ au nombre d’observations et $L$ à la vraisemblance. Plus la valeur du BIC est faible, meilleur est le modèle.

Le BIC pénalise davantage la complexité que l’AIC, en particulier lorsque le nombre d’observations $n$ est élevé.  

## Méthode utilisée

Enfin, il faut choisir la méthode avec laquelle les optimisations seront réalisées. Cette question se pose lorsque l’on compare les distributions à approcher avec et sans l’application du filtre des pics de radon.

Voici la distribution des données avec le filtre appliqué sur le premier quantile, avec $N_q=20$ :

![[dist_yB_sans_eval_quantile_0 1.png|323]]

Dans ce graphique, on observe la présence d’un événement à partir de 10, ce qui correspond à notre définition d’un pic. Après l’application du filtre des pics, il est normal d’observer ce comportement puisque, pour tout $y_A$, il existe au moins un $y_B > 10$.

Voici maintenant la distribution des données sans application du filtre, toujours pour le premier quantile :

![[dist_yB_sans_eval_quantile_0.png|325]]

Dans ce graphique, on n’observe plus le double événement, mais plutôt un petit pic proche de 0. Cela est dû au quantile choisi. En effet, ce pic diminue lorsque l’on augmente la valeur du quantile.

On constate donc que l’application du filtre des pics sur les données fait apparaître un double événement au sein de la densité des données des premiers quantiles. Cette particularité rend un fitting classique moins efficace. Nous pouvons alors envisager un double fitting, dont les détails seront précisés par la suite. Cependant, cette approche complexifie naturellement les résultats : avec un double fitting, nous obtenons l’ensemble des paramètres en double, ainsi qu’un paramètre supplémentaire correspondant au poids, qui sera décrit ultérieurement.

Les décisions à prendre après cette étude sont donc les suivantes :
- Applique-t-on le filtre des pics de radon ?
- Si oui, est-il pertinent de réaliser un double fitting ?

Les différentes possibilités concernant les méthodes sont présentées ci-dessous.

### Fitting simple

Comme son nom l’indique, le fitting simple est moins complexe que son alternative, mais il risque également d’être moins précis. Nous avons réalisé nos fittings simples selon deux approches que nous avons appelées : **simple automatique** et **simple manuelle**.

La méthode simple automatique utilise directement la fonction `fit` de `scipy`. On fournit en entrée les données ainsi que la distribution que l’on souhaite tester. En retour, on obtient les paramètres qui minimisent l’erreur entre la distribution choisie et les données observées.

Une limitation de cette approche est que l’algorithme peut converger vers un minimum local, ce qui peut conduire à une solution non physique. C’est pourquoi nous avons également développé une méthode manuelle offrant davantage de contrôle sur l’optimisation.

La méthode simple manuelle repose également sur une minimisation automatique, mais uniquement dans un domaine défini par des contraintes que nous imposons. Elle permet notamment de limiter le nombre d’itérations et de contrôler l’« explosion » éventuelle de l’optimisation. Les résultats obtenus avec cette méthode semblent comparables, voire légèrement meilleurs, à ceux obtenus avec la méthode automatique.

### Fitting double

D’autre part, en raison du double événement induit par le filtrage, il est également nécessaire de considérer un fitting basé sur une double distribution. Dans notre cas, afin de conserver une approche relativement simple, cette méthode consisterait à combiner deux distributions suivant la même loi.

Cependant, il faut adapter cette approche à notre objectif principal : déterminer des expressions des paramètres concernés qui dépendent de l’erreur de représentativité.

La technique du double fitting repose sur le même principe que le fitting simple, mais en combinant deux distributions pondérées. Il s’agit donc de construire une combinaison de deux densités de probabilité.

Considérons $\{q_n\}_{n}^{N_q}$ l’ensemble des quantiles traités. Pour chaque $n$, on définit alors une fonction de densité $pdf_n$ telle que :

$$  
pdf_{n} := \omega \cdot pdf_1 + (1-\omega)\cdot pdf_2,  
$$

où $\omega = \omega(N_q,q_n) = \omega_{N_q}(q_n)$ est un paramètre dépendant de la position du double pic pour chaque quantile $n$. Il faudra donc déterminer une expression de $\omega$ en fonction du nombre de quantiles considérés, $N_q$, ainsi que du quantile $q_n$.

Cette $pdf_n$ attribuera deux paramètres de moyenne et d’écart-type pour chaque quantile de $y_A$, chacun correspondant aux valeurs des deux distributions composant $pdf_n$.

Pour chaque quantile $q_n$, on disposera donc de quatre valeurs regroupées dans $Q_n$ :

$$  
Q_n := \begin{cases}  
(\mu_{1,n}, \mu_{2,n}), \text{ en tant que moyennes},\  
(\sigma_{1,n}, \sigma_{2,n}), \text{ en tant qu'écarts-types}.  
\end{cases}  
$$

L’objectif est alors de déterminer les fonctions suivantes à partir de l’ensemble ${Q_n}_{n\in I}$ :

$$  
\begin{cases}  
\mu_{1}(y_A) = \alpha_{1,1}\cdot y_A + \alpha_{1,2},\  
\mu_{2}(y_A) = \alpha_{2,1}\cdot y_A + \alpha_{2,2},  
\end{cases}  
$$

et

$$  
\begin{cases}  
\sigma_{1}(y_A) = \beta_{1,1}\cdot \sqrt{y_A} + \beta_{1,2},\  
\sigma_{2}(y_A) = \beta_{2,1}\cdot \sqrt{y_A} + \beta_{2,2}.  
\end{cases}  
$$

où les paramètres $(\alpha_{i,1}, \alpha_{i,2})$ sont déterminés à partir d’une régression linéaire appliquée à l’ensemble ${\mu_{i,n}}_{n\in I}$, tandis que les paramètres $(\beta_{i,1}, \beta_{i,2})$ sont obtenus à partir d’une régression linéaire appliquée à l’ensemble ${\sigma_{i,n}}_{n\in I}$.

# Choix de la distribution

Dans cette étude statistique, nous allons analyser les résultats des fittings réalisés selon les trois méthodes présentées précédemment. Les distributions utilisées seront les suivantes :
- Distribution normale ;
- Distribution gamma ;
- Distribution log-normale.

Nous allons maintenant présenter les résultats des fittings obtenus pour les données avec et sans application du filtre des pics de radon.

Dans chaque graphique, la légende mettra en évidence une distribution écrite en **gras**. Celle-ci correspond à la meilleure option selon l’un des critères d’évaluation (dans notre cas, le $BIC$) parmi les trois fittings testés. Cette représentation permet de disposer d’un point de référence pour chaque graphique. Nous analyserons également les résultats selon le critère $AIC$.

## Résultats du fitting avec filtre

Voici les résultats obtenus pour les données avec application du filtre :

Quantile 0:
![[dist_yB_3eval_quantile_0 2.png]]

Quantile 1:
![[dist_yB_3eval_quantile_1.png|800]]

Quantile 2:
![[dist_yB_3eval_quantile_2.png]]

Quantile 3:![[dist_yB_3eval_quantile_3 1.png]]

Quantile 4:![[dist_yB_3eval_quantile_4 1.png]]

Quantile 5:![[dist_yB_3eval_quantile_5.png]]

Quantile 6:![[dist_yB_3eval_quantile_6.png]]

Quantile 7:![[dist_yB_3eval_quantile_7.png]]

Quantile 8:![[dist_yB_3eval_quantile_8.png]]

Quantile 9:![[dist_yB_3eval_quantile_9.png]]

Et les résultats statistiques correspondants avec nos mesures:

| Fitting simple automatique | AIC | BIC | Écart  AIC moyen à la meilleure (%) | Écart BIC moyen à la meilleure (%) | nº invalides | Moyenne BIC |
| -------------------------- | --- | --- | ----------------------------------- | ---------------------------------- | ------------ | ----------- |
| `norm`                     | 1   | 1   | 19.624                              | 19.610                             | 0            | 39415.442   |
| `gamma`                    | 1   | 1   | 68.857                              | 68.840                             | 2            | 60695.731   |
| `log-norm`                 | 8   | 8   | 5.660                               | 5.658                              | 1            | 43088.783   |

| Fitting simple manuel | AIC | BIC | Écart  AIC moyen à la meilleure (%) | Écart BIC moyen à la meilleure (%) | nº invalides | Moyenne BIC |
| --------------------- | --- | --- | ----------------------------------- | ---------------------------------- | ------------ | ----------- |
| `norm`                | 0   | 0   | 35.768                              | 35.730                             | 0            | 49931.213   |
| `gamma`               | 0   | 0   | 0.585                               | 0.585                              | 0            | 37077.972   |
| `log-norm`            | 10  | 10  | 0                                   | 0                                  | 0            | 36871.456   |

| Fitting double | AIC | BIC | Écart  AIC moyen à la meilleure (%) | Écart BIC moyen à la meilleure (%) | nº invalides | Moyenne BIC |
| -------------- | --- | --- | ----------------------------------- | ---------------------------------- | ------------ | ----------- |
| `norm`         | 0   | 0   | 34.278                              | 34.208                             | 0            | 49536.632   |
| `gamma`        | 5   | 5   | 0.159                               | 0.159                              | 0            | 36941.814   |
| `log-norm`     | 5   | 5   | 0.252                               | 0.252                              | 0            | 37052.447   |

## Résultats fitting sans filtre

Voici les résultats pour les données sans filtre:

Quantile 0:
![[dist_yB_3eval_quantile_0 3.png]]

Quantile 1:
![[dist_yB_3eval_quantile_1 1.png]]

Quantile 2:![[dist_yB_3eval_quantile_2 1.png]]

Quantile 3:![[dist_yB_3eval_quantile_3 2.png]]

Quantile 4:![[dist_yB_3eval_quantile_4 2.png]]

Quantile 5:![[dist_yB_3eval_quantile_5 1.png]]

Quantile 6:![[dist_yB_3eval_quantile_6 1.png]]

Quantile 7:![[dist_yB_3eval_quantile_7 2.png]]

Quantile 8:![[dist_yB_3eval_quantile_8 1.png]]

Quantile 9:
![[dist_yB_3eval_quantile_9 1.png|697]]

Et les résultats statistiques correspondants avec nos mesures:

| Fitting simple automatique | AIC | BIC | Écart  AIC moyen à la meilleure (%) | Écart BIC moyen à la meilleure (%) | nº invalides | Moyenne BIC |
| -------------------------- | --- | --- | ----------------------------------- | ---------------------------------- | ------------ | ----------- |
| `norm`                     | 0   | 0   | nan                                 | nan                                | 0            | 707040.894  |
| `gamma`                    | 2   | 2   | nan                                 | nan                                | 1            | 976514.629  |
| `log-norm`                 | 8   | 8   | nan                                 | nan                                | 4            | 898463.556  |

| Fitting simple manuel | AIC | BIC | Écart  AIC moyen à la meilleure (%) | Écart BIC moyen à la meilleure (%) | nº invalides | Moyenne BIC |
| --------------------- | --- | --- | ----------------------------------- | ---------------------------------- | ------------ | ----------- |
| `norm`                | 0   | 0   | 75.714                              | 75.708                             | 0            | 1163266.612 |
| `gamma`               | 3   | 3   | 0.267                               | 0.267                              | 0            | 683749.258  |
| `log-norm`            | 7   | 7   | 0.032                               | 0.032                              | 0            | 680975.526  |

| Fitting double | AIC | BIC | Écart  AIC moyen à la meilleure (%) | Écart BIC moyen à la meilleure (%) | nº invalides | Moyenne BIC |
| -------------- | --- | --- | ----------------------------------- | ---------------------------------- | ------------ | ----------- |
| `norm`         | 1   | 1   | 44.716                              | 44.716                             | 0            | 1123906.437 |
| `gamma`        | 7   | 7   | 4.677                               | 4.677                              | 0            | 812500.011  |
| `log-norm`     | 2   | 2   | 5.138                               | 5.138                              | 0            | 819931.741  |

## Analyse des résultats

Nous pouvons observer que, dans les deux études, la distribution `log-norm` s’impose clairement pour le fitting simple, tandis que la distribution `gamma` obtient les meilleurs résultats pour le fitting double.

En analysant les moyennes des scores BIC pour chaque méthode et chaque distribution, on remarque que, pour la base de données filtrée, les méthodes **simple manuelle** et **double** sont toutes deux pertinentes. En revanche, pour la base de données non filtrée, un écart important apparaît entre les résultats du meilleur fitting simple et ceux du meilleur fitting double.

Malgré cette différence, nous réaliserons les calculs selon les deux méthodes dans les deux configurations afin de pouvoir comparer leurs résultats.

# Calcul des paramètres

Voici les résultats des régressions linéaires et les équations obtenues pour chaque paramètre.
## Données avec filtre
### Méthode simple: `log-norm`
#### Équations obtenues
$$\mu(y_A) = 4.0064 + 0.4681y_A$$
$$ \sigma(y_A) = 0.4497 + 1.2558\sqrt{y_A}$$

#### Régressions

Moyenne:
![[regression_mu_simple.png|354]]

Écart-type:
![[regression_sigma_simple 1.png|360]]
### Méthode double: `gamma`
#### Équations obtenues
$$\mu_1(y_A) = 3.0409 + 0.4701y_A$$
$$\mu_2(y_A) = 4.6187 + 0.4991y_A$$
$$\sigma_1(y_A) = 0.6742 + 0.9767\sqrt{y_A}$$
$$\sigma_2(y_A) = 0.5731 + 2.1737\sqrt{y_A}$$
$$\omega(y_A) = 0.5400 + 0.0034y_A$$
#### Régressions

Moyennes:
![[regression_mu1 1.png|302]]
![[regression_mu2.png|302]]

Écart-types:
![[regression_sigma1.png|302]]
![[regression_sigma2.png|302]]

Poids:
![[regression_weights.png|304]]





