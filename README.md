# 📚 Documentation Kubernetes - Cours KodeKloud

> Documentation complète et libre pour l'apprentissage de Kubernetes et Docker

[![License: CC BY-SA 4.0](https://img.shields.io/badge/License-CC%20BY--SA%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-sa/4.0/)

## 🎯 À propos

Cette documentation accompagne le cours Kubernetes de [KodeKloud](https://kodekloud.com/). Elle inclut :
- 🐳 Rappels complets sur Docker
- ☸️ Notes détaillées sur Kubernetes
- ⚙️ Explications sur les Container Runtimes (containerd, CRI-O, runc, CRI)
- 📝 Bibliothèque de manifests YAML
- 💻 Exercices pratiques
- 🚀 Projets complets

## 📖 Structure

```
notes-kubernetes/
├── 00-Rappels-Docker/          # 🐳 Fondamentaux Docker (section de rappel)
│   ├── 01-Introduction/
│   ├── 02-Commandes-de-base/
│   ├── 07-Dockerfile/
│   └── 08-Docker-Compose/
│
├── 01-Kubernetes-Core/         # ☸️ Cours principal Kubernetes
│   ├── 01-Introduction/
│   ├── 02-Architecture/
│   ├── 04-Pods/
│   ├── 05-Workloads/
│   └── 06-Services-et-Reseaux/
│
├── 02-Runtimes-et-CRI/         # ⚙️ Comprendre containerd, CRI, runc
│   ├── 01-Comprendre-les-Runtimes/
│   ├── 02-containerd/
│   ├── 03-CRI-O/
│   └── 05-Interface-CRI/
│
├── 03-YAML-et-Manifests/       # 📝 Templates réutilisables
├── 04-Exercices-Pratiques/     # 💻 Labs et exercices
├── 05-Projets-Complets/        # 🚀 Projets de A à Z
└── 06-Annexes/                 # 📖 Cheatsheets et glossaire
```

## 🛠️ Utilisation avec Obsidian

Cette documentation est optimisée pour [Obsidian](https://obsidian.md/).

### Installation
```bash
git clone git@github.com:eliot29/kubernetes-doc.git
cd kubernetes-doc
```

Ouvrez ensuite le dossier dans Obsidian :
1. **Open folder as vault**
2. Installez les plugins recommandés :
   - **Obsidian Git** (sync auto avec GitHub)
   - **Excalidraw** (diagrammes)

### Visualisation
- Graphes Mermaid intégrés
- Liens bidirectionnels entre notes
- Recherche full-text
- Graph view pour visualiser les connexions

## 📚 Parcours d'apprentissage

### Débutant
1. Commencez par `00-Rappels-Docker/`
2. Pratiquez avec `04-Exercices-Pratiques/Docker/`
3. Passez à `01-Kubernetes-Core/`

### Intermédiaire
1. Révisez Docker si nécessaire
2. Suivez `01-Kubernetes-Core/` dans l'ordre
3. Explorez `02-Runtimes-et-CRI/` pour approfondir

### Avancé
- Utilisez comme référence
- Consultez `03-YAML-et-Manifests/` pour des templates
- Implémentez les `05-Projets-Complets/`

## 🔗 Ressources

- [KodeKloud - Docker Course](https://kodekloud.com/courses/docker-for-the-absolute-beginner/)
- [KodeKloud - Kubernetes Course](https://kodekloud.com/courses/kubernetes-for-the-absolute-beginners/)
- [Documentation Kubernetes](https://kubernetes.io/docs/)
- [Documentation Docker](https://docs.docker.com/)

## 🤝 Contribution

Cette documentation est ouverte à tous ! N'hésitez pas à :
- Proposer des améliorations via PR
- Signaler des erreurs via Issues
- Ajouter du contenu manquant

## 📜 Licence

Cette œuvre est mise à disposition selon les termes de la [Licence Creative Commons Attribution - Partage dans les Mêmes Conditions 4.0 International](https://creativecommons.org/licenses/by-sa/4.0/).

---

**Commencez votre apprentissage : ouvrez `00-ACCUEIL.md` dans Obsidian ! 🚀**
