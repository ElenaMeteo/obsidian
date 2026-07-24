#python #distribution #fitting 
L'objectif de cette partie de l'exercice est de tracer les distributions de pics gamma observés dans chaque maille et y attribuer une distribution théorique qui s'y ajuste avec les paramètres optimaux. 

Les [[Données]] utilisées sont filtrées par nombre de stations par maille, le quilometrage de celle-ci étant déterminé par l'étude statistique réalisé sur [[Présence erreur de représentativité]]. Afin de limiter la convergence du système vers des solutions singulières, nous avons besoin d'une certaine quantité de données. Nous regardons donc les départements qui [[Données#Filtre 5 ou plus| contiennent 5 stations de mesure minimum]]. 

---
## Paramètres initiaux

Cette analyse est réalisé en partant de $\Delta = 40km$ et en séparant nos données en 4, 10 et 20 quantiles. 

---
## Fitting avec une distribution

Avec le point réalisé sur le document [[Formules mu et sigma (yA)]] il a été établi le besoin d'une analyse plus approfondie de nos possibilités afin d'avoir un fitting correct. Actuellement, avec nos meilleures distributions théoriques existentes, on trouve quelques complications:

![[dist_yB_all10q_eval 1.png]]

En effet, sur 10 quantiles étudies, 4 posent problèmes sur nos meilleurs candidats. Cela change quand on en annule un des deux. Si on fait cela seulement avec `gamma`, voila le résultat:

![[dist_yB_all10q_eval_gamma.png]]

et, si d'une autre part, on fait ça seulement avec `log-norm`:

![[dist_yB_all10q_eval_lognorm.png]]

La deuxième fênetre peut être considérée meilleure parce que `log-norm` fonctionne sur un quantile de plus que `gamma`. Mais en voulant faire une régression il est pas idéal que les deux quantiles conflictuels soient les deux premiers. De plus, la distribution `gamma` a un grand avantage par rapport à `log-norm`: on en a un exemple avec l'article de référence. 

Voyons ce qu'il se passe quand on utilise `gamma` sur 20 quantiles:

![[dist_yB_all20q_eval_gamma.png]]

et `log-norm sur 20 quantiles:

![[dist_yB_all20q_eval_lognorm.png|697]]

On va faire l'essai de la régression pour les deux cas. 

--- 
## Théorie du fitting avec deux distributions

Comme on peut l'observer sur les figures de la partie précédente, le fitting avec une seule distribution fonctionne très bien pour les hauts quantiles. Néanmoins, dès qu'on regarde la forme de la courbe sur les bas quantiles, on voit que la qualité de l'approximation peut être influencée par la présence du double événement. 

Une technique alternative est donc de faire un fitting a une distribution qui soit le résultat d'un combinaison de deux autres a travers d'un certain poids $\omega$. Supposons qu'on travaille sur $N$ quantiles de $y_A$. Considérons $I = \{1,...N\}$ et $q = \{q_n\}_{n\in I}$ l'ensemble des quantiles traités. Alors, pour tout $n\in I$ on aura une $pdf$ telle que: 
$$pdf_{n} := \omega \cdot pdf_1 + (1-\omega)\cdot pdf_2,$$
où $\omega = \omega(N,q_n) = \omega_N(q_n)$ est un paramètre qui va dépendre des emplacements du double pic pour chaque quantile $n$.  Il faudra donc trouver une expression de $\omega$ en fonction du nombre de quantiles qu'on traite, $N$, et le quantile dans lequel on se place pour chaque histogramme, $q_n$.

Cette $pdf_n$ attribuera deux paramètres de moyenne de écart-type pour chaque quantile de yA, chacun correspondant aux valeurs des deux $pdf$'s qui composent $pdf_n$. Pour chaque quantile $q_n$ on aura quatre valeurs regroupées dans $Q_n$:
$$Q_n := \begin{cases}
(\mu_{1,n}, \mu_{2,n}), \text{en tant que moyennes},\\
(\sigma_{1,n}, \sigma_{2,n}), \text{en tant qu'\'ecart-types}.
\end{cases}$$
Le but est donc de déterminer les fonctions qui suivent à partir de l'ensemble $\{Q_n\}_{n\in I}$  telles que: 
$$\begin{cases}
\mu_{1}(y_A) = \alpha_{1,1}\cdot y_A + \alpha_{1,2},\\
\mu_{2}(y_A) = \alpha_{2,1}\cdot y_A + \alpha_{2,2},
\end{cases}$$
$$\begin{cases}
\sigma_{1}(y_A) = \beta_{1,1}\cdot \sqrt{y_A} + \beta_{1,2},\\
\sigma_{2}(y_A) = \beta_{2,1}\cdot \sqrt{y_A} + \beta_{2,2},
\end{cases}$$

où les paramètres $(\alpha_{i,1}, \alpha_{i,2})$ sont déterminés à partir d'une régression linéaire sur l'ensemble $\{\mu_{i,n}\}_{n\in I}$ et les paramètres $(\beta_{i,1}, \beta_{i,2})$ sont déterminés à partir d'une régression linéaire sur l'ensemble $\{\sigma_{i,n}\}_{n\in I}$. 

---
## Fitting avec deux distributions

1. Déterminer $pdf_1$ et $pdf_2$: dans notre cas on va essayer avec les distributions `gamma`et `log-norm`
2. Déterminer l'expression de $\omega_N(q_n)$
3. Réaliser le fitting des deux distributions: utiliser programme que m'a envoyé Laurent
4. En sortir les paramètres concernés
5. Tracer les valeurs et faire les régressions linéaires
6. Déterminer les paramètres issus des régressions

---
## Résultats fitting avec deux distributions

Dans cette partie on va voir les résultats du fitting avec deux distributions, on va les comparer aux résultats qu'on a déjà et on va conclure sur nos meilleures possibilités. 


---
## Base de données: filtrée ou pas?

On va voir ce qu'il se passe quand on utilise la totalité de la base de données sans nécessairement la passer par le "filtre d'observations".


