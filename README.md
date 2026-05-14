# LaLiga Data Warehouse - Season 2024/2025

Ce projet est un pipeline ETL (Extract, Transform, Load) simple et efficace qui récupère les données de classement en temps réel de LaLiga (Espagne) via une API, les traite avec Python & Pandas, et les stocke dans une base de données MySQL.

## 🚀 Fonctionnalités

- **Extraction :** Récupération des données brutes depuis une API de football.

- **Transformation :**

    - Nettoyage des noms de colonnes en remplaçant les espaces par des underscores (``_``) .

    - Calcul de la forme des équipes (5 derniers matchs).

    - Normalisation des types de données (entiers, chaînes).

- **Chargement :** Insertion automatisée via la méthode **UPSERT** dans une base MySQL avec gestion des doublons (``ON DUPLICATE KEY UPDATE``).

## 🛠️ Stack Technique
- **Langage :** Python 3.12+

- **Analyse de données :** Pandas

- **Base de données :** MySQL 8.0+

- **Connexion DB :** ``mysql-connector-python``

- **Gestion des secrets :** ``python-dotenv``

## 📋 Prérequis
Avant de lancer le script, assure-toi d'avoir :

Un serveur MySQL actif.

Un fichier ``.env`` à la racine du projet (voir section Configuration).

Les bibliothèques Python installées :
```bash
pip install pandas mysql-connector-python python-dotenv
```
## ⚙️ Configuration

#### 1. Base de données

Exécute le script suivant dans ton client MySQL pour préparer l'environnement :

```SQL
CREATE DATABASE IF NOT EXISTS laliga_2024_2025_dw;
USE laliga_2024_2025_dw;

CREATE TABLE IF NOT EXISTS laliga_2024_2025_dw (
    Position INT NOT NULL,
    Equipe VARCHAR(100) NOT NULL,
    Overall_Matches_Played INT,
    Overall_Won INT,
    Overall_Draw INT,
    Overall_Lost INT,
    Overall_Goals_Scored INT,
    Overall_Goals_Conceded INT,
    Goal_Difference INT,
    Form VARCHAR(50),
    Points INT,
    PRIMARY KEY (Position)
);
```

#### 2. Variables d'environnement (``.env``)

```Extrait de code
MySQL_HOST=localhost
MySQL_PORT=3306
MySQL_USER=ton_nom_utilisateur
MySQL_PASSWORD=ton_mot_de_passe
MySQL_DATABASE=laliga_2024_2025_dw
```
## 📂 Structure du Code
- ``build_standing_df(data)`` : Fonction principale qui transforme le JSON brut en un DataFrame Pandas propre. Utilise un mapping technique pour garantir la fiabilité des statistiques.

- ``connector.connect()`` : Établit la liaison sécurisée avec MySQL.

- ``cursor.execute()`` : Gère l'insertion des données ligne par ligne.

## 📝 Utilisation

Lance simplement ton script principal ou ton notebook :

```Python
# Exemple de flux
data = fetch_api_data() # Ton appel API
df = build_standing_df(data)
save_to_mysql(df)
```
## ⚠️ Notes Techniques
- **Authentification :** Le projet utilise mysql_native_password pour garantir la compatibilité entre Python et MySQL 8.0.

- **Mapping de données :** Le script utilise ``detail.get("type", {}).get("name")`` pour générer les noms de colonnes. Le choix a été fait de conserver les orthographe et garantir que le script reste dynamique et compatible avec l'API sans mapping manuel complexe.