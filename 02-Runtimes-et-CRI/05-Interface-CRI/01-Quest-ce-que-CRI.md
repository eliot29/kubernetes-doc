# 📖 Qu'est-ce que CRI ?

## Définition

**CRI (Container Runtime Interface)** est une **spécification d'API** créée par Kubernetes pour standardiser la communication entre le kubelet et les runtimes de conteneurs.

C'est une **interface** (comme une prise USB) qui permet à Kubernetes de fonctionner avec **n'importe quel runtime** qui implémente cette interface.

## Analogie : La prise USB

```
┌──────────────────────────────────────┐
│  Peu importe la marque du câble      │
│  (Apple, Samsung, Anker...)          │
│                                      │
│  Si c'est USB-C, ça rentre dans     │
│  n'importe quel port USB-C           │
└──────────────────────────────────────┘
```

De même :
```
┌──────────────────────────────────────┐
│  Peu importe le runtime              │
│  (containerd, CRI-O, etc.)           │
│                                      │
│  Si il implémente CRI, Kubernetes    │
│  peut l'utiliser                     │
└──────────────────────────────────────┘
```

## Pourquoi CRI a été créé ?

### Le problème avant CRI

Au début, Kubernetes était **fortement couplé** à Docker :

```
Kubernetes → Docker seulement
```

**Problèmes :**
- ❌ Impossible d'utiliser un autre runtime
- ❌ Code Docker intégré directement dans Kubernetes (dockershim)
- ❌ Dépendance forte à Docker
- ❌ Difficile d'innover avec de nouveaux runtimes

### La solution : CRI

Kubernetes a créé une **interface standardisée** :

```
Kubernetes → CRI → n'importe quel runtime compatible
```

**Avantages :**
- ✅ Kubernetes ne connaît que l'interface CRI
- ✅ N'importe qui peut créer un runtime compatible CRI
- ✅ Pas de couplage fort
- ✅ Facilite l'innovation (nouveaux runtimes)
- ✅ Simplification du code Kubernetes

## Historique

### 2014-2016 : Ère Docker

```
Kubernetes
    ↓
  Docker (seul choix)
```

Kubernetes dépendait complètement de Docker.

### 2016 : Introduction de CRI

```
Kubernetes v1.5 (décembre 2016)
    ↓
  CRI API (interface)
    ↓
  ├─ containerd
  ├─ CRI-O
  └─ Docker (via dockershim)
```

Kubernetes introduit CRI pour supporter plusieurs runtimes.

