# 📝 ReplicaSet - Structure YAML détaillée

> Comprendre chaque partie du YAML et comment les Pods sont liés au ReplicaSet

## 🎯 Structure complète d'un ReplicaSet

Voici un ReplicaSet avec **tous les éléments annotés** :

```yaml
apiVersion: apps/v1              # Version de l'API Kubernetes
kind: ReplicaSet                 # Type de ressource
metadata:                        # Métadonnées du ReplicaSet
  name: nginx-replicaset         # Nom du ReplicaSet (unique dans le namespace)
  namespace: default             # Namespace (optionnel, default par défaut)
  labels:                        # Labels du ReplicaSet lui-même
    app: nginx
    version: v1
    managed-by: kubectl
spec:                            # Spécification du ReplicaSet
  replicas: 3                    # Nombre de Pods désirés

  selector:                      # ⭐ CRUCIAL : Sélectionne les Pods à gérer
    matchLabels:                 # Labels qui doivent correspondre EXACTEMENT
      app: nginx                 # Doit matcher avec template.metadata.labels
      tier: frontend

  template:                      # ⭐ Template des Pods à créer
    metadata:                    # Métadonnées des Pods
      labels:                    # ⚠️ DOIVENT contenir au minimum les labels du selector
        app: nginx               # ✅ Correspond à selector.matchLabels.app
        tier: frontend           # ✅ Correspond à selector.matchLabels.tier
        version: "1.21"          # Labels additionnels (OK)

    spec:                        # Spécification du Pod
      containers:                # Liste des conteneurs
      - name: nginx              # Nom du conteneur
        image: nginx:1.21        # Image Docker
        ports:
        - containerPort: 80      # Port exposé par le conteneur
        resources:
          requests:              # Ressources demandées
            memory: "64Mi"
            cpu: "100m"
          limits:                # Limites de ressources
            memory: "128Mi"
            cpu: "200m"
```

---

## 🔗 matchLabels : Comment ça lie les Pods ?

### Le concept clé

Le **selector** avec **matchLabels** est ce qui permet au ReplicaSet de savoir **quels Pods** il doit gérer.

```
┌─────────────────────────────────────────┐
│         ReplicaSet                      │
│                                         │
│  selector:                              │
│    matchLabels:                         │
│      app: nginx        ←────┐           │
│      tier: frontend         │           │
└─────────────────────────────┼───────────┘
                              │
                              │ Cherche les Pods
                              │ avec ces labels
                              │
        ┌─────────────────────┼─────────────────┐
        │                     │                 │
        ▼                     ▼                 ▼
    ┌────────┐          ┌────────┐        ┌────────┐
    │ Pod 1  │          │ Pod 2  │        │ Pod 3  │
    │        │          │        │        │        │
    │ labels:│          │ labels:│        │ labels:│
    │  app:  │          │  app:  │        │  app:  │
    │  nginx │          │  nginx │        │  nginx │
    │  tier: │          │  tier: │        │  tier: │
    │frontend│          │frontend│        │frontend│
    └────────┘          └────────┘        └────────┘
```

### Règle d'or

**Les labels du `template.metadata.labels` DOIVENT contenir AU MINIMUM tous les labels du `selector.matchLabels`.**

### ✅ Exemple CORRECT

```yaml
spec:
  selector:
    matchLabels:
      app: nginx
      tier: frontend

  template:
    metadata:
      labels:
        app: nginx        # ✅ Présent dans selector
        tier: frontend    # ✅ Présent dans selector
        version: "1.21"   # ✅ Label additionnel OK
        env: prod         # ✅ Label additionnel OK
```

**Pourquoi ça marche ?**
- Le template a **au moins** les labels `app: nginx` et `tier: frontend`
- Les Pods créés auront ces labels
- Le ReplicaSet pourra les identifier et les gérer

### ❌ Exemple INCORRECT

```yaml
spec:
  selector:
    matchLabels:
      app: nginx
      tier: frontend

  template:
    metadata:
      labels:
        app: nginx        # ✅ Présent
        # ❌ MANQUE tier: frontend
        version: "1.21"
```

