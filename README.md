# IMPORTER LA BASE DE DONNEE : https://www.kaggle.com/datasets/zzettrkalpakbal/full-filled-brain-stroke-dataset

# Analyse statistique

### On cherche la moyenne d'âge des patients susceptibles d'avoir un arrêt cérébrale, l'âge minimum des patients ainsi que le maximum

SELECT ROUND(AVG(age),2) AS Age_Moy, MIN(age) AS Age_Min, MAX(age) AS Age_Max
FROM stroke_data;

### La moyenne d'âge des patients est 52.05, l'âge maximum est 82 mais on remarque tout de suite qu'il y a une erreur au niveau de l'âge minimum qui est 0.48.
### Avant d'interpréter ces résultats, nous allons tout d'abord identifier les valeurs aberrantes qui peuvent fausser les résultats d'analyses statistiques,

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

### Identifications des valeurs aberrantes pour le niveau de glucose et de IMC/BMI

### Les résultats retrouvés sont la moyenne du taux de glucose : 127,87 et son écart-type : 59,43. La moyenne de l'IMC qui est de 29,67 et son écart type 2,82.

SELECT avg_glucose_level, bmi,
    CASE 
        WHEN avg_glucose_level < (127.87 - 2 * 59.43) OR avg_glucose_level > (127.87 + 2 * 59.43) THEN 'Outlier'
        ELSE 'Normal' 
    END AS glucose_status,
    CASE 
        WHEN bmi < (29.67 - 2 * 2.82) OR bmi > (29.67 + 2 * 2.82) THEN 'Outlier' 
        ELSE 'Normal' 
    END AS bmi_status
FROM stroke_data
WHERE (avg_glucose_level < 9.01 OR avg_glucose_level > 246.73)
OR    (bmi < 24.03 OR bmi > 35.31);

### On retrouvera des données aberrantes au niveau de l'IMC. Nous supprimerons donc les données aberrantes.

DELETE FROM stroke_data
WHERE 
    (avg_glucose_level < 9.01 OR avg_glucose_level > 246.73)
    OR 
    (bmi < 24.03 OR bmi > 35.31);

### En réexecutant la requête, nous obtenons des données plus précises :

SELECT 
    AVG(avg_glucose_level) AS avg_glucose,
    STDDEV(avg_glucose_level) AS stddev_glucose,
    AVG(bmi) AS avg_bmi,
    STDDEV(bmi) AS stddev_bmi
FROM stroke_data;

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


## Nous allons maintenant regrouper les patients selon leur statut tabagique (fumeurs/non-fumeurs) pour analyser la fréquence des AVC dans chaque groupe : 

SELECT 
    smoking_status,
    COUNT(*) AS total_patients,
    SUM(stroke) AS stroke_cases,
    AVG(stroke) * 100 AS avg_stroke
FROM stroke_data
GROUP BY smoking_status;

### Nous pouvons remarquer que pour les patient n'ayant jamais fumé présentent un taux d'AVC modéré (15.0%) qui suggère que le fait de ne jamais avoir fumé est associé à un risque relativement plus bas d'AVC.
### Pour les anciens fumeurs, avec un taux d'AVC de 28.26%, les anciens fumeurs présentent un risque plus élevé que les non-fumeurs mais moins que les patients avec un statut inconnu (28,26%)
### Etonnamment, les fumeurs ont le taux le plus bas d'AVC avec 5,8% environ. Il est possible que d'autres facteurs, comme des comportements de santé ou des traitements médicaux, influencent ces résultats.













