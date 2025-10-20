# 🐳 Rappels Docker

> Section de rappel complète sur Docker avant d'aborder Kubernetes

## 📌 Contexte

Cette section reprend les fondamentaux de Docker issus du [cours KodeKloud](https://notes.kodekloud.com/docs/Docker-Training-Course-for-the-Absolute-Beginner/). C'est une **section de rappel** pour bien comprendre les conteneurs avant de passer à l'orchestration avec Kubernetes.

## 📑 Chapitres

### [[01-Introduction/00-INDEX|01 - Introduction à Docker]]
- Qu'est-ce que Docker ?
- Pourquoi utiliser les conteneurs ?
- Conteneurs vs Machines Virtuelles
- Architecture Docker
- Installation

### [[02-Commandes-de-base/00-INDEX|02 - Commandes de base]]
- `docker run`, `docker ps`, `docker stop`
- Gestion des conteneurs et images
- Inspection et logs
- Attach et Detach

### [[03-Images/00-INDEX|03 - Images Docker]]
- Comprendre les images
- Layers et cache
- Docker Hub
- Tags et versions

### [[04-Conteneurs/00-INDEX|04 - Conteneurs]]
- Lifecycle des conteneurs
- Mode interactif (`-it`)
- Port mapping (`-p`)
- Variables d'environnement

### [[05-Volumes/00-INDEX|05 - Volumes et persistance]]
- Types de stockage (volumes, bind mounts, tmpfs)
- Volume mapping (`-v`)
- Architecture en couches
- Copy-on-Write

### [[06-Reseaux/00-INDEX|06 - Réseaux Docker]]
- Types de réseaux (Bridge, Host, None)
- Communication inter-conteneurs
- DNS intégré
- Port mapping

### [[07-Dockerfile/00-INDEX|07 - Dockerfile]]
- Instructions de base (FROM, RUN, COPY, CMD, ENTRYPOINT)
- Build d'images personnalisées
- Layers et optimisation
- Best practices

### [[08-Docker-Compose/00-INDEX|08 - Docker Compose]]
- Syntaxe YAML
- Orchestration multi-conteneurs
- Networks et volumes
- Versions du format Compose

### [[09-Registry/00-INDEX|09 - Docker Registry]]
- Docker Hub
- Registry privé
- Push/Pull d'images
- Authentification

## 🎯 Objectifs

À la fin de cette section, vous aurez :
- ✅ Une bonne compréhension des conteneurs Docker
- ✅ La capacité de créer et gérer des conteneurs
- ✅ Les bases pour construire des images personnalisées
- ✅ Une vision d'ensemble de l'écosystème Docker

## 🔗 Ressources

- [KodeKloud - Docker Course](https://notes.kodekloud.com/docs/Docker-Training-Course-for-the-Absolute-Beginner/)
- [Documentation officielle Docker](https://docs.docker.com/)
- [Docker Hub](https://hub.docker.com/)

## ⚡ Raccourci

**Déjà à l'aise avec Docker ?** Passez directement à [[01-Kubernetes-Core/00-INDEX|Kubernetes Core]]

---

**Commencer → [[01-Introduction/01-Pourquoi-Docker|Pourquoi Docker ?]]**