**Erreur lors de la création :**
```
The ReplicaSet "nginx-rs" is invalid:
spec.template.metadata.labels: Invalid value:
{"app":"nginx","version":"1.21"}:
`selector` does not match template `labels`
```

---

## 🔍 Pourquoi cette correspondance est obligatoire ?

### Scénario problématique sans correspondance

Imaginons que Kubernetes autorise ceci :

```yaml
selector:
  matchLabels:
    app: nginx
template:
  metadata:
    labels:
      app: web    # ❌ Différent !
```

**Que se passerait-il ?**

1. Le ReplicaSet crée des Pods avec `app: web`
2. Le ReplicaSet cherche des Pods avec `app: nginx`
3. Le ReplicaSet ne trouve **aucun Pod** qui correspond !
4. Il pense qu'il lui manque 3 Pods
5. Il crée 3 nouveaux Pods avec `app: web`
6. Encore une fois, il ne les trouve pas...
7. **Boucle infinie** de création de Pods ! 💥

**Kubernetes empêche ce problème en validant que les labels correspondent.**

---

## 🎭 Comment le ReplicaSet trouve les Pods

### Étape par étape

**1. Vous créez le ReplicaSet**
```bash
kubectl apply -f replicaset.yaml
```

**2. Le ReplicaSet Controller s'active**
```
ReplicaSet Controller lit le manifest :
- replicas: 3
- selector.matchLabels:
    app: nginx
    tier: frontend
```

**3. Le controller cherche des Pods existants**
```bash
# En interne, Kubernetes fait l'équivalent de :
kubectl get pods -l app=nginx,tier=frontend
```

**4. Comparaison**
```
État désiré : 3 répliques
État actuel : 0 Pods trouvés

→ Différence : -3
→ Action : Créer 3 Pods
```

**5. Création des Pods**
```
Le ReplicaSet crée 3 Pods en utilisant le template :
- Pod 1 : nginx-replicaset-abc12 (labels: app=nginx, tier=frontend)
- Pod 2 : nginx-replicaset-def34 (labels: app=nginx, tier=frontend)
- Pod 3 : nginx-replicaset-ghi56 (labels: app=nginx, tier=frontend)
```

**6. Vérification continue**
```
Le controller surveille en permanence :
kubectl get pods -l app=nginx,tier=frontend --watch

Si un Pod disparaît → Recrée
Si trop de Pods → Supprime
```

---

## 🔧 kubectl edit replicaset - Exemples détaillés

### Commande de base

```bash
kubectl edit replicaset nginx-replicaset
```

**Ce qui se passe :**
1. Kubernetes ouvre le YAML dans votre éditeur (vi, nano, ou $EDITOR)
2. Vous modifiez le YAML
3. Vous sauvegardez et quittez
4. Kubernetes applique les changements

### Exemple 1 : Changer le nombre de replicas

**Avant :**
```bash
kubectl get rs nginx-replicaset
# NAME               DESIRED   CURRENT   READY   AGE
# nginx-replicaset   3         3         3       5m
```

**Éditer :**
```bash
kubectl edit rs nginx-replicaset
```

**Dans l'éditeur, modifier :**
```yaml
spec:
  replicas: 3    # Changer en 5
```

**Sauvegarder et quitter (dans vi : `:wq`)**

**Résultat :**
```
replicaset.apps/nginx-replicaset edited
```

**Vérifier :**
```bash
kubectl get rs nginx-replicaset
# NAME               DESIRED   CURRENT   READY   AGE
# nginx-replicaset   5         5         5       6m

kubectl get pods
# NAME                     READY   STATUS    AGE
# nginx-replicaset-abc12   1/1     Running   6m
# nginx-replicaset-def34   1/1     Running   6m
# nginx-replicaset-ghi56   1/1     Running   6m
# nginx-replicaset-jkl78   1/1     Running   10s   ← Nouveau
# nginx-replicaset-mno90   1/1     Running   10s   ← Nouveau
```

### Exemple 2 : Ajouter des labels au ReplicaSet

**Éditer :**
```bash
kubectl edit rs nginx-replicaset
```

