#dataset #données #python 

### Structure des données
Nos données sont constituées d'un fichier *.csv* par station de mesure, classifiés par département. Ceux-ci contiennent la quantité de radon ([[rayonnement gamma]]) mesurée par heure. Le tableau suivant montre la structure générale des archives.

| Données |             |                   |          |       |            |
| ------- | ----------- | ----------------- | -------- | ----- | ---------- |
|         | Département |                   |          |       |            |
|         |             | Station de mesure |          |       |            |
|         |             |                   | Jour     | Heure | Dose Gamma |
|         |             |                   |          | Heure | Dose Gamma |
|         |             |                   |          | ...   | ...        |
|         |             |                   | Jour + 1 | Heure | Dose Gamma |

---
### Dates concernées
Dans un premier temps on a travailler avec des fichiers qui contenaient les données **du 01/06/2024 au 30/06/2025**. Avec les restrictions trouvées sur la réunion du [[Carnet de Bord#**06/05/2026**|06/05/2026]], on a eu à élargir notre base de données à la totalité des années **2023 et 2024.** Cela implique un changement sur certaines choses parce qu'on ne dispose pas des mêmes stations pendant ces années là que pendant 2025, à cause de l'ajout de nouvelles stations de mesure en 2024 pour les jeux olympiques.

---
#### Filtre 5 ou plus
Voici une liste des départements qui contiennent ==5 stations de mesure ou plus==:
	**\[10, 13, 17, 18, 26, 29, 30, 33, 37, 38, 41, 42, 45, 47, 49, 50, 57, 58, 59, 62, 68, 69, 75, 76, 77, 78, 82, 83, 84, 86, 89, 91, 92\]**

Actuellement, en ayant un peu avancé on travaille sur des mailles et pas des départements. 
À nos données on leur applique deux filtres:
- Les mailles utilisées doivent contenir 5 stations de mesure ou plu
- Les données utilisées doivent contenir une observation dans au moins une station de la même maille. 

---
### Summary all peaks
Le document "Summary_all_peaks.csv" rassemble les événements observés et simulés de toute la France. Dans chaque événement soit il y a eu un positif en simulation, soit en observation, soit les deux. C'est à dire qu'il contient aussi les données des faux positifs et faux négatifs.

