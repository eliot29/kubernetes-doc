# 🎓 Documentation Kubernetes - KodeKloud

> Notes complètes du cours Kubernetes avec rappels Docker

## 🗺️ Parcours d'apprentissage

```mermaid
graph LR
    A[🐳 Rappels Docker] --> B[☸️ Kubernetes Core]
    B --> C[⚙️ Runtimes & CRI]
    C --> D[💻 Pratique]
    D --> E[🚀 Projets]

    style A fill:#2496ed,stroke:#1d7dbe,color:#fff
    style B fill:#326ce5,stroke:#2559c7,color:#fff
    style C fill:#00d8ff,stroke:#00b8d9,color:#000
    style D fill:#4caf50,stroke:#388e3c,color:#fff
    style E fill:#ff9800,stroke:#f57c00,color:#fff
```

## 📚 Sections principales

### [[00-Rappels-Docker/00-INDEX|🐳 00 - Rappels Docker]]
**Les fondamentaux de la conteneurisation**

Section de rappel pour comprendre Docker avant d'attaquer Kubernetes :
- Introduction et concepts
- Commandes essentielles
- Images et Dockerfile
- Volumes et réseaux
- Docker Compose

**→ Commencer ici si vous débutez avec les conteneurs**

---

### [[01-Kubernetes-Core/00-INDEX|☸️ 01 - Kubernetes Core]]
**Le cœur de Kubernetes**

Apprentissage complet de Kubernetes :
- Architecture et composants
- Pods, Deployments, Services
- Configuration (ConfigMaps, Secrets)
- Networking et Storage
- Déploiements avancés

**→ Section principale du cours**

---

### [[02-Runtimes-et-CRI/00-INDEX|⚙️ 02 - Runtimes & CRI]]
**Comprendre l'écosystème sous-jacent**

Plongée technique dans les runtimes de conteneurs :
- containerd, CRI-O, runc
- Interface CRI (Container Runtime Interface)
- Architecture complète
- Docker vs containerd

**→ Pour comprendre ce qui se passe "sous le capot"**

---

### [[03-YAML-et-Manifests/00-INDEX|📝 03 - YAML & Manifests]]
**Bibliothèque de manifests réutilisables**

Templates et exemples pour vos déploiements :
- Pods, Deployments, Services
- ConfigMaps, Secrets
- Ingress, PersistentVolumes
- Docker Compose

---

### [[04-Exercices-Pratiques/00-INDEX|💻 04 - Exercices Pratiques]]
**Hands-on labs et exercices**

Mettez en pratique vos connaissances :
- Exercices Docker
- Exercices Kubernetes
- Labs KodeKloud

---

### [[05-Projets-Complets/00-INDEX|🚀 05 - Projets Complets]]
**Applications réelles**

Projets de A à Z :
- Applications microservices
- CI/CD avec Kubernetes
- Monitoring et observabilité

---

### [[06-Annexes/00-INDEX|📖 06 - Annexes]]
**Références et aide**

- Commandes utiles (cheatsheet)
- Troubleshooting
- Glossaire

---

## 🎯 Comment utiliser cette documentation ?

### 🟢 Débutant complet
1. Commencez par [[00-Rappels-Docker/00-INDEX|Rappels Docker]]
2. Pratiquez les exercices Docker
3. Passez à [[01-Kubernetes-Core/00-INDEX|Kubernetes Core]]
4. Explorez [[02-Runtimes-et-CRI/00-INDEX|Runtimes & CRI]] pour approfondir

### 🟡 Vous connaissez Docker
1. Survolez [[00-Rappels-Docker/00-INDEX|Rappels Docker]] si besoin
2. Plongez dans [[01-Kubernetes-Core/00-INDEX|Kubernetes Core]]
3. Utilisez [[03-YAML-et-Manifests/00-INDEX|YAML & Manifests]] comme référence

### 🟠 Vous connaissez Kubernetes
- Utilisez cette doc comme référence
- Explorez [[02-Runtimes-et-CRI/00-INDEX|Runtimes & CRI]] pour la partie avancée
- Consultez les [[05-Projets-Complets/00-INDEX|Projets]] pour des cas d'usage

---

## 🔧 Environnement de travail

### Prérequis
- Docker installé
- kubectl installé
- Un cluster Kubernetes (minikube, kind, ou cloud)

### Ressources
- [KodeKloud - Docker Course](https://notes.kodekloud.com/docs/Docker-Training-Course-for-the-Absolute-Beginner/)
- [KodeKloud - Kubernetes Course](https://notes.kodekloud.com/docs/kubernetes-for-the-absolute-beginners-hands-on-tutorial/)
- [Documentation Kubernetes](https://kubernetes.io/docs/)
- [Documentation Docker](https://docs.docker.com/)

---

## 🚀 Par où commencer ?

**Vous êtes prêt ?** Direction la section qui correspond à votre niveau !

→ [[00-Rappels-Docker/00-INDEX|Commencer par Docker]]
→ [[01-Kubernetes-Core/00-INDEX|Passer directement à Kubernetes]]
→ [[02-Runtimes-et-CRI/00-INDEX|Comprendre les Runtimes]]
