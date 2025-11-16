# ⚽ FootballAnalytics_Python

> 🚀 **Projet Data Engineering complet** : pipeline **ETL en Python** pour extraire, transformer et charger les données de matchs de football depuis l’API _Football-Data.org_ dans **SQLite**, puis migration vers le **Cloud (ADF + Snowflake)** et visualisation via **Streamlit**.

---

## 🏷️ Badges techniques

![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python)
![Pandas](https://img.shields.io/badge/Pandas-DataFrame-yellow?logo=pandas)
![SQLite](https://img.shields/badge/Database-SQLite-lightgrey?logo=sqlite)
![ETL](https://img.shields.io/badge/Process-ETL-orange)
![ADF](https://img.shields.io/badge/Azure-Data%20Factory-blue?logo=microsoftazure)
![Snowflake](https://img.shields.io/badge/Warehouse-Snowflake-00BFFF?logo=snowflake)
![Streamlit](https://img.shields.io/badge/App-Streamlit-FF4B4B?logo=streamlit)

---

## 🧠 Objectif

Automatiser le suivi et la transformation des données de matchs :

1. **Extraction** depuis l’API `football-data.org`  
2. **Transformation** des données brutes en DataFrames propres  
3. **Chargement** dans une base **SQLite**  
4. **Journalisation complète** via `logger_config.py`  
5. **Migration Cloud** → *Azure Data Factory + Snowflake*  
6. **Visualisation temps réel** → *Streamlit*  

Ce pipeline constitue la **brique Python locale** du projet complet *Football Analytics Data Platform*.

---

## 🧩 Stack technique

| Outil / Lib | Usage principal |
|--------------|----------------|
| 🐍 **Python** | Développement du pipeline ETL |
| 🌐 **Requests** | Appels API REST |
| 🧮 **Pandas** | Transformation |
| 🗄️ **SQLite3** | Base locale |
| 🧾 **Logging / dotenv** | Logs & secrets |
| ☁️ **Azure Data Factory** | Orchestration Cloud |
| ❄️ **Snowflake** | Data Warehouse |
| 📊 **Streamlit** | Dashboard |

---

## 🗂️ Structure du projet

---

# 🧠 Architecture du pipeline Python

```mermaid
flowchart LR
    A[⚽ API Football Data] -->|JSON| B[📥 EXTRACT<br>src/extract/extract_api.py]
    B --> C[🧮 TRANSFORM<br>src/transform/transform.py]
    C --> D[💾 LOAD<br>src/load/database.py]
    D --> E[(🗄️ SQLite<br>data/historique_match.db)]
    C --> F[📊 Processed Files<br>data/processed/]
    A --> G[📁 Raw JSON<br>data/raw/]
    B --> H[🧾 Logs<br>data/logs/etl_*.log]
    D --> H
```
# 🧠 Architecture du pipeline ADF
```mermaid
flowchart LR
    A[🌐 Web Activity<br>Get_Football_Matches] --> B[📦 Copy RAW<br>Azure Blob Storage]
    B --> C[❄️ Load Snowflake RAW]
    C --> D[🧮 Transform CURATED<br>MERGE / UPSERT]
    D --> E[(❄️ Snowflake CURATED Schema)]
    C -.-> F[⚠️ Failure Handling]
    D --> G[🧾 Logs → MONITORING.ETL_LOGS]
```
##  🗄️ Structure des tables SnowflakeADF
### 🧊 RAW.MATCHES (semi-structuré)
| Colonne | Type | Description |
|--------|------|-------------|
| payload_raw | VARIANT | JSON complet du match |
| load_time | TIMESTAMP | Timestamp du chargement |
| source_file | STRING | Nom du fichier JSON ingéré |

---

### 📘 CURATED.MATCHES (nettoyée & enrichie)
| Colonne | Type | Description |
|--------|------|-------------|
| match_id | INTEGER | Identifiant unique du match |
| competition | STRING | Nom de la compétition |
| home_team | STRING | Équipe domicile |
| away_team | STRING | Équipe extérieure |
| buts_domicile | INTEGER | Nombre de buts domicile |
| buts_visiteurs | INTEGER | Nombre de buts extérieur |
| vainqueur | STRING | "Home" / "Away" / "Draw" |
| heure_match_paris | TIMESTAMP | Date/heure convertie en UTC+1/UTC+2 |
| loaded_at | TIMESTAMP | Timestamp ingestion pipeline |

## 🧱 Modèle de données Snowflake (CURATED)

```mermaid
erDiagram
    CURATED_MATCHES {
        int match_id
        string competition
        string home_team
        string away_team
        int buts_domicile
        int buts_visiteurs
        string vainqueur
        timestamp heure_match_paris
        timestamp loaded_at
    }

    COMPETITIONS {
        int competition_id
        string nom_competition
        string pays
    }

    COMPETITIONS ||--o{ CURATED_MATCHES : "competition_id"
```

---



# 🖥️ 4. Fonctionnalités de l'application Streamlit

## 🖥️ Fonctionnalités Streamlit

### 🎛️ Filtres interactifs
- Filtre par **compétition**

### 📊 Visualisations
- Histogramme des buts par compétition
- Évolution des scores dans le temps
- Tableau dynamique des matchs

### 🔁 Fonctionnalités avancées
- Auto-refresh grâce à `streamlit-autorefresh`
- Connexion Snowflake → Streamlit pour données live
- Mise en forme thématique “Football Analytics”

### ☁️ Architecture d’accès
Snowflake → Python Connector → Streamlit Cloud / Local