**Ajouter des labels au ReplicaSet (pas aux Pods) :**
```yaml
metadata:
  name: nginx-replicaset
  labels:
    app: nginx
    env: production      # ← Ajouter
    team: backend        # ← Ajouter
```

**Sauvegarder et vérifier :**
```bash
kubectl get rs nginx-replicaset --show-labels
# NAME               DESIRED   CURRENT   READY   AGE   LABELS
# nginx-replicaset   5         5         5       10m   app=nginx,env=production,team=backend
```

### Exemple 3 : Modifier l'image (⚠️ Attention !)

**Éditer :**
```bash
kubectl edit rs nginx-replicaset
```

**Modifier l'image :**
```yaml
spec:
  template:
    spec:
      containers:
      - name: nginx
        image: nginx:1.21    # Changer en nginx:1.22
```

**Sauvegarder**
```
replicaset.apps/nginx-replicaset edited
```

**⚠️ IMPORTANT : Les Pods existants NE SONT PAS mis à jour !**

```bash
kubectl get pods -o yaml | grep "image:"
# image: nginx:1.21    ← Ancienne version !
```

**Pour mettre à jour, vous devez supprimer les Pods :**
```bash
kubectl delete pod nginx-replicaset-abc12
# Le nouveau Pod aura nginx:1.22

kubectl get pods -o yaml | grep "image:" | head -1
# image: nginx:1.22    ← Nouvelle version pour le Pod recréé
```

**C'est pourquoi on utilise des Deployments pour les rolling updates !**

### Exemple 4 : Modifier les resource limits

**Éditer :**
```bash
kubectl edit rs nginx-replicaset
```

**Modifier les ressources :**
```yaml
spec:
  template:
    spec:
      containers:
      - name: nginx
        resources:
          requests:
            memory: "64Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"    # Augmenter de 128Mi à 256Mi
            cpu: "500m"        # Augmenter de 200m à 500m
```

**Sauvegarder**

**⚠️ Les Pods existants ne sont pas affectés !**

Pour appliquer aux Pods existants :
```bash
# Option 1 : Supprimer tous les Pods (ils seront recréés)
kubectl delete pods -l app=nginx

# Option 2 : Rolling restart (si vous avez un Deployment)
kubectl rollout restart deployment nginx
```

---

## 📏 kubectl scale - Exemples détaillés

### Commande de base

```bash
kubectl scale replicaset <nom> --replicas=<nombre>
```

### Exemple 1 : Scale up (augmenter)

**État initial :**
```bash
kubectl get rs nginx-replicaset
# NAME               DESIRED   CURRENT   READY   AGE
# nginx-replicaset   3         3         3       10m

kubectl get pods
# NAME                     READY   STATUS    AGE
# nginx-replicaset-abc12   1/1     Running   10m
# nginx-replicaset-def34   1/1     Running   10m
# nginx-replicaset-ghi56   1/1     Running   10m
```

**Scaler à 5 répliques :**
```bash
kubectl scale rs nginx-replicaset --replicas=5
```

**Résultat immédiat :**
```
replicaset.apps/nginx-replicaset scaled
```

**Vérifier :**
```bash
kubectl get rs nginx-replicaset
# NAME               DESIRED   CURRENT   READY   AGE
# nginx-replicaset   5         5         5       11m

kubectl get pods
# NAME                     READY   STATUS    AGE
# nginx-replicaset-abc12   1/1     Running   10m
# nginx-replicaset-def34   1/1     Running   10m
# nginx-replicaset-ghi56   1/1     Running   10m
# nginx-replicaset-jkl78   1/1     Running   5s    ← Nouveau
# nginx-replicaset-mno90   1/1     Running   5s    ← Nouveau
```

**Observer en temps réel :**
```bash
kubectl get pods --watch
```

### Exemple 2 : Scale down (diminuer)

**État initial (5 répliques) :**
```bash
kubectl get pods
# NAME                     READY   STATUS    AGE
# nginx-replicaset-abc12   1/1     Running   15m
# nginx-replicaset-def34   1/1     Running   15m
# nginx-replicaset-ghi56   1/1     Running   15m
# nginx-replicaset-jkl78   1/1     Running   5m
# nginx-replicaset-mno90   1/1     Running   5m
```