**dockershim** = couche de compatibilité pour Docker (qui n'était pas compatible CRI nativement)

### 2020 : Déprécation de dockershim

```
Kubernetes v1.20 (décembre 2020)
```

Annonce de la déprécation de dockershim.

**Pourquoi ?**
- dockershim était une couche supplémentaire à maintenir
- containerd (utilisé par Docker) supporte CRI directement
- Simplification du code Kubernetes

### 2022 : Suppression de dockershim

```
Kubernetes v1.24 (mai 2022)
```

dockershim est **complètement retiré** de Kubernetes.

**Conséquence :**
- Docker n'est plus supporté comme runtime Kubernetes
- Utiliser containerd ou CRI-O directement
- Les images Docker continuent de fonctionner (format OCI)

## Architecture avant et après CRI

### Avant CRI (avec Docker)

```
┌──────────────────────┐
│    kubectl run       │
└──────────┬───────────┘
           │
┌──────────▼───────────┐
│   kube-apiserver     │
└──────────┬───────────┘
           │
┌──────────▼───────────┐
│      kubelet         │
└──────────┬───────────┘
           │
┌──────────▼───────────┐
│    dockershim        │  ← Couche de compatibilité
└──────────┬───────────┘
           │
┌──────────▼───────────┐
│    Docker Engine     │
└──────────┬───────────┘
           │
┌──────────▼───────────┐
│     containerd       │
└──────────┬───────────┘
           │
┌──────────▼───────────┐
│       runc           │
└──────────────────────┘
```

**Problème :** Trop de couches ! dockershim + Docker Engine alors que containerd suffit.

### Après CRI (sans Docker)

```
┌──────────────────────┐
│    kubectl run       │
└──────────┬───────────┘
           │
┌──────────▼───────────┐
│   kube-apiserver     │
└──────────┬───────────┘
           │
┌──────────▼───────────┐
│      kubelet         │
└──────────┬───────────┘
           │
┌──────────▼───────────┐
│    CRI (gRPC)        │  ← Interface standardisée
└──────────┬───────────┘
           │
    ┌──────┴──────┐
    │             │
┌───▼────┐   ┌────▼─────┐
│containerd   │ CRI-O    │
└───┬────┘   └────┬─────┘
    │             │
    └──────┬──────┘
           │
┌──────────▼───────────┐
│       runc           │
└──────────────────────┘
```

**Avantages :**
- ✅ Moins de couches
- ✅ Plus simple
- ✅ Meilleure performance
- ✅ Choix du runtime

## Spécification CRI

CRI est une **API gRPC** (Google Remote Procedure Call) composée de deux services principaux :

### 1. RuntimeService

Gère le **cycle de vie des conteneurs** :

```
- RunPodSandbox       → Créer l'environnement réseau du Pod
- StopPodSandbox      → Arrêter le sandbox
- RemovePodSandbox    → Supprimer le sandbox
- CreateContainer     → Créer un conteneur
- StartContainer      → Démarrer un conteneur
- StopContainer       → Arrêter un conteneur
- RemoveContainer     → Supprimer un conteneur
- ListContainers      → Lister les conteneurs
- ContainerStatus     → Statut d'un conteneur
- Exec                → Exécuter une commande dans un conteneur
```

### 2. ImageService

Gère les **images** :

```
- PullImage      → Télécharger une image
- ListImages     → Lister les images
- RemoveImage    → Supprimer une image
- ImageStatus    → Statut d'une image
```

## Qui implémente CRI ?

| Runtime | Type d'implémentation | Status |
|---------|----------------------|--------|
| **containerd** | Via plugin CRI | ✅ Production |
| **CRI-O** | Implémentation native | ✅ Production |
| **Docker** | Via dockershim (retiré) | ❌ Déprécié depuis K8s 1.24 |
| **Mirantis cri-dockerd** | Shim externe pour Docker | ⚠️ Communauté |

### containerd (via plugin)

```
kubelet → CRI gRPC → containerd CRI plugin → containerd core → runc
```

containerd inclut un **plugin CRI** qui expose l'API CRI.

### CRI-O (natif)

```
kubelet → CRI gRPC → CRI-O → runc
```

CRI-O est conçu **spécifiquement** pour implémenter CRI. C'est sa raison d'être.

### Docker (retiré)

```
kubelet → dockershim → Docker API → containerd → runc
```

Trop de couches, c'est pourquoi dockershim a été retiré.

## Ce que CRI ne fait PAS

❌ CRI n'est **pas** un runtime
❌ CRI ne **remplace pas** containerd ou CRI-O
❌ CRI ne **gère pas** directement les conteneurs

✅ CRI est **uniquement une interface** (comme une API REST)

## Exemple concret : Créer un Pod

Quand vous faites `kubectl run nginx --image=nginx`, voici ce qui se passe avec CRI :

```bash
# 1. kubectl envoie la requête à l'API Server
kubectl run nginx --image=nginx

# 2. Scheduler assigne le Pod à un Node

# 3. kubelet sur le Node reçoit la commande

# 4. kubelet appelle CRI : RunPodSandbox
→ CRI call: RunPodSandbox(metadata, network_config)
→ containerd crée les namespaces réseau

# 5. kubelet appelle CRI : PullImage
→ CRI call: PullImage("nginx:latest")
→ containerd télécharge l'image

# 6. kubelet appelle CRI : CreateContainer
→ CRI call: CreateContainer(pod_sandbox_id, container_config, image)
→ containerd crée le conteneur

# 7. kubelet appelle CRI : StartContainer
→ CRI call: StartContainer(container_id)
→ containerd → runc démarre le conteneur

# 8. Pod est Running !
```

**Tout passe par CRI !** kubelet ne sait même pas si c'est containerd ou CRI-O derrière.

## Avantages de CRI

### Pour Kubernetes
- ✅ Simplifie le code (pas de logique spécifique pour chaque runtime)
- ✅ Facilite les tests (mock de l'interface CRI)
- ✅ Réduit la maintenance

### Pour les runtime developers
- ✅ Spécification claire à implémenter
- ✅ Pas besoin de modifier Kubernetes
- ✅ Innovation facilitée

### Pour les utilisateurs
- ✅ Choix du runtime selon les besoins
- ✅ Transparence (les Pods fonctionnent pareil)
- ✅ Flexibilité

## Résumé

| Aspect | Description |
|--------|-------------|
| **Définition** | Interface standard entre Kubernetes et runtimes |
| **Type** | API gRPC |
| **Services** | RuntimeService + ImageService |
| **Créé** | 2016 (Kubernetes v1.5) |
| **Implémentations** | containerd (plugin), CRI-O (natif) |
| **But** | Découpler Kubernetes des runtimes |

**Prochaine étape** : Comprendre l'architecture détaillée de CRI

---

→ [[02-Architecture-CRI|Architecture CRI]]
← [[../04-runc-OCI/00-INDEX|runc et OCI]]
