# 🔧 Commandes Docker de base

> Maîtriser les commandes essentielles

## Contenu du chapitre

### [[01-docker-run|🚀 docker run - Créer et lancer des conteneurs]]
- Syntaxe de base
- Options principales (-d, -p, -v, --name, -e, -it)
- Exemples pratiques

### [[02-gestion-conteneurs|📦 Gérer les conteneurs]]
- `docker ps` - Lister les conteneurs
- `docker start/stop/restart` - Contrôler le cycle de vie
- `docker rm` - Supprimer des conteneurs
- `docker logs` - Voir les logs
- `docker exec` - Exécuter des commandes

### [[03-gestion-images|🖼️ Gérer les images]]
- `docker images` - Lister les images
- `docker pull` - Télécharger des images
- `docker rmi` - Supprimer des images
- `docker tag` - Tagger des images
- `docker push` - Envoyer vers un registry

### [[04-attach-detach|🔌 Attach et Detach]]
- Mode foreground vs background
- `docker run -d` - Détacher
- `docker attach` - Réattacher
- `Ctrl+P` puis `Ctrl+Q` - Détacher sans arrêter

### [[05-inspection-debug|🔍 Inspection et Debug]]
- `docker inspect` - Détails complets
- `docker logs` - Logs
- `docker top` - Processus en cours
- `docker stats` - Utilisation ressources
- `docker diff` - Modifications filesystem

### [[06-nettoyage|🧹 Nettoyage et maintenance]]
- `docker system prune` - Nettoyage global
- `docker container prune` - Nettoyer conteneurs
- `docker image prune` - Nettoyer images
- `docker volume prune` - Nettoyer volumes
- `docker system df` - Utilisation disque

## 🎯 Objectifs

À la fin de ce chapitre, vous saurez :
- ✅ Lancer et gérer des conteneurs
- ✅ Gérer les images Docker
- ✅ Déboguer des problèmes
- ✅ Maintenir votre environnement Docker propre

## 📝 Cheatsheet rapide

```bash
# Lancer un conteneur
docker run nginx

# Lister conteneurs en cours
docker ps

# Arrêter un conteneur
docker stop <container-id>

# Voir les logs
docker logs <container-id>

# Nettoyer
docker system prune -a
```

---

**Commencer → [[01-docker-run|docker run]]**
