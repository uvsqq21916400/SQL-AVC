# IMPORTER LA BASE DE DONNEE : https://www.kaggle.com/datasets/zzettrkalpakbal/full-filled-brain-stroke-dataset
/* Calcul des statistiques descriptives globales
Objectif : Obtenir une vue d'ensemble des caractéristiques démographiques et des facteurs de risque (âge, niveau de glucose, BMI) dans ton dataset.*/

# Exploration des données

#On cherche la moyenne d'âge des patients susceptibles d'avoir un arrêt cérébrale, l'âge minimum des patients ainsi que le maximum
SELECT ROUND(AVG(age),2) AS Age_Moy, MIN(age) AS Age_Min, MAX(age) AS Age_Max
FROM stroke_data;

# La moyenne d'âge des patients est 52.05, l'âge maximum est 82 mais on remarque tout de suite qu'il y a une erreur au niveau de l'âge minimum qui est 0.48.
# Pour régler ce problème nous allons procéder au nettoyage des valeurs non entiers.

# On vérifie les valeurs qui sont des nombres décimaux
SELECT age from stroke_data
WHERE age LIKE '%.%';

# On désactive le safe mode
SET SQL_SAFE_UPDATES = 0;

# On efface donc les lignes ayant les décimales.
DELETE FROM stroke_data
WHERE age like '%.%';

