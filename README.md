# Etude inter-millésime des fichiers foncier

## 1. Récupération des données de l’observatoire urba de géovendée

Lien de l’observatoire : [https://dataservices.geovendee.fr/obs_urba/](https://dataservices.geovendee.fr/obs_urba/)

![](https://geotripad.herokuapp.com/uploads/upload_4283632f6d79a76eea7038789b778b16.PNG)


**Répéter l’opération pour l’export du second millésime.**

## 2. Traitement des données dans qgis

1. **Ajout d’un champ « millesime »** dans la table attributaire du fichier récupéré sur l’observatoire urba
1. **Utilisation de l’algorithme QGIS « regrouper »** pour dissoudre les entités avec le champs de regroupement **« typopardom »**
1. **Utilisation de l’algorithme QGIS « De morceaux multiple à morceaux uniques »** afin de découper les multi-polygones créés par l’algorithme précédent en polygones simple
1. **Répéter les étapes 1 à 3** pour le second millésime.
1. **Utilisation de l’algorithme QGIS « Union »** entre les deux couches « géométries simples » créées par les étapes précédentes.
1. **Suppression des champs** "id;gid;id_2;gid_2" inutiles dans la table créés par l’algorithme « union »
1. **Option facultative**  Import en base avec génération d’Id automatique

Il est possible d'effectuer ce traitement avec le modeleur graphique de qgis : 

![](https://geotripad.herokuapp.com/uploads/upload_58e476c4594797b618720d3bd6d879fd.PNG)


Fin du traitement, les fichiers peuvent désormais être utilisés.

Utilisation dans GEO avec un champ calculé :

NB : J'ai ajouté un critère de surface (area>100) pour supprimer les polygones découpés ayant surface trop petite (le plus souvent issus de décallage dans la donnée source)

```
CASE WHEN {typopardom} IN ('agricole','agricole et autre','naturelle','naturelle et autre','eau','eau et autre') AND {typopardom_2} IN('autre','autre et autre','bati','bati et autre')
## possibilité de filter certains polygones trop petits avec AND ST_AREA({geom})>100
THEN 'modification'
ELSE 'pas de changement'
END
```

Equivalent pour qgis, choisir symbologie - ensemble de règle et inscrire la règle : 
```
 "typopardom"  IN ('agricole','agricole et autre','naturelle','naturelle et autre','eau','eau et autre') AND  "typopardom_2" IN('autre','autre et autre','bati','bati et autre')
AND  $area >100
````

