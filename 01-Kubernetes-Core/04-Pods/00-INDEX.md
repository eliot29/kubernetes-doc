# 📦 Pods

> La plus petite unité déployable dans Kubernetes

## 📌 Contexte

Les **Pods** sont le concept fondamental de Kubernetes. Comprendre les Pods est essentiel pour maîtriser Kubernetes.

## Contenu du chapitre

### [[01-Pods-vs-Nodes|🏢 Pods vs Nodes - La différence expliquée]]
**⭐ À LIRE EN PREMIER !**

Explication détaillée pour comprendre la différence entre Nodes et Pods :
- Analogie de l'immeuble
- Définitions précises
- Schémas visuels
- Exemples concrets
- Questions fréquentes

**Si vous confondez Pods et Nodes, ce chapitre est fait pour vous !**

### [[02-Quest-ce-qu-un-Pod|📖 Qu'est-ce qu'un Pod ?]]
- Définition détaillée
- Anatomie d'un Pod
- Pourquoi des Pods et pas directement des conteneurs ?
- Cycle de vie d'un Pod

### [[03-Creer-un-Pod|🚀 Créer un Pod]]
- Avec `kubectl run`
- Avec un manifest YAML
- Premier Pod : nginx
- Vérifier l'état du Pod

### [[04-Multi-container-Pods|🎭 Pods multi-conteneurs]]
- Patterns : Sidecar, Ambassador, Adapter
- Communication entre conteneurs
- Partage de volumes
- Init containers

### [[05-Lifecycle-Pod|♻️ Lifecycle d'un Pod]]
- États (Pending, Running, Succeeded, Failed, Unknown)
- Phases de démarrage
- Liveness, Readiness, Startup probes
- Termination gracieuse

### [[06-Commandes-Pod|🔧 Commandes essentielles]]
- `kubectl get pods`
- `kubectl describe pod`
- `kubectl logs`
- `kubectl exec`
- `kubectl delete pod`

## 🎯 Objectifs

À la fin de ce chapitre, vous saurez :
- ✅ La différence entre un Node et un Pod
- ✅ Ce qu'est un Pod et pourquoi il existe
- ✅ Créer et gérer des Pods
- ✅ Utiliser des Pods multi-conteneurs
- ✅ Déboguer des problèmes de Pods

## 🗺️ Rappel architecture

```
Cluster Kubernetes
    │
    ├─ Node 1 (machine)
    │    ├─ Pod A (nginx)
    │    │    └─ Conteneur nginx
    │    └─ Pod B (app + sidecar)
    │         ├─ Conteneur app
    │         └─ Conteneur logger
    │
    └─ Node 2 (machine)
         └─ Pod C (redis)
              └─ Conteneur redis
```

**Mémo :**
- **Node** = La machine (serveur/VM)
- **Pod** = Groupe de conteneurs qui tournent ensemble
- Un Pod tourne **sur** un Node

## 📝 Exemple de manifest Pod simple

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

---

**⭐ Commencer par → [[01-Pods-vs-Nodes|Pods vs Nodes]]**
