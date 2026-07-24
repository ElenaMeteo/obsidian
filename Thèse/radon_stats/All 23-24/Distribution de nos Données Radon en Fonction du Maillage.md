
Le but de cette étude est de prendre en compte l'**erreur de représentativité** lors de l'évaluation de nos prévisions. On veut voir comment cela influence nos résultats et le comparer avec des situations où, cette erreur, n'a pas été assimiliée.

# Contexte

Afin de réaliser une prévision de dispersion de polluants en cas d'accident nucléaire, nous avons besoin d'une prévision météorologique qui va nos donner une description de l'état et évolution de l'atmosphère.

Lors d'une prévision météorologique ou de dispersion, les calculs se font sur un maillage prédéfini sur la zone d'intérêt. *Préciser km Arome, Arpege, Ldx et Px*

Pour notre étude nous réalisons nos prévisions avec AROME et LDX. 

Néanmoins au moment d'évaluer une prévision un décalage apparaît: les deux facteurs concernés, la prévision et les observations, ne sont pas à la même échelle. 
En effet, comme a l'été décrit précédemment, les prévisions se font sous une structure de maille, alors que les observations, elles, sont ponctuelles. 

![[Images - Ch. Erreur Obs.png|369]]

Alors comment prendre en compte ce décalage au moment d'évaluer une prévision? 
Nous allons tout d'abord présenter le fonctionnement de la méthode utilisée, puis les résultats obtenus. 

# Théorie de la Méthode: première partie

## Erreur d'observation

Lorsqu'une observation et réalisée et transférée à un modèle avec la fin d'évaluer une prévision, on retrouve toujours sa correspondante erreur: $e_O$. 
L'erreur d'observation se décompose en deux parties: 
- L'**erreur de mesure**: $e_m$, correspondant au décalage entre la mesure faite par l'appareil et la réalité; cette-là est relativement facile à estimer avec les limites de l'appareil utilisé qui sont précisées;
- L'**erreur de représentativité**: $e_r$, correspondant au décalage au niveau de la modélisation entre une prévision (généralisée sur toute la maille) et une observation (ponctuelle).

On peut donc exprimer l'erreur telle que:
$$e_O = e_m + e_r.$$
En effectuant un abus de langage on va considérer $e_r\sim r$ , qui est l'erreur sur laquelle cette étude est réalisée. 

Autrement dit, soit $y$ notre valeur de référence (dans ce cas là, l'observation) et $x$ la valeur réelle, alors:
$$y = x +r$$


# Théorie de la Méthode: deuxième partie
# Mise en place de la Méthode
# Traitement des Données
# Nos Fittings
# Étude Statistique des Résultats

