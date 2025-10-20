# 🔧 crictl vs docker - Comprendre les différences

> Pourquoi utiliser crictl au lieu de docker ? Qui utilise quoi et quand ?

## ❓ La question

**"Si j'ai déjà Docker et `docker ps`, pourquoi ai-je besoin de `crictl` ?"**

C'est une excellente question ! La réponse courte : **crictl** est fait pour **déboguer Kubernetes**, pas pour le développement local.

---

## 🎯 Qu'est-ce que crictl ?

### Définition

**crictl** (CRI Command Line Interface Tool) est un **outil en ligne de commande** pour interagir avec n'importe quel runtime de conteneurs compatible **CRI**.

**Créé par :** Kubernetes SIG (Special Interest Group)
**But :** Déboguer les runtimes CRI et les conteneurs Kubernetes

### Ce que crictl n'est PAS

❌ **Ce n'est PAS** un remplaçant de Docker pour le développement
❌ **Ce n'est PAS** un remplaçant de `kubectl`
❌ **Ce n'est PAS** pour construire des images
❌ **Ce n'est PAS** pour gérer des Pods Kubernetes

### Ce que crictl EST

✅ Un outil de **bas niveau** pour interagir avec CRI
✅ Pour **déboguer** des problèmes de runtime
✅ Pour **inspecter** des conteneurs Kubernetes
✅ Compatible avec **containerd**, **CRI-O**, et tout runtime CRI

---

## 🤔 Pourquoi crictl existe ?

### Le problème avant crictl

Avant, pour déboguer Kubernetes :
- Avec Docker : `docker ps`, `docker logs`, etc. ✅
- Avec containerd : `ctr` (interface différente) 🤷
- Avec CRI-O : `podman` ou commandes spécifiques 🤷

**Problème :** Chaque runtime a **ses propres commandes** !

### La solution : crictl

Une **interface unifiée** pour tous les runtimes CRI :

```
crictl → CRI (standard) → containerd / CRI-O / autre
```

**Avantages :**
- ✅ Même commandes quel que soit le runtime
- ✅ Respecte l'interface CRI (comme Kubernetes)
- ✅ Parfait pour déboguer Kubernetes

---

## 👥 Qui utilise quoi et quand ?

### Tableau comparatif complet

| Outil | Qui l'utilise ? | Quand ? | Pour quoi ? | Niveau |
|-------|----------------|---------|-------------|--------|
| **docker** | Développeurs | Développement local | Créer, tester des conteneurs | Application |
| **crictl** | Ops / SRE / Admins K8s | Debug cluster K8s | Inspecter conteneurs K8s | Runtime (bas niveau) |
| **kubectl** | Tout le monde | Gérer Kubernetes | Déployer, gérer des Pods | Orchestration (haut niveau) |
| **ctr** | Experts containerd | Debug containerd | Opérations avancées containerd | Runtime (très bas niveau) |
| **nerdctl** | Développeurs | Alternative Docker | Tester avec containerd | Application |

### Cas d'usage détaillés

#### 1. 🖥️ Développement local

**Utilisateur :** Développeur qui code une application

**Outil :** `docker`

**Pourquoi :**
- Build d'images (`docker build`)
- Test local rapide (`docker run`)
- Docker Compose pour multi-conteneurs
- Interface familière

**Exemples :**
```bash
# Build votre image
docker build -t myapp:1.0 .

# Tester localement
docker run -p 3000:3000 myapp:1.0

# Voir les logs
docker logs container-id

# Multi-conteneurs
docker-compose up
```

**Environnement :** Laptop, machine de dev

---

#### 2. ☸️ Gestion d'applications Kubernetes

**Utilisateur :** DevOps, développeur qui déploie

**Outil :** `kubectl`

**Pourquoi :**
- Gérer des Deployments, Services, ConfigMaps
- Voir le statut des Pods
- Appliquer des manifests YAML
- Interagir avec l'API Kubernetes

**Exemples :**
```bash
# Déployer une app
kubectl apply -f deployment.yaml

# Lister les Pods
kubectl get pods

# Voir les logs d'un Pod
kubectl logs pod-name

# Exécuter une commande dans un Pod
kubectl exec -it pod-name -- bash

# Scaling
kubectl scale deployment myapp --replicas=5
```

