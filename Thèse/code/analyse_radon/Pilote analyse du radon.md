#propagation #radon #analyse #statistiques #juin2025 #nonmodifiable 

Ce document sert de pilote dans l'exercice d'analyse statistique du radon. 
Ici, on va faire référence à toutes les parties du code et on va les relier.
Ce projet est inscrit dans le cadre du [[Projet naTech]], qui vise à améliorer l'information transmise aux décideurs lors d'un rejet accidentel de polluants causé par une catastrophe naturelle.

### Parties du projet

- [[Données]] étudiées
- Étude:
	- [[Distribution des données]]
		- Distribution observations par département
		- Fitting avec distributions théoriques
		- Distribution observations toute la France
	- [[Stations de mesure]]
		- Scores des simulations par station et département 
		- Carte des stations en France
		- Distance relative entre stations
	- [[Présence erreur de représentativité]]
		- Analyse des différences entre les valeurs moyennes et les individuelles
		- Analyse de l'erreur moyenne (MSE) en fonction du $\Delta$
	- [[Paramètres de la distribution (bases)]]
		- Determination de la loi
		- Description des paramètres
		- Exécution et résultats

### Dictionnaire

- **Pic de radon**: Le radon est detecté à partir du rayonnement gamma émis lors de la détérioration. Le seuil de rayonnement établi pour définir un pic est de $10\ nSvh^{-1}$ 
- **Pic pratique de radon:** On va considérer qu'on a observé un pic si `obs > 10` ou bien `obs > 10 - tol_o`  et  `simu > 10 + tol_simu`. Premièrement on a fixé `tol_obs = 3` et `tol_simu = 2`. C'est à dire qu'on peut aller jusqu'à `obs = 7` tant que la simulation est au moins `simu = 12`. 








