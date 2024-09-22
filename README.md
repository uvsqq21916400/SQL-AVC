# IMPORTER LA BASE DE DONNEE : https://www.kaggle.com/datasets/zzettrkalpakbal/full-filled-brain-stroke-dataset
/* Calcul des statistiques descriptives globales
Objectif : Obtenir une vue d'ensemble des caractéristiques démographiques et des facteurs de risque (âge, niveau de glucose, BMI) dans ton dataset.*/

# Exploration des données

# On cherche la moyenne d'âge des patients susceptibles d'avoir un arrêt cérébrale, l'âge minimum des patients ainsi que le maximum

SELECT ROUND(AVG(age),2) AS Age_Moy, MIN(age) AS Age_Min, MAX(age) AS Age_Max
FROM stroke_data;

### La moyenne d'âge des patients est 52.05, l'âge maximum est 82 mais on remarque tout de suite qu'il y a une erreur au niveau de l'âge minimum qui est 0.48.
### Pour régler ce problème nous allons procéder au nettoyage des valeurs non entiers.

### On vérifie les valeurs qui sont des nombres décimaux
SELECT age from stroke_data
WHERE age LIKE '%.%';

### On désactive le safe mode
SET SQL_SAFE_UPDATES = 0;

### On efface donc les lignes ayant les décimales.
DELETE FROM stroke_data
WHERE age like '%.%';

### En réexecutant la requête de la moyenne, minimum et maximum : la moyenne a changé ainsi que le minimum
SELECT ROUND(AVG(age),2) AS Age_Moy, MIN(age) AS Age_Min, MAX(age) AS Age_Max
FROM stroke_data;

## Interprétation
### L'âge moyen étant quand même élevée = 53.01, on peut interpreter que les risques d'AVC ou ceux ayant déjà eu des AVC chez un patient est plus fréquent chez une personne âgée ce qui est cohérent par rapport avec les connaissances médicales,
### Etant donnée que l'âge minimum est assez bas, cela peut suggerer que même les enfants peuvent être a risque.
### Un âge maximum élevé est attendu et montre que les mesures préventives doivent se concentrer sur les personnes âgées pour réduire le risque d’AVC

## On cherche la moyenne et l'écart type du niveau de glucose et du BMI (indice de masse corporelle)

SELECT 
    AVG(avg_glucose_level) AS avg_glucose,
    STDDEV(avg_glucose_level) AS stddev_glucose,
    AVG(bmi) AS avg_bmi,
    STDDEV(bmi) AS stddev_bmi
FROM stroke_data;

## Interprétation
### L'écart-type étant très loin de la moyenne peut signifier qu'il y a une grande variabilité dans les niveaux de glucose, avec des valeurs largement dispersées autour de la moyenne. Cela pourrait indiquer une population hétérogène avec différents niveaux de risque.
###

### On cherche maintenant la moyenne du niveau de glucose par catégorie d’hypertension : 
WITH GlucoseParHT AS (
    SELECT 
        hypertension, 
        round(AVG(avg_glucose_level),2) AS avg_glucose
    FROM stroke_data
    GROUP BY hypertension)
SELECT 
    CASE 
        WHEN hypertension = 0 THEN 'Pas hypertension' 
        WHEN hypertension = 1 THEN 'Hypertension' 
        ELSE 'Inconnue' 
    END AS hypertension_status, avg_glucose
FROM GlucoseParHT;

## Interprétation
### La légère différence de moyenne des niveaux de glucose entre les patients hypertendus et non hypertendus montre que, dans cette base de données, l’hypertension n’a pas d’impact majeur sur les niveaux de glucose. 



## Identification des valeurs aberrantes : Détecter et traiter les valeurs anormales dans les données pour éviter les biais (avec CTE ou non)






