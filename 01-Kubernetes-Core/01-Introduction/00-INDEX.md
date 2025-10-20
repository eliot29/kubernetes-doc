# ☸️ Introduction à Kubernetes

> Découvrir l'orchestration de conteneurs

## Contenu du chapitre

### [[01-Quest-ce-que-Kubernetes|🤔 Qu'est-ce que Kubernetes ?]]
- Définition et origine (Google Borg)
- Pourquoi l'orchestration ?
- K8s vs Docker Swarm vs autres
- Cas d'usage et écosystème CNCF

### [[02-Probleme-sans-orchestration|⚠️ Le problème sans orchestration]]
- Scaling manuel
- Haute disponibilité
- Load balancing
- Rolling updates
- Self-healing

### [[03-Ce-que-Kubernetes-resout|✅ Ce que Kubernetes résout]]
- Déploiement automatisé
- Scaling horizontal
- Service discovery
- Load balancing intégré
- Auto-healing
- Rollbacks automatiques

### [[04-Concepts-Fondamentaux|📚 Concepts fondamentaux]]
- Cluster
- Nodes (Master/Worker)
- Pods
- Services
- Deployments
- Namespaces

### [[05-Ecosysteme-CNCF|🌐 Écosystème CNCF]]
- Cloud Native Computing Foundation
- Projets majeurs (Kubernetes, containerd, Prometheus, etc.)
- Certification (CKA, CKAD, CKS)

## 🎯 Objectifs

À la fin de ce chapitre, vous comprendrez :
- ✅ Pourquoi Kubernetes existe
- ✅ Les problèmes qu'il résout
- ✅ Les concepts de base
- ✅ L'écosystème cloud-native

## 🗺️ De Docker à Kubernetes

```mermaid
graph LR
    A[🐳 Docker<br/>Conteneurisation] --> B[☸️ Kubernetes<br/>Orchestration]
    B --> C[🚀 Production<br/>À grande échelle]

    style A fill:#2496ed,stroke:#1d7dbe,color:#fff
    style B fill:#326ce5,stroke:#2559c7,color:#fff
    style C fill:#4caf50,stroke:#388e3c,color:#fff
```

**Docker** crée les conteneurs
**Kubernetes** les orchestre à grande échelle

---

**Commencer → [[01-Quest-ce-que-Kubernetes|Qu'est-ce que Kubernetes ?]]**
