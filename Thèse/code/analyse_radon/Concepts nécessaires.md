### Score de Brier

Le score de Brier mesure la performance d'un ensemble de prévisions en les comparant avec les observations correspondantes et en prenant la moyenne de cette erreur quadratique. Supposons qu'on a $N$ prévisions $p_j$ et  $N$ observations $o_j$. Alors le score de Brier est donné par:
$$BS = \frac{1}{N}\sum_{j=1}^N(p_j-o_j)^2$$

---
### AIC et BIC

Le **AIC** et le **BIC** sont des critères utilisés pour **comparer des modèles statistiques** (par exemple, des régressions) et décider lequel est le meilleur.
Ils ne mesurent pas directement l’erreur (comme le MSE ou le RMSE), mais ils équilibrent :
- La qualité d’ajustement du modèle aux données  
- La complexité du modèle (nombre de paramètres)  
#### AIC (Critère d’information d’Akaike)
Ce critère pénalise la complexité et donc privilégie la capacité prédictive.
Formule :
$$
AIC = 2k - 2\ln(L)
$$
Où $k$ est le nombre de paramètres du modèle et $L$, sa vraisemblance. Plus le AIC est faible, meilleur est modèle. 
#### BIC (Critère d’information bayésien)

Le BIC favorise des modèles plus simples et cherche le **modèle “vrai”**.
Formule :
$$
BIC = k \ln(n) - 2\ln(L)
$$
Où $k$ est nombre de paramètres , $n$ le nombre d’observations  et $L$ la vraisemblance. Plus le BIC est faible, meilleur est modèle.Il pénalise davantage la complexité, surtout lorsque $n$ est grand.  

---
### Méthode Ray-Casting

La méthode *ray-casting* est une manière de savoir si un point est à l'intérieur d'un polygone à partir de ses coordonnées et de celles des vertex du polygone en question. 

L'idée principale:
- Un rayon horizontal est "lancé" depuis le point qu'on regarde en forme de semi-droite. 
- On compte le nombre d'intersections qu'il y a entre cette ligne et les cotés du polygone
	- Nombre impair: le point est à l'intérieur (Il faut faire gaffe parce que la rayon peut avoir une intersection et être dehors si son intersection est faite au sein d'une arrête du polygone.)
	- Nombre pair: le point est à l'extérieur

>[!Note] 
>L'algorithme part d'une demie-droite horizontale et calcule les points d'intersection avec chaque côté du polygone (chacun à son tour). Cela se décide en fonction du numéro total de la fin. 

---
### Score CRPS
Le CRPS est défini pour une distribution $F(y_A)$ et une observation $y_B$ tel que: $$CRPS = \mathbb{E}_X|X - y_B| - \frac{1}{2}\mathbb{E}_{X,X'}|X - X'|,$$ où $X$ et $X'$ sont deux variables aléatoires indépendantes avec distribution $F(y_A)$.

---
### PIT histogram
Un **histogramme PIT** (Probability Integral Transform) est un outil utilisé en statistiques pour vérifier si un modèle probabiliste est bien calibré, c’est-à-dire si ses prédictions correspondent à la réalité.
Tu as un modèle qui prédit une **distribution de probabilité** pour une variable (pas juste une valeur).
Pour chaque observation réelle $y_i$​, tu calcules :
$$u_i = F_i(y_i)$$
où $F_i$ est la fonction de répartition prédite par le modèle. Les $u_i$ sont donc des valeurs entre 0 et 1. Ce sont les valeurs qu'on va utiliser pour notre histogramme.