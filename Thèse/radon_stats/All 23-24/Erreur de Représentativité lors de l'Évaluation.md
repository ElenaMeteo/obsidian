Le but de cette étude est de prendre en compte l'**erreur de représentativité** lors de l'évaluation de nos prévisions. On veut voir comment cela influence nos résultats et le comparer avec des situations où, cette erreur, n'a pas été assimilée.
# Introduction

### Contexte

La chaîne de prévision de dispersion de polluants repose, essentiellement, sur deux étapes:
- Modélisation des entrées
- Dispersion atmosphérique
En effet, avant de pouvoir lancer le modèle de dispersion, il est nécessaire de disposer d'une description de l'état de l'atmosphère ainsi que de son l'évolution au cours du temps. 
Les données d'entrée se répartissent en deux grandes catégories: l'état de la source du polluant et la prévision météorologique. Ces deux composantes influencent fortement la prévision de la dispersion, puisqu'elles déterminent les conditions initiales ainsi que les mécanismes de transport des polluants dans l'atmosphère.

Lors d'une prévision météorologique ou de dispersion, les calculs se font sur un maillage prédéfini sur la zone d'intérêt. *Préciser km Arome, Arpege, Ldx et Px*

Pour notre étude nous réalisons nos prévisions tout en combinant les modèles AROME et LDX. 
### Erreur d'observation

Une fois la prévision, notée $z$, effectuée, il convient de l'évaluer en la confrontant aux observations. Néanmoins, un décalage apparaît.

Lorsqu'une observation, $y$, est réalisée et transférée à un modèle, on retrouve ce qu'on appelle "*erreur d'observation*", que l'on note $e_O$. 
L'erreur d'observation est définie par $$e_O = e_m + e_r,$$ où:
- $e_m$, est l'erreur de mesure, qui correspond à la différence entre la mesure faite par l'appareil et la réalité; 
- $e_r$ est l'erreur de représentativité, qui entre en jeu lorsqu'on rattache l'observation à un modèle, et correspond au décalage au niveau de la modélisation entre une prévision et une observation.

L'erreur de mesure est relativement facile à estimer, les caractéristiques de précision des instruments étant généralement fournies par le fabricant. Elle est, de plus, déjà prise en compte dans les modèles de prévision. 

En revanche, l'erreur de représentativité est plus difficile à quantifier. En effet, les prévisions se font sous une structure de maille, chaque valeur représentant une moyenne ou une estimation sur une cellule de la grille, tandis que les observations correspondent à des mesures ponctuelles réalisées en des positions précises. Cette différence de représentation spatiale induit une erreur supplémentaire lors de la comparaison entre prévisions et observations et nous empêche d'avoir une évaluation réaliste de nos prévisions.

C'est pour cette raison que notre étude se centre sure l'erreur de représentativité.

![[Images - Ch. Erreur Obs.png|455]]

Actuellement, le processus est le suivant:  ***préciser méthode actuelle et pourquoi elle suppose un problème.***

Alors comment prendre en compte ce décalage au moment d'évaluer une prévision? 
Nous allons tout d'abord présenter le fonctionnement de la méthode utilisée, puis les résultats obtenus.  
# Théorie de la méthode

Une manière de tenir compte de l'erreur de représentativité, $e_r$​, sur l'évaluation de nos prévisions est de perturber ces dernières en combinant la distribution des prévisions avec celle du modèle d'observation. Cette combinaison relève, en théorie, d'une convolution entre les deux distributions; nous verrons cependant qu'en pratique, on lui préfère une approximation par Monte-Carlo, plus simple à mettre en œuvre. Cette partie présente d'abord le principe mathématique de la convolution, avant de détailler la construction concrète des données perturbées.
### Fonctionnement de la convolution

La convolution est une opération mathématique qui, à partir de deux fonctions définies sur un domaine $\mathbb{K}$, produit une troisième fonction décrivant la manière dont elles se combinent. Les valeurs manipulées étant représentées avec une précision finie, on a $\mathbb{K}\subseteq \mathbb{Q}$. Nous travaillerons donc sur un ensemble discret de valeurs.

En théorie des probabilités, la convolution discrète permet de déterminer la distribution de la somme de deux variables discrètes aléatoires indépendantes. Plus précisément, si $A$ et $B$ sont deux variables aléatoires indépendantes de lois $p_A$ et $p_B$​, alors la loi de leur somme$$C = A +B,$$ est donnée par la convolution de leurs distributions :$$p_C = p_A * p_B$$Dans le cas discret, cette convolution s'écrit $$(p_A*p_B)(x) = \sum_{k\in\mathbb{K}}p_A(k)p_B(x-k)$$
### Construction des données: Convolution ou Monte-Carlo?

Notre objectif est d'obtenir la distribution des prévisions perturbées par l'erreur de représentativité, c'est-à-dire de combiner la distribution des prévisions avec celle du modèle d'observation. Nous allons voir que si cette combinaison correspond formellement à une convolution, elle sera en réalité approchée numériquement par une méthode de Monte-Carlo.

Soit $\mathcal{M}$ une maille définie sur la France de longueur $\Delta$, où on appelle $m_i$ les cellules limitées par la grille, et où $i\in I =\{1,...,N_m\}$, $N_m$ étant le nombre de cellules inclues dans $\mathcal{M}$.

