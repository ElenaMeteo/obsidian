# **06/05/2026**
Réunion de mise au point. Le sujet était la façon de prendre en compte l'erreur de représentativité dans le score qui va évaluer notre prévision. Les paramètres d'une loi qui va modéliser nos événements, vont dépendre de la longueur de la maille. 
Remarque principale: **Il n'y a pas de modélisation du bruit de fond ce qui veut dire que ça ne fait pas sens de modéliser l'erreur de représentativité quand il n'y a pas d'événement** (dépassement d'un pic défini). Notre approche doit changer. On va garder les lignes de données qui contiennent au moins un événement detecté et on va faire notre étude avec ça. 

Il faut refaire toute la partie d'analyse de radon (juin 2025) avec des différentes données: Il s'agit d'augmenter le délai parce qu'on va beaucoup filtrer les données. 

On reste sur juin pour l'instant
- Distribution France entière avec données >10. On regarde a quoi ça ressemble
- Est ce qu'on arrive a fitter? 

# **12/05/2026**
Après une réunion avec Laurent on a établi que nos pistes pour la suite sont les suivantes:
- On regarde combien de pics on trouve en France (yB)
- On calcule les correspondants yA et on les compte
- S'il y en a assez, on va les ranger par intervalles (type histogramme). Il faut répartir les yA de façon équitable.
- On va prendre tous les yB associés aux yA et on va faire un fit, propre à chaque yA avec ces valeurs. 
- Pour chaque valeur de yA on aura donc une moyenne et un écart-type associés. Ça nous permettra de faire une régression linéaire en fonction de yA. 

# 13/05/2026
Mise en route du programme du 12/05. Voici l'approche qui va être mise en place: 
- On va faire un dictionnaire où chaque clé correspond a une station et les éléments seront les valeurs simulées et les valeurs observées
- Pour pouvoir gérer ce dictionnaire, il faut savoir quelles stations sont dans chaque maille et pouvoir y faire référence. C'est plutôt faisable en sachant comment on utilise la maille dans l'autre algo. Il faut juste donner une place plus importante aux noms des stations, mais ça on le verra plus tard

# 05/06/2026
Retour de 2 semaines de formation et 1 semaine de vacances
- Garder les valeurs de toutes les stations d'une même maille dès qu'on observe un pic. Ce correspond aux yB.
- Faire scatter-plot avec yB en fonction de yA. 
- Histogramme de fréquences de yA avec des intervalles définis. Assez de valeurs yA dans chaque intervalle: on peut faire des intervalles non réguliers. 
- Faire un fit avec les yB de la catégorie de l'histogramme correspondante
- Pour chaque intervalle on obtient un mu et un sigma. 
- Tracer mu et sigma en fonction de yA(moy). Est ce qu'on peut faire une regréssion?
- Dans notre article c'était linéaire. On va essayer de faire pareil. 

# 08/06/2026
Je me suis rendu compte que les données qu'on a pour 2023 et pour 2024 ne sont pas les mêmes. Il y a des stations de mesure en 2024 qui sont inexistentes en 2023. Ça fait qu'on ait 481 archives pour l'un et 434 pour l'autre.
J'ai demandé à Arnaud et la raison de ça est qu'il y a eu des stations installées pour les jeux olympiques. Il faut donc mettre un filtre de données qui sélectionne seulement les stations présentes dans les deux années et aussi qui sont complètes. 
Pour la suite il faut donc:
- [x] Filtrer stations de mesure valides
	- [x] presentes 2 années
	- [x] données complètes
- [x] Continuer avec la liste faite le [[Carnet de Bord#05/06/2026|05/06/2026]]
Pour faire la filtration on peut soit faire un algorithme soit le faire à la main. Mais vuq u'il faut quand-même enlever les incomplètes il va falloir faire un algo. 

# 09/06/2026
Nettoyage des documents json. 
Correction du code jusqu'à maintenant (afin de pas accumuler beaucoup d'erreurs).

# 10/06/2026
Un autre problème rencontré est qu'on réalise un changement de références à un point pour pas a voir à se trembaler les adresses partout mais on a pas un vecteur qui nous permette passer d'une référence à l'adresse, ce qui est un peu problématique dès qu'on a besoin des adresses. 
Où fait-on le changement? `dict_simu_vs_obs`
Est-il nécessaire?
Si oui, mettre en place un vecteur de "traduction".
J'ai échangé toutes les références par les adresses complètes. Ça a l'air d'avoir bien marché.

Les études statistiques faites jusqu'à maintenant deviennent un peu invalides à cause du changement de base de données. On gardera les résultats pour une future comparaison et, éventuellement, pour refaire le même étude dans la même ligne pour la nouvelle base de données. Mais pour l'instant, on va essayer d'avancer sur le reste.

- Garder les valeurs de toutes les stations d'une même maille dès qu'on observe un pic. Ce correspond aux yB.
On a les groupes d'adresses de stations par maille. On les parcourt et on regarde les données de chaque station. Quand on trouve un positif on garde les données de toutes les stations (obs). La matrice issue de ça, aura toutes les informations nécessaires pour le prochain pas. 

# 11/06/2026
On fait quoi au moment de la sélection si y a des valeurs nan dans des stations alors qu'on est rentré dans le critère de filtrage? On enlève la station en question ou on enlève la ligne entière?

Pour l'instant on va juste enlèver les lignes entières. On réflechira si faire autrement après. 

# 12/06/2026
Scatter-plot: trop de valeurs yA proches de 0 -> contradiction avec la façon de calculer yA. 
Compteur de yA au moment de determiner les obs.
Regarder pour une case les valeurs de yA par rapport aux valeurs de yB. 
Pour l'histogramme: faire des quantiles de yA plutôt que de yB: tracer yB par paquets plutot que tout mélanger.
Présentation mardi: présentation vulgarisation
Essai de séparation du main: structure_donnees.py et jsondict_23_24.json

# 15/06/2026
- [ ] Validation données: yA trop basses
- [ ] Séparation de certaines parties du programme pour le rendre plus rapide
- [ ] Corriger histogramme: quantiles
- [ ] Présentation pour demain
Je me suis rendue compte que en fait la lecture de tous les documents que je faisais au début (et que j¡ai passé à structure_donnees.py) ne servait à rien parce que les lectures se refaisaient dans un deuxième temps quand les stations ont été filtrées par le maillage.
Cela donne une lecture beaucoup plus optimisée. 

Presentation
- Introduction probleme
- Objectifs: 
	- evaluer intérêt pe (continuite these youness)
	- evaluation ensembliste propre (erreur obs)
- PE
- Radon lessivage nous permet d'avoir une evaluation objective propre
- Application natech une fois qu'on a valide: utilisation, produit, demostration
	

# 16/06/2026
Présentation événement natech
distribution des yB en fonction des yA par quantiles

# 17/06/2026
Réunion périodique avec les tuteurs.
	- Fitting summary all peaks distribution: gamma
	- On part de cette distribution
	- Idée principale afin d'avoir nos paramètres
-> LDX: il faut valider que les données sont bien converties du coté de météo pour l'utilisation de LDX. Donne des prévisions correctes par rapport aux références de l'ASNR, les observations et les analyses.

- Faire recaps des formations et envoyer à Irène
- Réunions plus regulières
- Reflechir à un plan de travail a plus long terme
	- premieres simulations cas natech pour fevrier 2027
	- claustering? objectif deuxieme annee
	- arome ou pas? adaptation du coté de l'asnr.

- Est ce qu'il y a une influence sur la sensibilité du résultat à faire un backup toutes les 6h ou 24h au niveau des résultats de LDX?

# 18/06/2026
Les résultats des distributions montrent un double pic sur les premiers quantiles. Il faut voir si on peut traiter ça mathématiquement. Il y a moyen de définir des pdf par intervalles mais il faut voir s'il est possible de le faire unifier afin d'obtenir une seule moyenne et un seul écart-type par yA. D'un autre côté on se demande si la remarque de la réunion du [[Carnet de Bord#**06/05/2026|06/05/2026]] est vraiment pertinente dans notre situation: on réalise juste un post-traîtement,  on utilise pas le modèle quand on détermine les paramètres.

# 19/06/2026
En vue du changement de situation on doit remettre en ordre les documents Obsidian, refaire une étude statistique suffisamment solide comme pour justifier nos choix de fitting et réaliser le fitting. Il se trouve que en ayant déjà essayé un fitting on retrouve des problèmes de cohérence avec les données et donc il faut un peu plus approfondir.

Après le travail d'aujourd'hui plusieurs questions se posent pour la suite. 
- Utilisation du filtrage des données? Cela va sans doute influencer la bi-modalité des distributions obtenues par quantile.
- Si on garde ces données: fitting simple où on essaye le double. 
	- Pour le simple, on pourrait faire la régression avec les quantiles non conflictuels et valider (ou pas) avec le reste en établissant les paramètres manuellement à travers la régression
	- Pour le complexe, il faudrait trouver un moyen de définir une nouvelle distribution et que python fasse le fitting avec. Il est possible de trouver une formule plus ou moins adéquate mais c'est le fitting avec un seul paramètre qui m'inquiète le plus: en effet, si on obtient plusieurs paramètres par quantile, on y trouve pas son sens. 
Par rapport à la possibilité de combinaison de deux distributions: le but c'est donc de sortir deux régressions et d'établir une loi combinée avec les paramètres issus de ces deux régressions et un paramètre supplémentaire pour les poids de chacune. 

# 29/06/2026
Après mon passage à Paris, on a LDX sur les ordinateurs prêts à fonctionner. Pour faire tourner ce qu'il nous faut, il nous faut des **fichiers de "mise au point" de radon** (pour pas démarrer une simulation en supposant qu'il y a 0 radon dans l'atmosphère). 
D'une autre part, par rapport aux données: on veut pouvoir faire un fit le plus correcte possible: Laurent a fait un essai avec le "double fitting" en le comparant à un fitting simple. Le double est beaucoup plus performant mais le fitting est "manuel". Il faut qu'on compare les résultats: **avec/sans filtre de d'observations et avec les deux fittings.** 
Pour comparer tout ça, ça commence à être compliqué de le gérer avec le code. On commence un projet d'interface. 

J'ai besoin de réorganiser mon code par paquets.
1- Données: Il faut 
	- une librairie qui contient les fonctions de filtrage de données et de mise en forme. Il faut que tous les fichiers aient le même format
	- un code qui gère le passage par ces fonctions des données brutes
	À la fin il faut obtenir un fichier de données complètes et un autre filtré pour chaque base essayée.
2- Projets: il faut
	- Séparer le main par projets: y a des choses qu'on fait une fois et on refait plus. 

# 01/07/2026
Choses à faire actuellement:
- Gagner l'autonomie d'utiliser LDX
	- [ ] Comprendre "restart"
	- [ ] Obtenir fichiers nécessaires pour le faire
- [ ] Restructurer Obsidian afin d'avoir une lecture simplifiée
-  Restructurer le code
	- [ ] Séparer en plus petits bouts
	- [ ] Le rendre souple: 
		- [ ] mettre un même format de base de données 
		- [ ] créer un fichier avec les paramètres principaux et le relier aux produits
- Avancer sur l'analyse:
	- [ ] Être en mesure de comparer les densités simples et doubles
	- [ ] Être en mesure de comparer les densités avec données complètes vs. filtrées
	- [ ] En sortir une conclusion
	- [ ] Aller jusqu'au bout de l'analyse

# 02/07/2026
Aujourd'hui j'ai commencé à restructurer le code en classes. cela le simplifie beaucoup et probablement je pourrais tout faire avec des classes. Tout n'est pas encore parfait. Pour l'instant il y a 3 documents: fitting_classe.py qui contient les classes pour faire le fitting (simple et double) - pour celui la il va falloir que je mette correctement la structure en vue des différences de données entre le simple et le double -, graphs_classe.py qui contient les classes qui tracent les graphiques (pas encore decide la façon dont je vais structurer cette la) et scores_classe.py qui contient la classe calculant les scores mais je sais pas trop encore si ce format là est pratique pour ça.
J'ai prévu d'en faire une autre pour gèrer les données (lecture de tous les documents csv) et les mettre dans un format unifié pour pas avoir de soucis d'adaptation
Laurent m'a envoyé un programme de travail pour l'été. Pour celui-là il est vraiment important que j'arrive à faire un code souple avec les classes. 


# 15/07/2026
Retour des vacances. On reprend la restructure du code par classes.
Pour s'y remettre: reprenons du début.
- [x] Données: format adéquat
- [x] Fitting: bon format paramètres
Je dois décider maintenant la méthode d'affichage que je veux utiliser. J'ai 3 graphiques par quantile donc une fenêtre avec les 3 graphiques serait pertinente. Et je dois aussi afficher toutes les fenêtres au même moment. 

# 20/07/2026
Il faut qu'on se dépêche et qu'avant le 14 août on ait les formules du mu et sigma qu'on veut utiliser.

# 21/07/2026
La méthode simple manuelle donne beaucoup de problèmes avec certaines distributions. On pourrait peut être les régler mais on va d'abord fiare en sorte que le reste des choses marche. 

Aujourd'hui j'ai réussi a faire les fittings et les graphiques pour les methodes simple automatique et double. Comme on avait deja remarque, la méthode automatique explose à des certains quantiles. Maintenant il faut décider un certain nombre de choses:
- Quelle distribution va-t-on utiliser?
- Avec quelle méthode allons nous le faire?
- Combien de quantiles?
- Quelle maille?
Pour cela il faut faire une étude statistique
Mais avant, il faut debugger la méthode simple manuelle

# 22/07/2026
Réunion de mise au point:
Il ne faut pas trop passer du temps sur le fitting, il faut avancer.
Choisir un critère de décision. 
Refaire les graphiques avec toutes les observations (sans passer par le filtrage de présence de pic).
Modélisation du bruit de fond en cours: ça implique quoi? Entre en jeu au post-traîtement.

Premier chapitre de la thèse d'Irène à lire pour la variabilité sous-maille. 

Objectif: au moins tester sur une journée les fichiers des membres et évaluation avant la fin du mois d'août.

# 29/07/2026
Cette semaine s'est centré sur tout mettre à point sur Obsidian (créer une suite logique des événements) et de finir les études statistiques pour avoir une formules de mu et sigma avant la fin de la semaine. 