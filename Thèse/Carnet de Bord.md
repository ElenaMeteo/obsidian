### **06/05/2026**
Réunion de mise au point. Le sujet était la façon de prendre en compte l'erreur de représentativité dans le score qui va évaluer notre prévision. Les paramètres d'une loi qui va modéliser nos événements, vont dépendre de la longueur de la maille. 
Remarque principale: **Il n'y a pas de modélisation du bruit de fond ce qui veut dire que ça ne fait pas sens de modéliser le l'erreur de représentativité quand il n'y a pas d'événement** (dépassement d'un pic défini). Notre approche doit changer. On va garder les lignes de données qui contiennent au moins un événement detecté et on va faire notre étude avec ça. 

Il faut refaire toute la partie d'analyse de radon (juin 2025) avec des différentes données: Il s'agit d'augmenter le délai parce qu'on va beaucoup filtrer les données. 

On reste sur juin pour l'instant
- Distribution France entière avec données >10. On regarde a quoi ça ressemble
- Est ce qu'on arrive a fitter? 

### **12/05/2026**
Après une réunion avec Laurent on a établi que nos pistes pour la suite sont les suivantes:
- On regarde combien de pics on trouve en France (yB)
- On calcule les correspondants yA et on les compte
- S'il y en a assez, on va les ranger par intervalles (type histogramme). Il faut répartir les yA de façon équitable.
- On va prendre tous les yB associés aux yA et on va faire un fit, propre à chaque yA avec ces valeurs. 
- Pour chaque valeur de yA on aura donc une moyenne et un écart-type associés. Ça nous permettra de faire une régression linéaire en fonction de yA. 

### 13/05/2026
Mise en route du programme du 12/05. Voici l'approche qui va être mise en place: 
- On va faire un dictionnaire où chaque clé correspond a une station et les éléments seront les valeurs simulées et les valeurs observées
- Pour pouvoir gérer ce dictionnaire, il faut savoir quelles stations sont dans chaque maille et pouvoir y faire référence. C'est plutôt faisable en sachant comment on utilise la maille dans l'autre algo. Il faut juste donner une place plus importante aux noms des stations, mais ça on le verra plus tard

### 05/06/2026
Retour de 2 semaines de formation et 1 semaine de vacances
- Garder les valeurs de toutes les stations d'une même maille dès qu'on observe un pic. Ce correspond aux yB.
- Faire scatter-plot avec yB en fonction de yA. 
- Histogramme de fréquences de yA avec des intervalles définis. Assez de valeurs yA dans chaque intervalle: on peut faire des intervalles non réguliers. 
- Faire un fit avec les yB de la catégorie de l'histogramme correspondante
- Pour chaque intervalle on obtient un mu et un sigma. 
- Tracer mu et sigma en fonction de yA(moy). Est ce qu'on peut faire une regréssion?
- Dans notre article c'était linéaire. On va essayer de faire pareil. 

## 08/06/2026
Je me suis rendu compte que les données qu'on a poyr 2023 et pour 2024 ne sont pas les mêmes. Il y a des stations de mesure en 2024 qui sont inexistentes en 2023. Ça fait qu'on ait 481 archives pour l'un et 434 pour l'autre.
J'ai demandé à Arnaud et la raison de ça est qu'il y a eu des stations installées pour les jeux olympiques. Il faut donc mettre un filtre de données qui sélectionne seulement les stations présentes dans les deux années et aussi qui sont complètes. 
Pour la suite il faut donc:
- [x] Filtrer stations de mesure valides
	- [x] presentes 2 années
	- [x] données complètes
- [x] Continuer avec la liste faite le [[Carnet de Bord#05/06/2026|05/06/2026]]
Pour faire la filtration on peut soit faire un algorithme soit le faire à la main. Mais vuq u'il faut quand-même enlever les incomplètes il va falloir faire un algo. 

## 09/06/2026
Nettoyage des documents json. 
Correction du code jusqu'à maintenant (afin de pas accumuler beaucoup d'erreurs).

## 10/06/2026
Un autre problème rencontré est qu'on réalise un changement de références à un point pour pas a voir à se trembaler les adresses partout mais on a pas un vecteur qui nous permette passer d'une référence à l'adresse, ce qui est un peu problématique dès qu'on a besoin des adresses. 
Où fait-on le changement? `dict_simu_vs_obs`
Est-il nécessaire?
Si oui, mettre en place un vecteur de "traduction".
J'ai échangé toutes les références par les adresses complètes. Ça a l'air d'avoir bien marché.

Les études statistiques faites jusqu'à maintenant deviennent un peu invalides à cause du changement de base de données. On gardera les résultats pour une future comparaison et, éventuellement, pour refaire le même étude dans la même ligne pour la nouvelle base de données. Mais pour l'instant, on va essayer d'avancer sur le reste.

- Garder les valeurs de toutes les stations d'une même maille dès qu'on observe un pic. Ce correspond aux yB.
On a les groupes d'adresses de stations par maille. On les parcourt et on regarde les données de chaque station. Quand on trouve un positif on garde les données de toutes les stations (obs). La matrice issue de ça, aura toutes les informations nécessaires pour le prochain pas. 

### 11/06/2026
On fait quoi au moment de la sélection si y a des valeurs nan dans des stations alors qu'on est rentré dans le critère de filtrage? On enlève la station en question ou on enlève la ligne entière?

Pour l'instant on va juste enlèver les lignes entières. On réflechira si faire autrement après. 

### 12/06/2026
Scatter-plot: trop de valeurs yA proches de 0 -> contradiction avec la façon de calculer yA. 
Compteur de yA au moment de determiner les obs.
Regarder pour une case les valeurs de yA par rapport aux valeurs de yB. 
Pour l'histogramme: faire des quantiles de yA plutôt que de yB: tracer yB par paquets plutot que tout mélanger.
Présentation mardi: présentation vulgarisation
Essai de séparation du main: structure_donnees.py et jsondict_23_24.json

### 15/06/2026
- [x] Validation données: yA trop basses
- [x] Séparation de certaines parties du programme pour le rendre plus rapide
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
	

### 16/06
Présentation événement natech
distribution des yB en fonction des yA par quantiles

### 17/06 
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




