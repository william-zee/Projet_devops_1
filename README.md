# 🌍 Rapport Technique & Guide de Déploiement : Pollution Dashboard

**Auteur :** William [Ton Nom de Famille]  
**Projet :** DevOps for SWE - ESIEE 2026  

---

## 1. Résumé du Projet

Ce projet consiste en la conception et le déploiement d'une application web orientée données (Dashboard de pollution) utilisant une architecture Cloud Native.

L'objectif principal était de mettre en place :

- ✅ Un pipeline CI/CD automatisé  
- ✅ La gestion de fichiers volumineux via Git LFS  
- ✅ Un déploiement robuste sur AWS  

L'application permet de visualiser des données géographiques de pollution stockées dans une base de données relationnelle et traitées via un backend Python/Dash.

---

## 2. Architecture Technique

L'infrastructure repose sur AWS afin de garantir disponibilité et scalabilité tout en respectant le Free Tier.

- **Application (Container)** : Python (Flask/Dash) conteneurisé avec Docker  
- **Base de Données** : AWS RDS (PostgreSQL)  
- **Registre de Conteneurs** : AWS ECR  
- **Serveur de Déploiement** : AWS EC2 (t3.micro)  
- **Gestion des Données Volumineuses** : Git LFS (>800 Mo de CSV)

---

## 3. Pipeline CI/CD (GitHub Actions)

Le pipeline se déclenche à chaque push sur la branche `main`.

### 🔹 Phase CI (Intégration Continue)

- Checkout du code + Git LFS (`lfs: true`)
- Build Docker via Dockerfile optimisé
- Tests de validation de l’image

### 🔹 Phase CD (Livraison Continue)

- Authentification AWS via GitHub Secrets
- Push de l’image vers AWS ECR (`latest`, région `eu-north-1`)

---

## 4. Prérequis et Configuration Initiale

```bash
# 1. Clonage du dépôt
git clone https://github.com/votre-repo/projet-pollution.git
cd projet-pollution

# 2. Récupération des données lourdes
git lfs install
git lfs pull
5. 💻 Déploiement Local (MicroK8s)
1. Build image locale
DOCKER_BUILDKIT=0 docker build -t pollution-dashboard:local .

2. Préparer MicroK8s
sudo microk8s start
sudo microk8s enable dns storage

3. Importer l’image
docker save pollution-dashboard:local | sudo microk8s images import -

4. Déployer via Kubernetes
sudo microk8s kubectl apply -f k8s_app.yaml

5. Vérification
sudo microk8s kubectl get pods
sudo microk8s kubectl port-forward service/dashboard-service 8080:80


Accès : http://localhost:8080

6. ☁️ Déploiement Cloud (AWS EC2)
1. Login ECR
aws ecr get-login-password --region eu-north-1 | \
sudo docker login --username AWS --password-stdin <AWS_ID>.dkr.ecr.eu-north-1.amazonaws.com

2. Nettoyage ancien conteneur
sudo docker rm -f pollution-dashboard

3. Lancement
sudo docker run -d \
  --name pollution-dashboard \
  -p 5000:5000 \
  -e DB_HOST='pollution-db.xxxxx.eu-north-1.rds.amazonaws.com' \
  -e DB_NAME='postgres' \
  -e DB_USER='postgres' \
  -e DB_PASSWORD='<PASSWORD>' \
  <AWS_ID>.dkr.ecr.eu-north-1.amazonaws.com/pollution-dashboard:latest


Accès :
http://<EC2_PUBLIC_IP>:5000/static/FINAL_dashboard.html

⚠️ Ouvrir le port TCP 5000 dans le Security Group.

Air Quality Data Science Project – France (2000–2015)

Dashboard interactif d’analyse de la qualité de l’air en France.

User Guide
Prérequis

Python 3.9+

pip

Git

Connexion internet (premier téléchargement uniquement)

Installation
git clone https://github.com/longeacc/DATA_Science_PROJECT_AirQuality_France.git
cd DATA_Science_PROJECT_AirQuality_France

Environnement virtuel

Windows

python -m venv .venv
.venv\Scripts\activate


Linux/Mac

python3 -m venv .venv
source .venv/bin/activate

Dépendances
pip install -r requirements.txt

Lancement
python main.py


Le dashboard sera généré dans :
output_csv/superposed_graphs_map/FINAL_dashboard.html

Structure du Projet
DATA_Science_PROJECT_AirQuality_France/
├── data/
│   ├── raw/
│   ├── cleaned/
│   └── air_quality.db
├── src/
│   ├── visualizations/
│   └── utils/
├── output_csv/
├── main.py
└── requirements.txt

Data Source

Zenodo – INERIS Air Quality Dataset

529 305 observations

2000–2015

Polluants : PM10, PM2.5, NO₂, O₃, AOT40, SOMO35

Licence : CC-BY-4.0

Architecture Interne
main.py
├── generate_dashboard()
├── find_existing_file()
└── to_rel()

src/visualizations/
├── map.py
├── superpose_scatter_plots.py
└── superpose_histograms.py

Analyse des Résultats
Zones Urbaines

NO₂ ↓

PM10 ↓

O₃ stable ou légère hausse

Zones Suburbaines

NO₂ ↓ modéré

O₃ plus élevé l’été

Zones Rurales

NO₂ stable faible

O₃ pics estivaux

Contributions Originales

Code original :

Dashboard generator

Pipeline data cleaning

Scripts de visualisation

Intégration SQLite

CSS custom

JS interactions

Bibliothèques tierces :

Leaflet.js

Leaflet.heat

Marked.js

Future Enhancements

🔍 Recherche par commune

📊 Graphiques temporels par commune

📌 Panel latéral détaillé

Optimisation SQL avec index

Licence

MIT (code original)

CC-BY-4.0 (données INERIS)