**Environnement :** Cluster Kubernetes (dev, staging, prod)

---

#### 3. 🔧 Debug du cluster Kubernetes (runtime)

**Utilisateur :** Admin K8s, SRE, support technique

**Outil :** `crictl`

**Pourquoi :**
- Déboguer des problèmes de runtime
- Voir les conteneurs au niveau CRI
- Inspecter les pod sandboxes
- Diagnostiquer des problèmes kubelet

**Exemples :**
```bash
# Lister les conteneurs (vue runtime)
crictl ps

# Lister les Pods (sandboxes)
crictl pods

# Voir les logs d'un conteneur
crictl logs container-id

# Inspecter un conteneur
crictl inspect container-id

# Voir les images
crictl images

# Stats des conteneurs
crictl stats
```

**Environnement :** Directement sur un Node Kubernetes

**Cas d'usage typiques :**
- "Pourquoi ce Pod ne démarre pas ?"
- "Le conteneur est créé mais ne start pas"
- "Problème de pull d'image"
- "Le kubelet ne répond pas, que se passe-t-il au niveau runtime ?"

---

#### 4. 🛠️ Opérations avancées containerd

**Utilisateur :** Expert containerd

**Outil :** `ctr` (containerd CLI)

**Pourquoi :**
- Accès complet à containerd (pas limité à CRI)
- Gestion des namespaces containerd
- Import/export d'images
- Opérations de maintenance

**Exemples :**
```bash
# Lister conteneurs dans le namespace k8s.io
ctr -n k8s.io containers list

# Importer une image
ctr images import image.tar

# Voir tous les namespaces
ctr namespaces list
```

**Environnement :** Node Kubernetes, serveur avec containerd

---

## 📊 Comparaison des commandes

### Commandes équivalentes

| Action | docker | crictl | kubectl |
|--------|--------|--------|---------|
| **Lister conteneurs** | `docker ps` | `crictl ps` | `kubectl get pods` |
| **Lister toutes** | `docker ps -a` | `crictl ps -a` | `kubectl get pods --all-namespaces` |
| **Voir logs** | `docker logs <id>` | `crictl logs <id>` | `kubectl logs <pod>` |
| **Exécuter commande** | `docker exec -it <id> sh` | `crictl exec -it <id> sh` | `kubectl exec -it <pod> -- sh` |
| **Inspecter** | `docker inspect <id>` | `crictl inspect <id>` | `kubectl describe pod <pod>` |
| **Stopper** | `docker stop <id>` | `crictl stop <id>` | `kubectl delete pod <pod>` |
| **Supprimer** | `docker rm <id>` | `crictl rm <id>` | `kubectl delete pod <pod>` |
| **Voir images** | `docker images` | `crictl images` | N/A (pas géré par k8s) |
| **Pull image** | `docker pull nginx` | `crictl pull nginx` | Automatique par kubelet |
| **Stats** | `docker stats` | `crictl stats` | `kubectl top pods` |

### Ce que chaque outil peut faire

| Capacité | docker | crictl | kubectl |
|----------|--------|--------|---------|
| Build d'images | ✅ | ❌ | ❌ |
| Run conteneur standalone | ✅ | ❌ | ❌ |
| Docker Compose | ✅ | ❌ | ❌ |
| Voir conteneurs K8s | ⚠️ (si Docker runtime) | ✅ | ✅ (niveau Pod) |
| Déboguer runtime CRI | ❌ | ✅ | ❌ |
| Gérer Deployments | ❌ | ❌ | ✅ |
| Scaling | ❌ | ❌ | ✅ |
| Services K8s | ❌ | ❌ | ✅ |
| ConfigMaps/Secrets | ❌ | ❌ | ✅ |

---

## 🎯 Scénarios réels

### Scénario 1 : Développeur local

**Contexte :** Vous développez une app Node.js

```bash
# Votre workflow
docker build -t myapp:dev .
docker run -p 3000:3000 -v $(pwd):/app myapp:dev
docker logs myapp-container
```

**Outil utilisé :** `docker` ✅

**Pourquoi pas crictl ?** Vous n'avez pas de cluster Kubernetes, crictl ne serait d'aucune utilité.

---

### Scénario 2 : Déploiement en production

