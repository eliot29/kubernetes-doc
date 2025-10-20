# ⚙️ Installation de Docker

## Choix de l'édition

### Docker Desktop (Recommandé pour débuter)

**Pour qui ?**
- Développeurs sur Mac ou Windows
- Interface graphique intuitive
- Environnement tout-en-un

**Inclut :**
- Docker Engine
- Docker CLI
- Docker Compose
- Kubernetes (optionnel)
- Interface graphique

**Téléchargement :**
- [Docker Desktop pour Mac](https://docs.docker.com/desktop/install/mac-install/)
- [Docker Desktop pour Windows](https://docs.docker.com/desktop/install/windows-install/)

### Docker Engine (Linux)

**Pour qui ?**
- Serveurs Linux
- Environnements de production
- Utilisateurs expérimentés

**Plus léger** (pas d'interface graphique)

---

## Installation sur Linux (Ubuntu)

### Prérequis

- Ubuntu 64-bit (versions supportées : 20.04, 22.04, 23.04, 23.10)
- Accès sudo

### Méthode 1 : Script d'installation rapide (Recommandé)

```bash
# Télécharger et exécuter le script officiel
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Ajouter votre utilisateur au groupe docker (évite sudo)
sudo usermod -aG docker $USER

# Redémarrer la session ou exécuter
newgrp docker

# Vérifier l'installation
docker --version
```

### Méthode 2 : Installation manuelle (Production)

#### 1. Désinstaller les anciennes versions

```bash
sudo apt-get remove docker docker.io docker-engine containerd runc
```

#### 2. Installer les prérequis

```bash
sudo apt-get update

sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

#### 3. Ajouter la clé GPG officielle de Docker

```bash
sudo mkdir -m 0755 -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

#### 4. Ajouter le dépôt Docker

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

#### 5. Installer Docker Engine

```bash
sudo apt-get update

sudo apt-get install \
    docker-ce \
    docker-ce-cli \
    containerd.io \
    docker-buildx-plugin \
    docker-compose-plugin
```

#### 6. Vérifier l'installation

```bash
sudo docker run hello-world
```

### Post-installation (Important)

#### Permettre à votre utilisateur d'exécuter Docker sans sudo

```bash
# Créer le groupe docker (si n'existe pas)
sudo groupadd docker

# Ajouter votre utilisateur au groupe
sudo usermod -aG docker $USER

# Activer les changements (redémarrez votre session ou exécutez)
newgrp docker

# Tester sans sudo
docker run hello-world
```

#### Configurer Docker pour démarrer au boot

```bash
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```

---

## Installation sur Windows

### Prérequis

- Windows 10 64-bit (version 21H2 ou supérieure) **OU** Windows 11
- WSL 2 activé (recommandé)
- Virtualisation activée dans le BIOS

### Étapes

1. **Télécharger Docker Desktop**
   - Aller sur [Docker Desktop pour Windows](https://docs.docker.com/desktop/install/windows-install/)
   - Télécharger `Docker Desktop Installer.exe`

2. **Exécuter l'installateur**
   - Double-cliquer sur `Docker Desktop Installer.exe`
   - Suivre les instructions

3. **Configurer WSL 2**
   ```powershell
   # Ouvrir PowerShell en administrateur
   wsl --install
   wsl --set-default-version 2
   ```

4. **Démarrer Docker Desktop**
   - Lancer Docker Desktop depuis le menu Démarrer
   - Attendre que Docker démarre

5. **Vérifier l'installation**
   ```powershell
   docker --version
   docker run hello-world
   ```

---

## Installation sur macOS

### Prérequis

- macOS 11 (Big Sur) ou supérieur
- Mac avec Apple Silicon (M1/M2) ou Intel

### Étapes

1. **Télécharger Docker Desktop**
   - [Pour Apple Silicon (M1/M2)](https://desktop.docker.com/mac/main/arm64/Docker.dmg)
   - [Pour Intel](https://desktop.docker.com/mac/main/amd64/Docker.dmg)

2. **Installer**
   - Double-cliquer sur `Docker.dmg`
   - Glisser Docker dans Applications

3. **Démarrer Docker Desktop**
   - Ouvrir Docker depuis Applications
   - Accepter les autorisations

4. **Vérifier l'installation**
   ```bash
   docker --version
   docker run hello-world
   ```

---

## Vérification de l'installation

### 1. Vérifier la version

```bash
docker --version
# Docker version 24.0.7, build afdd53b

docker version
# Affiche les versions du client ET du serveur
```

### 2. Vérifier les informations système

```bash
docker info
```

**Vérifiez :**
- ✅ `Server Version`: version de Docker Engine
- ✅ `Storage Driver`: overlay2 (recommandé sur Linux)
- ✅ `Cgroup Driver`: systemd ou cgroupfs
- ✅ `Runtimes`: runc (par défaut)

### 3. Tester avec Hello World

```bash
docker run hello-world
```

**Ce qui devrait se passer :**
```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
...
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
```

### 4. Lancer un conteneur interactif

```bash
docker run -it ubuntu bash
```

Vous êtes maintenant **dans un conteneur Ubuntu** !

```bash
# Dans le conteneur
root@abc123:/# cat /etc/os-release
# PRETTY_NAME="Ubuntu 22.04.3 LTS"

root@abc123:/# exit
# Retour sur votre machine hôte
```

### 5. Tester le daemon Docker

```bash
# Sur Linux
sudo systemctl status docker

# Devrait afficher "active (running)"
```

---

## Premier conteneur utile

### Lancer un serveur web NGINX

```bash
docker run -d -p 8080:80 --name mon-nginx nginx
```

**Explication :**
- `-d` : Détaché (en arrière-plan)
- `-p 8080:80` : Map le port 8080 de l'hôte → port 80 du conteneur
- `--name mon-nginx` : Nom du conteneur
- `nginx` : Image à utiliser

**Tester :**
```bash
# Ouvrir dans le navigateur
http://localhost:8080

# Ou avec curl
curl http://localhost:8080
```

**Gérer le conteneur :**
```bash
# Voir les logs
docker logs mon-nginx

# Arrêter le conteneur
docker stop mon-nginx

# Redémarrer
docker start mon-nginx

# Supprimer
docker rm -f mon-nginx
```

---

## Commandes utiles post-installation

```bash
# Lister les conteneurs en cours
docker ps

# Lister tous les conteneurs (même arrêtés)
docker ps -a

# Lister les images
docker images

# Nettoyer (supprimer conteneurs arrêtés, images inutilisées, etc.)
docker system prune -a

# Voir l'utilisation disque
docker system df

# Arrêter tous les conteneurs
docker stop $(docker ps -q)

# Supprimer tous les conteneurs
docker rm $(docker ps -aq)
```

---

## Problèmes courants

### Permission denied (Linux)

**Erreur :**
```
Got permission denied while trying to connect to the Docker daemon socket
```

**Solution :**
```bash
sudo usermod -aG docker $USER
newgrp docker
# Ou redémarrer votre session
```

### Docker daemon not running (Windows/Mac)

**Erreur :**
```
Cannot connect to the Docker daemon
```

**Solution :**
- Vérifier que Docker Desktop est lancé
- Redémarrer Docker Desktop

### WSL 2 installation incomplete (Windows)

**Solution :**
```powershell
# PowerShell en admin
wsl --install
wsl --update
```

### Virtualisation désactivée

**Sur Windows :**
- Activer Hyper-V dans le BIOS
- Activer "Virtual Machine Platform" dans les fonctionnalités Windows

**Sur Mac :**
- Vérifier que la virtualisation est activée (généralement OK par défaut)

---

## Configuration avancée (optionnel)

### Configurer le daemon Docker

Fichier : `/etc/docker/daemon.json` (Linux)

```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  },
  "storage-driver": "overlay2"
}
```

Redémarrer après modification :
```bash
sudo systemctl restart docker
```

### Définir un registry privé par défaut

```bash
# Login à un registry privé
docker login my-registry.com

# Credentials stockées dans ~/.docker/config.json
```

---

## Tester avec whalesay 🐳

```bash
docker run docker/whalesay cowsay "Docker fonctionne !"
```

Résultat :
```
 _______________________
< Docker fonctionne ! >
 -----------------------
    \
     \
      \
                    ##        .
              ## ## ##       ==
           ## ## ## ##      ===
       /""""""""""""""""___/ ===
  ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~
       \______ o          __/
        \    \        __/
          \____\______/
```

---

## Prochaine étape

Maintenant que Docker est installé, explorons les **concepts clés** !

→ [[05-Concepts-Cles|Concepts clés de Docker]]
← [[03-Architecture-Docker|Architecture Docker]]
