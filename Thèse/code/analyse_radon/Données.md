#dataset #données #python

### Structure des données
Nos données sont constituées d'un fichier *.csv* par station de mesure, classifiés par département. Ceux-ci contiennent la quantité de radon ([[rayonnement gamma]]) mesurée par heure **du 01/06/2024 au 30/06/2025**. Le tableau suivant montre la structure générale des archives.

| Données |             |                   |          |       |            |
| ------- | ----------- | ----------------- | -------- | ----- | ---------- |
|         | Département |                   |          |       |            |
|         |             | Station de mesure |          |       |            |
|         |             |                   | Jour     | Heure | Dose Gamma |
|         |             |                   |          | Heure | Dose Gamma |
|         |             |                   |          | ...   | ...        |
|         |             |                   | Jour + 1 | Heure | Dose Gamma |

#### Filtre 5 ou plus
Voici une liste des départements qui contiennent ==5 stations de mesure ou plus==:
	**\[10, 13, 17, 18, 26, 29, 30, 33, 37, 38, 41, 42, 45, 47, 49, 50, 57, 58, 59, 62, 68, 69, 75, 76, 77, 78, 82, 83, 84, 86, 89, 91, 92\]**