**Contexte :** Vous déployez votre app sur Kubernetes

```bash
# Votre workflow
kubectl apply -f deployment.yaml
kubectl get pods
kubectl logs myapp-pod-abc123
kubectl port-forward myapp-pod-abc123 3000:3000
```

**Outil utilisé :** `kubectl` ✅

**Pourquoi pas docker ?** Vous ne gérez pas les conteneurs individuellement, Kubernetes s'en charge.

**Pourquoi pas crictl ?** Tout fonctionne, pas besoin de descendre au niveau runtime.

---

### Scénario 3 : Pod qui ne démarre pas

**Contexte :** Un Pod est en statut `CrashLoopBackOff` et vous ne comprenez pas pourquoi

```bash
# 1. Niveau Kubernetes (kubectl)
kubectl describe pod myapp-pod-abc123
# Events: Failed to pull image "myapp:1.0": rpc error...

# 2. Niveau runtime (crictl) - sur le Node
ssh worker-node-1
crictl images
# myapp:1.0 manquante !

crictl pull myapp:1.0
# Error: authentication required

# Ah ! Problème d'auth au registry
```

**Outils utilisés :** `kubectl` puis `crictl` ✅

**Pourquoi crictl ?** kubectl vous dit qu'il y a un problème, crictl vous permet de déboguer **au niveau du runtime**.

---

### Scénario 4 : Kubelet qui ne répond plus

**Contexte :** Un Node a des problèmes, kubelet semble bloqué

```bash
# Sur le Node problématique
ssh worker-node-2

# Vérifier l'état du kubelet
systemctl status kubelet
# kubelet.service - Kubernetes Kubelet
#   Active: active (running)

# Vérifier ce que voit le runtime
crictl ps
# CONTAINER ID   IMAGE   STATE   NAME
# (rien ou conteneurs bloqués)

crictl pods
# POD ID   NAME   STATE
# abc123   web    Ready

# Inspecter les logs du runtime
crictl logs --tail=50 abc123

# Vérifier les images
crictl images
```

**Outil utilisé :** `crictl` ✅

**Pourquoi crictl ?** Vous êtes au niveau système, kubectl ne fonctionne peut-être pas, crictl interroge **directement le runtime via CRI**.

---

## 📋 Qui utilise quoi ? Tableau de décision

### Par rôle

| Rôle | Outil principal | Outils secondaires | Contexte |
|------|----------------|-------------------|----------|
| **Développeur applicatif** | `docker` | `kubectl` (pour déployer) | Développement local + déploiement |
| **DevOps / SRE** | `kubectl` | `crictl` (debug), `docker` (CI/CD) | Gestion cluster + debug |
| **Admin Kubernetes** | `kubectl` | `crictl` (debug runtime) | Administration cluster |
| **Support technique** | `crictl` | `kubectl`, logs système | Debug avancé |
| **Expert containerd** | `ctr` | `crictl`, `kubectl` | Maintenance runtime |

### Par environnement

| Environnement | Outil | Pourquoi |
|---------------|-------|----------|
| **Laptop de dev** | `docker` | Développement local sans K8s |
| **Minikube / Kind (local K8s)** | `kubectl` + `docker` | K8s local + build images |
| **Cluster K8s dev/staging** | `kubectl` | Gestion applications |
| **Cluster K8s prod** | `kubectl` | Déploiement et monitoring |
| **Node K8s (SSH)** | `crictl` | Debug au niveau runtime |
| **CI/CD pipeline** | `docker` + `kubectl` | Build images + déploiement |

### Par problème à résoudre

| Problème | Outil | Commande exemple |
|----------|-------|------------------|
| "Je veux tester mon app localement" | `docker` | `docker run -p 3000:3000 myapp` |
| "Je veux déployer sur K8s" | `kubectl` | `kubectl apply -f deployment.yaml` |
| "Mon Pod est en CrashLoop" | `kubectl` → `crictl` | `kubectl describe` puis `crictl logs` |
| "L'image ne se pull pas" | `crictl` | `crictl pull nginx` |
| "Le kubelet ne crée pas les conteneurs" | `crictl` | `crictl ps -a` |
| "Combien de CPU utilise ce Pod ?" | `kubectl` | `kubectl top pod myapp` |
| "Combien de CPU utilise ce conteneur ?" | `crictl` | `crictl stats container-id` |