**Scaler à 2 répliques :**
```bash
kubectl scale rs nginx-replicaset --replicas=2
```

**Vérifier :**
```bash
kubectl get pods
# NAME                     READY   STATUS        AGE
# nginx-replicaset-abc12   1/1     Running       16m
# nginx-replicaset-def34   1/1     Running       16m
# nginx-replicaset-ghi56   1/1     Terminating   16m   ← En cours de suppression
# nginx-replicaset-jkl78   1/1     Terminating   6m    ← En cours de suppression
# nginx-replicaset-mno90   1/1     Terminating   6m    ← En cours de suppression

# Après quelques secondes :
kubectl get pods
# NAME                     READY   STATUS    AGE
# nginx-replicaset-abc12   1/1     Running   16m
# nginx-replicaset-def34   1/1     Running   16m
```

**3 Pods ont été supprimés automatiquement.**

### Exemple 3 : Scale à 0 (arrêt temporaire)

**Scaler à 0 pour "éteindre" temporairement :**
```bash
kubectl scale rs nginx-replicaset --replicas=0
```

**Vérifier :**
```bash
kubectl get rs
# NAME               DESIRED   CURRENT   READY   AGE
# nginx-replicaset   0         0         0       20m

kubectl get pods
# No resources found in default namespace.
```

**Tous les Pods sont supprimés, mais le ReplicaSet existe toujours.**

**Rallumer :**
```bash
kubectl scale rs nginx-replicaset --replicas=3
```

### Exemple 4 : Scale conditionnel (avec --current-replicas)

**Scaler seulement si le nombre actuel est 3 :**
```bash
kubectl scale rs nginx-replicaset --current-replicas=3 --replicas=5
```

**Si le ReplicaSet a bien 3 répliques :**
```
replicaset.apps/nginx-replicaset scaled
```

**Si le ReplicaSet a un autre nombre (ex: 2) :**
```
error: Expected replicas to be 3, was 2
```

**Cas d'usage :** Éviter des race conditions dans des scripts.

### Exemple 5 : Scale avec vérification d'état

**Scaler et attendre que tous les Pods soient prêts :**
```bash
kubectl scale rs nginx-replicaset --replicas=10
kubectl wait --for=condition=ready pod -l app=nginx --timeout=60s
```

**Résultat :**
```
pod/nginx-replicaset-abc12 condition met
pod/nginx-replicaset-def34 condition met
...
(tous les 10 Pods)
```

### Exemple 6 : Scale depuis un fichier

**Vous pouvez aussi scaler en modifiant le YAML puis apply :**

**Fichier `replicaset.yaml` :**
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
spec:
  replicas: 7    # Changer de 3 à 7
  # ...
```

**Appliquer :**
```bash
kubectl apply -f replicaset.yaml
```

**Résultat :**
```
replicaset.apps/nginx-replicaset configured
```

---

## 🎯 Comparaison edit vs scale

| Aspect | kubectl edit | kubectl scale |
|--------|-------------|---------------|
| **Commande** | `kubectl edit rs <name>` | `kubectl scale rs <name> --replicas=X` |
| **Ouvre éditeur** | ✅ Oui (vi, nano) | ❌ Non |
| **Change replicas** | ✅ Oui | ✅ Oui |
| **Change image** | ✅ Oui | ❌ Non |
| **Change labels** | ✅ Oui | ❌ Non |
| **Change resources** | ✅ Oui | ❌ Non |
| **Rapidité** | Lent (éditeur) | ⚡ Rapide |
| **Scriptable** | ❌ Difficile | ✅ Facile |
| **Cas d'usage** | Modifications complexes | Scaling rapide |

### Quand utiliser quoi ?

**Utilisez `kubectl scale` pour :**
- ✅ Changer rapidement le nombre de répliques
- ✅ Dans des scripts d'automatisation
- ✅ Réponse rapide à une montée de charge

**Utilisez `kubectl edit` pour :**
- ✅ Modifier plusieurs champs à la fois
- ✅ Ajouter des labels ou annotations
- ✅ Changer la configuration des conteneurs
- ✅ Debug interactif

---

## 🔄 Workflow complet : Création et modification

### Étape 1 : Créer le ReplicaSet

**Fichier `nginx-rs.yaml` :**
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
      tier: frontend
  template:
    metadata:
      labels:
        app: nginx
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
```