Soit $Z$ une variable aléatoire qui décrit la prévision d'ensemble avec $n$ membres sur $N_m$ mailles différentes. On note $[z_{i,1}, z_{i,2},..., z_{i,n}]$ l'ensemble des $n$ prévisions sur la maille $m_i$. On aura donc, pour tout $i\in I$, $$p_{Z_i}(z) = \frac{1}{n}\sum_{k=1}^n\delta(z-z_{i,k}).$$On introduit également une variable aléatoire $E$, désignant l'erreur de représentativité du modèle, **dont la distribution est supposée connue** (définition de laquelle on verra sur [[Erreur de Représentativité lors de l'Évaluation#Modèle d'observation|Modèle d'observation]]).

>[!Observation]
>Le variables aléatoires $Z$ et $E$ sont indépendantes (à démontrer)

En théorie, la distribution des prévisions perturbées s'obtiendrait en calculant exactement la convolution $p_{Z_i}∗p_E$​. En pratique, $Z$ étant une distribution empirique à $n$ points, ce calcul reviendrait à évaluer $p_E(x−z_{i,k})$ pour chaque membre $k$ et chaque valeur $x$ du support: un coût qui croît vite avec la finesse de discrétisation de $\mathbb{K}$. On utilise donc une approximation par Monte-Carlo, qui ne nécessite pas de discrétiser $p_E$ sur tout son support. En effet, pour chaque membre de l'ensemble de prévisions, une réalisation aléatoire de l'erreur sera générée, puis une nouvelle prévision sera construite selon $$x_{i,j} = z_{i,j} + e_{i,j}.$$
En ajoutant cette erreur aléatoire à notre prévision, on obtient un nouvel ensemble perturbé $[x_{i,1}​,...,x_{i,n}​]$ dont la distribution empirique converge vers $p_{Z_i}∗p_E$ lorsque le nombre de tirages augmente. C'est cette collection qui sert ensuite de référence perturbée pour l'évaluation des prévisions.

# Modèle d'observation
 
Le modèle d'observation consiste, essentiellement, en une distribution probabiliste ayant ses paramètres entièrement déterminés et suivant la distribution de notre variable. 

Dû a la construction de l'erreur de représentativité, l'intérêt de cette méthode est qu'il y ait une dépendance à la longueur de la maille, $\Delta$, auprès des paramètres qui déterminent le modèle d'observation.

Le nombre de paramètres à optimiser varie en fonction de la distribution choisie, mais notre étude va surtout se centrer sur la moyenne $\mu$ et l'écart-type $\sigma$. Cela veut dire qu'on va optimiser tous les paramètres mais on va seulement chercher à prendre en compte la longueur de la maille pour ces deux derniers.
### Structure du modèle

Pour construire le modèle il faut déterminer deux choses: la distribution principale et ses correspondants paramètres. La distribution choisie doit être cohérente avec la distribution de la grandeur étudiée

>[!Exemple]
>D'après [[Accounting for representativeness in the verification of ensemble forecasts]]



### $y_A$ et $y_B$

Ce modèle d'observation est basé sur l'article [[Accounting for representativeness in the verification of ensemble forecasts]].

Soit $\mathcal{M}$ une maille de longueur $\Delta$ où on appelle $m_i$ les aires limitées par la grille, où $i\in I =\{1,...,N_m\}$, $N_m$ étant le nombre de petites mailles inclues dans $\mathcal{M}$ qui touchent à la France.

Dans chaque $m_i$, on aura $s_i$ stations de mesure, qui vont nous procurer les observations. Pour chaque station de mesure $s_{i,j}$ (où $j\in\{1,...,s_i\}$), on aura un certain nombre d'observations auxquelles qui seront notées $y_B$. 

Le nombre d'observation de chaque station de mesure va dépendre de la fréquence d'enregistrement $freq$ établie. Les $y_B$ vont donc dépendre de la maille dans laquelle ils se trouvent, de la station qui l'a mesuré, et de l'instant $t\in T=\{1,...,N_T\}$, où $N_T$ est le nombre total d'instants mesurés,  auquel la mesure a été faite. C'est à dire que:
$$y_B = y_B^{i, j, t}$$

>[!Exemple]
Reprenons la Figure $\ref{img:maille}$ pour un exemple. 
À gauche nous avons la maille $\mathcal{M}$ désignée sur la France, et à droite une des aires $m_{i_0}$ où figurent ses $s_{i_0}$ stations de mesure. Si l'on considère $freq = 1h$, l'ensemble des stations $\{s_{i_0,j}\}_{j\leq s_{i_0}}$ donnera une observation $y_B^{i_0,j,t}$ toutes les heures. Cela composera la base de données d'observations sur laquelle on travaillera.  

>[!Remarque]
>La valeur $s_i$ est un numéro et $s_{i,j}$ fait référence à une station.

Avec cette construction on va pouvoir faire référence aux observations individuelles des stations, mais le problème est que le modèle "ne parle pas cette langue". C'est à dire que notre modèle comprend les valeurs moyennes par maille $m_i$. 

Gardant les repères de la construction antérieure, on définit $y_A^{i,t}$ comme la moyenne des valeur $y_B^{i,j,t}$ sur $j$. C'est à dire qu'on va inclure les observations faites avec toutes les stations  d'une même maille $m_i$ et à un même instant $t$. Plus précisément,
$$y_A^{i,t} = \frac{1}{s_i}\sum_{j=1}^{s_i} y_B^{i,j,t}.$$
Voici une représentation graphique de notre notation:

![[2.png|339]]

Le but de cette méthode est de voir à quel point les valeurs moyennes $y_A$, qu'on cherche à estimer au moment des prévisions, sont différentes des valeurs ponctuelles $y_B$ réelles.

# Mise en place de la Méthode
# Traitement des Données
# Nos Fittings
# Étude Statistique des Résultats