---

## 🔄 Migration Docker → containerd + crictl

### Avant (avec Docker)

```bash
# Sur un Node Kubernetes avec Docker
docker ps  # Voir les conteneurs K8s
docker logs <container-id>
docker exec -it <container-id> sh
```

### Après (avec containerd)

```bash
# Sur un Node Kubernetes avec containerd
crictl ps  # Voir les conteneurs K8s
crictl logs <container-id>
crictl exec -it <container-id> sh
```

**Commandes presque identiques !** C'est voulu.

---

## 💡 Installation de crictl

### Sur Linux

```bash
# Télécharger crictl
VERSION="v1.28.0"
wget https://github.com/kubernetes-sigs/cri-tools/releases/download/$VERSION/crictl-$VERSION-linux-amd64.tar.gz

# Extraire
sudo tar zxvf crictl-$VERSION-linux-amd64.tar.gz -C /usr/local/bin

# Vérifier
crictl --version
```

### Configuration

Créer `/etc/crictl.yaml` :

```yaml
runtime-endpoint: unix:///run/containerd/containerd.sock
image-endpoint: unix:///run/containerd/containerd.sock
timeout: 10
debug: false
```

**Pour CRI-O**, utiliser :
```yaml
runtime-endpoint: unix:///var/run/crio/crio.sock
```

---

## 📝 Exemples pratiques crictl

### Lister les Pods (sandboxes)

```bash
crictl pods
```

Résultat :
```
POD ID              CREATED             STATE   NAME                NAMESPACE
abc123def456        10 minutes ago      Ready   nginx-pod           default
ghi789jkl012        1 hour ago          Ready   redis-pod           kube-system
```

### Lister les conteneurs

```bash
crictl ps
```

Résultat :
```
CONTAINER ID        IMAGE               CREATED             STATE   NAME
mno345pqr678        nginx:latest        10 minutes ago      Running nginx
stu901vwx234        redis:7-alpine      1 hour ago          Running redis
```

### Voir les logs

```bash
crictl logs mno345pqr678
```

### Inspecter un conteneur

```bash
crictl inspect mno345pqr678
```

Résultat (JSON) :
```json
{
  "status": {
    "id": "mno345pqr678",
    "metadata": {
      "name": "nginx"
    },
    "state": "CONTAINER_RUNNING",
    "createdAt": "2024-01-15T10:00:00Z",
    "startedAt": "2024-01-15T10:00:01Z",
    "image": {
      "image": "docker.io/library/nginx:latest"
    }
  }
}
```

### Exécuter une commande

```bash
crictl exec -it mno345pqr678 sh
```

---

## ⚠️ Limitations de crictl

### Ce que crictl NE PEUT PAS faire

❌ **Build d'images** → Utiliser `docker build` ou `buildah`
❌ **Créer des Pods** → Utiliser `kubectl`
❌ **Gérer des Services K8s** → Utiliser `kubectl`
❌ **Docker Compose** → Utiliser `docker-compose`
❌ **Pusher des images** → Utiliser `docker push` ou `skopeo`

### Pourquoi ces limitations ?

crictl est un outil de **debug CRI**, pas un outil de développement complet. Son but est d'inspecter ce que fait Kubernetes via CRI.

---

## 🎓 Conclusion

### Résumé en une phrase

| Outil | En une phrase |
|-------|---------------|
| **docker** | Pour **développer** et **tester** des conteneurs localement |
| **crictl** | Pour **déboguer** le runtime CRI sur un Node Kubernetes |
| **kubectl** | Pour **gérer** des applications sur Kubernetes |

### Mémo

```
Développement local → docker
Gestion K8s → kubectl
Debug runtime K8s → crictl
```

### Quand installer crictl ?

✅ Vous êtes admin d'un cluster Kubernetes
✅ Vous devez déboguer des problèmes de runtime
✅ Vous travaillez avec containerd ou CRI-O

❌ Vous développez en local sans Kubernetes
❌ Vous débutez avec Docker
❌ Vous utilisez uniquement kubectl sans problème

---

**Prochaine étape :** [[07-Plugins-CRI|Plugins CRI (containerd vs CRI-O)]]

← [[05-ImageService|ImageService]]
