
Prévision déterministe: incertitudes sur l'état initial et sur la modélisation. 

horizon de prévisibilité: limite au-delà de laquelle une prévision déterministe n'a plus d'intérêt

Prévision d'ensemble: approche climatologique inutile. Elle ne dépend pas de l'écoulement du jour. 

Il est possible de faire un ensemble multi-modèle mais y en a trop peu, du coup on sous-estime l'incertitude parce qu'il ont des biais différents. Ça va pas nous renseigner sur l'incertitude. Le biais est systématique et si chaque modèle est différent, ça trompe sur l'incertitude du jour. 

scores pour vérifier que nos membres sont correctes. mais comment cela nous a donne une piste de comment faire nos états initiaux?

 Incertitude de couplage: les frontières d'un petit domaine ont besoin d'information d'un autre modèle donc il faut prendre en compte l'incertitude induite. Comment prendre en compte cette incertitude alors?
 Différences de résolution de couplage, le plus proche c'est, le mieux. 
 On utilise différents coupleurs pour chaque membre. 

Chaine d'assimilation des données.
L'analyse est un mélange entre une prévision à courte échéance et les observations correspondantes. 

Pour le processus d'assimilation d'ensemble, on utilise l'erreur de mesure pour perturber les observations et utiliser ces "pseudo-observations" pour complémenter chaque ébauche. Les nouvelles obs sont aussi valables que les observations de base. Ce fait est valide par le fait que les observations ne sont pas parfaites mais on connaît sa marge d'erreur.

L'assimilation d'ensemble est très chère numériquement parlant. Cela nous limite beaucoup la quantité de membres qu'on peut avoir. 

En vue de comment marche l'assimilation, on peut en déduire que l'incertitude qui persiste est liée au modèle, puisque celle qui ne dépend pas du modèle est corrigée à chaque étape. 


