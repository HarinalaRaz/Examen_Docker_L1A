# Examen_Docker_L1A
RAZAFINDRAMBOA Lanjaniaina Harinala L1A\193\24-25
#  Docker - Commandes de Base

Un résumé rapide des commandes Docker essentielles pour démarrer et gérer vos conteneurs. Idéal pour les débutants ou comme pense-bête pour les utilisateurs réguliers.

---

#  Images

```bash
# Télécharger une image depuis Docker Hub
docker pull <nom_image>
#ex:
docker pull ubuntu 

# Lister les images disponibles localement
docker images

# Supprimer une image
docker rmi <id_image>
#ex:
docker rmi ubuntu
```
## Lancement d'un conteneur
```bash
# Lancer un conteneur
docker run <options> <nom_image>

# Exemple : lancer un conteneur interactif
docker run -it ubuntu /bin/bash

# Lister les conteneurs en cours d'exécution
docker ps

# Lister tous les conteneurs (même arrêtés)
docker ps -a

# Arrêter un conteneur
docker stop <id_conteneur>

# Supprimer un conteneur
docker rm <id_conteneur>
```
# Dockerfile
---
## instruction de base qu'il faut connaitre
```bash
# Utilise une image de base officielle Python
FROM python:3.11-slim

# Définit le répertoire de travail dans le conteneur
WORKDIR /app

# Copie les fichiers locaux dans le conteneur
COPY . .

# Installe les dépendances à partir d'un fichier requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Expose le port que l'application va utiliser (optionnel)
EXPOSE 8000

# Commande de lancement par défaut
CMD ["python", "app.py"]
```
commande pour le tester
```bash
# Construire l'image
docker build -t monapp .

# Lancer le conteneur
docker run -p 8000:8000 monapp
```
## resumer des commande dans dockerfile
| 🔧 Commande   | 💬 Rôle                                                                |
| ------------- | ---------------------------------------------------------------------- |
| `FROM`        | Définit l'image de base à utiliser. Ex : `FROM python:3.11`            |
| `LABEL`       | Ajoute des métadonnées (auteur, version, description, etc.)            |
| `ENV`         | Définit des variables d'environnement dans le conteneur                |
| `WORKDIR`     | Définit le répertoire de travail à l'intérieur du conteneur            |
| `COPY`        | Copie des fichiers locaux dans l'image. Ex : `COPY . /app`             |
| `ADD`         | Comme `COPY`, mais gère aussi les URLs et fichiers compressés          |
| `RUN`         | Exécute une commande pendant la construction de l’image                |
| `CMD`         | Définit la commande par défaut à exécuter quand le conteneur démarre   |
| `ENTRYPOINT`  | Spécifie un binaire à exécuter (plus rigide que `CMD`)                 |
| `EXPOSE`      | Informe du port utilisé (documentation uniquement, pas d'ouverture)    |
| `VOLUME`      | Crée un point de montage pour les volumes Docker                       |
| `ARG`         | Définit une variable utilisable **uniquement pendant la construction** |
| `USER`        | Définit l’utilisateur utilisé pour exécuter les instructions suivantes |
| `HEALTHCHECK` | Définit une commande pour vérifier que le conteneur est sain           |
| `ONBUILD`     | Déclenche une instruction quand l’image est utilisée comme base        |
| `SHELL`       | Change le shell par défaut (`sh`, `bash`, etc.)                        |
| `STOPSIGNAL`  | Définit le signal d’arrêt envoyé (ex : `SIGKILL`)                      |

# Docker Volume
---
commande de base
```bash
# Créer un volume
docker volume create mon_volume

# Lister les volumes existants
docker volume ls

# Inspecter un volume
docker volume inspect mon_volume

# Supprimer un volume
docker volume rm mon_volume

# Supprimer tous les volumes non utilisés
docker volume prune
```
Utiliser un volume dans un conteneur
```bash
# Lier un volume Docker à un dossier dans le conteneur
docker run -v mon_volume:/app/data my_image
```
 Cela signifie :

    mon_volume est un volume Docker.

    /app/data est le chemin dans le conteneur.
## Lier un dossier local comme volume 
```bash
docker run -v $(pwd)/data:/app/data my_image
```
## exemple pratique
```bash
# Créer un volume
docker volume create data_volume

# Lancer un conteneur avec ce volume
docker run -it --name test_volume -v data_volume:/data alpine sh

# À l’intérieur du conteneur, écrire un fichier
echo "Bonjour" > /data/bonjour.txt

# Quitter et relancer un nouveau conteneur avec le même volume
docker run -it -v data_volume:/data alpine cat /data/bonjour.txt
```
ce commande va montrer que le fichier est toujour la

# Docker Network
Docker utilise des réseaux virtuels pour connecter les conteneurs entre eux, avec ou sans accès à Internet. Les réseaux personnalisés facilitent la communication inter-conteneurs par nom.

## commande de base

```bash

# Lister les réseaux disponibles
docker network ls

# Créer un réseau personnalisé (bridge par défaut)
docker network create mon_reseau

# Supprimer un réseau
docker network rm mon_reseau

# Inspecter les détails d’un réseau
docker network inspect mon_reseau
```
## utiliser un reseau dans un conteneur

```bash
docker run -d --name conteneur1 --network mon_reseau nginx
docker run -it --name conteneur2 --network mon_reseau alpine sh
```
À l’intérieur de conteneur2, ont peut faire un ping conteneur1 (résolution de nom automatique via Docker DNS).

## Se connecter / déconnecter à un réseau

```bash
# Connecter un conteneur à un réseau
docker network connect mon_reseau mon_conteneur

# Déconnecter un conteneur d’un réseau
docker network disconnect mon_reseau mon_conteneur
```
## type de reseau docker

| Type      | Description                                                          |
| --------- | -------------------------------------------------------------------- |
| `bridge`  | Réseau privé par défaut entre conteneurs sur une même machine.       |
| `host`    | Partage le réseau de l’hôte (pas d'isolation).                       |
| `none`    | Pas de réseau.                                                       |
| `overlay` | Réseau multi-hôtes (Swarm).                                          |
| `macvlan` | Donne une adresse MAC/IPv4 à chaque conteneur (accès réseau direct). |

## exemple pratique
```bash
docker network create mon_reseau
docker run -d --name web1 --network mon_reseau nginx
docker run -it --name client --network mon_reseau alpine sh
```
le client peut faire:
```bash
wget web1
```
Et le client obtiens la page web du conteneur web1.