**Créer :**
```bash
kubectl apply -f nginx-rs.yaml
```

### Étape 2 : Vérifier

```bash
kubectl get rs
# NAME       DESIRED   CURRENT   READY   AGE
# nginx-rs   3         3         3       10s

kubectl get pods --show-labels
# NAME             READY   STATUS    AGE   LABELS
# nginx-rs-abc12   1/1     Running   10s   app=nginx,tier=frontend
# nginx-rs-def34   1/1     Running   10s   app=nginx,tier=frontend
# nginx-rs-ghi56   1/1     Running   10s   app=nginx,tier=frontend
```

### Étape 3 : Scaler rapidement

```bash
# Augmenter à 5
kubectl scale rs nginx-rs --replicas=5

# Vérifier
kubectl get pods | wc -l
# 6 (5 Pods + header)
```

### Étape 4 : Modifier l'image (avec edit)

```bash
kubectl edit rs nginx-rs
# Changer image: nginx:1.21 → nginx:1.22
# Sauvegarder
```

### Étape 5 : Forcer le redéploiement

```bash
# Supprimer tous les Pods pour qu'ils soient recréés avec la nouvelle image
kubectl delete pods -l app=nginx

# Vérifier que les nouveaux Pods ont la bonne image
kubectl get pods -o jsonpath='{.items[0].spec.containers[0].image}'
# nginx:1.22
```

### Étape 6 : Nettoyer

```bash
kubectl delete rs nginx-rs
```

---

## ⚠️ Erreurs courantes et solutions

### Erreur 1 : Labels ne correspondent pas

**Manifest incorrect :**
```yaml
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: web    # ❌ Différent !
```

**Erreur :**
```
The ReplicaSet "nginx-rs" is invalid:
spec.template.metadata.labels: Invalid value:
{"app":"web"}: `selector` does not match template `labels`
```

**Solution :**
```yaml
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx    # ✅ Correspond
```

### Erreur 2 : Oublier le selector

**Manifest incorrect :**
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
spec:
  replicas: 3
  # ❌ selector manquant !
  template:
    # ...
```

**Erreur :**
```
error: error validating "nginx-rs.yaml": error validating data:
ValidationError(ReplicaSet.spec): missing required field "selector"
```

**Solution :** Toujours inclure le selector.

### Erreur 3 : apiVersion incorrecte

**Manifest incorrect :**
```yaml
apiVersion: v1    # ❌ Incorrect pour ReplicaSet
kind: ReplicaSet
```

**Erreur :**
```
error: unable to recognize "nginx-rs.yaml": no matches for kind "ReplicaSet" in version "v1"
```

**Solution :**
```yaml
apiVersion: apps/v1    # ✅ Correct
kind: ReplicaSet
```

---

## 📚 Résumé

### matchLabels : Les points clés

1. **selector.matchLabels** = Ce que le ReplicaSet cherche
2. **template.metadata.labels** = Ce que les Pods auront
3. **Les labels du template DOIVENT contenir ceux du selector**
4. **Le ReplicaSet trouve les Pods via ces labels**

### kubectl edit : Les points clés

- Ouvre un éditeur pour modifier le YAML
- Pratique pour modifications complexes
- ⚠️ Changer l'image ne met PAS à jour les Pods existants
- Nécessite de supprimer les Pods manuellement pour appliquer

### kubectl scale : Les points clés

- Commande rapide pour changer le nombre de répliques
- Parfait pour l'automatisation (scripts)
- Peut scaler à 0 (arrêt temporaire)
- Peut être conditionnel avec `--current-replicas`

---

← [[01-ReplicaSets-vs-ReplicationControllers|ReplicaSets vs Replication Controllers]]
→ [[02-Deployments|Deployments (recommandé)]]
