#python #carte #france #stations #mesure

Il y a des stations de mesure partout en France. 
Dans notre base de données, les coordonnées des stations sont reliées aux points du maillage du modèle. C'est à dire que les points des cartes qui vont être montrées ne correspondent pas exactement à la vraie position des stations, mais au point du maillage le plus proche des vraies coordonnées. 

### Carte des stations

L'exercice à faire sur les stations: 
1. localiser et "colorier" en fonction du nombre [[Pilote analyse du radon#Dictionnaire| pics de radon]] détectées sur la période établie
2. mettre les couleurs à l'échelle du[[Concepts nécessaires#Score de Brier| score de Brier]] correspondant

Voici le résultat pour la première partie:
![[carte_stat_all_pics.png|482]]

Voici le résultat pour la deuxième partie:

![[carte_stat_all_brier.png|483]]

Deux choses à remarquer:
- Les stations de mesure se retrouvent surtout autour de centrales nucléaires ou industries chimiques
- L'échelle entre les deux mesures semble être comparable: les couleurs sont très similaires. A-t-on correctement tracé la carte?

### Positions relatives des stations


Il est aussi dans notre intérêt de savoir les positions relatives entre les stations. Nous voulons savoir la distance entre chacune des stations existentes et, en particulier, on veut une liste de stations entre lesquelles il y a une distance inférieure à un certain seuil $\Delta$.

Cela va servir à parcourir les groupes de stations dans la même zone de la maille plus facilement.

Pour déterminer la distance entre deux stations on utilise la [[formule d'Haversine]].








