---
title: Syllabus
template: home.html
hide:
  - navigation
  - toc
---

## Syllabus { #syllabus }

!!! info "Site en construction"
    Les pages sont ajoutées au fil de l'avancement du cours. Les chapitres et labs sans lien sont à venir.

### Fiche d'identification

| Rubrique | Description |
|---|---|
| Intitulé | Orchestration de conteneurs avec Kubernetes |
| Public | Master DevOps et Cloud Computing, semestre 2 |
| Volume horaire | 45 h (15 séances de 3 h : environ 15 h de cours, 30 h de TP) |
| Crédits | 3 ECTS |
| Prérequis | Linux, réseau (TCP/IP, DNS), conteneurs (Docker), YAML, notions de CI/CD |
| Outils de TP | k3s (1 serveur + 2 agents), `kubectl`, Helm ; voir [l'environnement VMware](annexes/environnement-vmware.md) |

### Objectif général

Concevoir, déployer, sécuriser et exploiter des applications cloud-native sur Kubernetes, en autonomie, selon les pratiques DevOps et GitOps.

### Acquis d'apprentissage

À la fin du cours, l'étudiant est capable de :

- **AA1** : expliquer l'architecture de Kubernetes et le modèle déclaratif (comprendre)
- **AA2** : déployer et mettre à jour des applications conteneurisées (appliquer)
- **AA3** : exposer des services et contrôler les flux réseau (appliquer)
- **AA4** : gérer configuration, stockage et sécurité (appliquer)
- **AA5** : diagnostiquer et résoudre des incidents (analyser)
- **AA6** : mettre en place observabilité et autoscaling (analyser)
- **AA7** : automatiser le déploiement par Helm, CI/CD et GitOps (créer)
- **AA8** : évaluer une architecture selon fiabilité, coût et sécurité (évaluer)

### Compétences visées

=== "Savoirs"

    Architecture du control plane et des nœuds, objets Kubernetes, réseau (CNI, Services, Ingress), stockage (CSI), modèle de sécurité (RBAC, Pod Security).

=== "Savoir-faire"

    Maîtrise de `kubectl`, rédaction de manifestes, packaging Helm et Kustomize, administration d'un cluster k3s, pipelines CI/CD et GitOps (Argo CD), supervision (Prometheus, Grafana), dépannage.

=== "Savoir-être"

    Autonomie, rigueur (infrastructure as code, revue de pairs), travail en équipe, veille technologique.

### Organisation du cours

#### Partie 1 : Fondations (9 h) { #partie-1 }

[Présentation de la Partie 1](partie-1-fondations/index.md)

| Chapitre | Contenu | Ressources |
|---|---|---|
| 1. Architecture de Kubernetes (AA1) | Du conteneur à l'orchestration, control plane, modèle déclaratif | [Cours](partie-1-fondations/ch01-architecture/cours.md) · [Lab 1.1 : Installer k3s](partie-1-fondations/ch01-architecture/lab-1-1-installer-k3s.md) · [Lab 1.2 : kubectl](partie-1-fondations/ch01-architecture/lab-1-2-kubectl.md) |
| 2. Pods et Deployments (AA1, AA2) | Pods, ReplicaSets, Deployments, stratégies de mise à jour | À venir |
| 3. Services et réseau (AA3) | Services, DNS interne, Ingress et Gateway API | À venir |
| **Mini-projet 1** | Application web à deux niveaux | [Énoncé](partie-1-fondations/mini-projet-1.md) |

#### Partie 2 : Configuration, données et sécurité (12 h) { #partie-2 }

[Présentation de la Partie 2](partie-2-configuration-donnees-securite/index.md)

| Chapitre | Contenu | Ressources |
|---|---|---|
| 4. Configuration et ressources (AA4) | ConfigMaps, Secrets, requests et limits, ResourceQuotas, LimitRanges | À venir |
| 5. Stockage (AA4) | Volumes, PV, PVC, StorageClass, StatefulSets | À venir |
| 6. Workloads spécialisés (AA2) | Jobs, CronJobs, DaemonSets, patterns multi-conteneurs | À venir |
| 7. Sécurité (AA4) | RBAC, ServiceAccounts, SecurityContext, Pod Security, NetworkPolicies | À venir |
| **Mini-projet 2** | Application trois niveaux sécurisée et persistante | [Énoncé](partie-2-configuration-donnees-securite/mini-projet-2.md) |

#### Partie 3 : Exploitation (9 h) { #partie-3 }

| Chapitre | Contenu | Ressources |
|---|---|---|
| 8. Administration du cluster k3s (AA1, AA5) | Haute disponibilité (etcd embarqué), sauvegarde, mise à jour, maintenance | À venir |
| 9. Observabilité et dépannage (AA5, AA6) | Logs, probes, Prometheus, Grafana, alerting, diagnostic | À venir |
| 10. Autoscaling et scheduling (AA6) | HPA, VPA, affinités, taints et tolerations, PodDisruptionBudget | À venir |
| **Mini-projet 3** | Exploitation d'une application sous charge | À venir |

#### Partie 4 : Industrialisation (15 h) { #partie-4 }

| Chapitre | Contenu | Ressources |
|---|---|---|
| 11. Helm et Kustomize (AA7) | Charts, values, releases, overlays | À venir |
| 12. CI/CD et GitOps (AA7) | Pipeline, registre d'images, Argo CD | À venir |
| 13. Cloud managé et haute disponibilité (AA8) | AKS, EKS, responsabilité partagée, coûts, TLS | À venir |
| 14. Extensibilité (AA8) | CRD, opérateurs, service mesh (aperçu) | À venir |
| 15. Projet fil rouge et synthèse (AA1 à AA8) | Application microservices complète, soutenance | À venir |

### Méthodes pédagogiques

Cours interactifs courts, TP guidés sur cluster k3s, classe inversée, études de cas d'incidents, projet fil rouge en binôme. Chaque partie se termine par un mini-projet, avec une autonomie croissante (guidé, semi-guidé, autonome).

### Évaluation

| Modalité | Pondération |
|---|---|
| Contrôle continu : TP notés et quiz | 30 % |
| Projet fil rouge : déploiement, rapport et soutenance | 40 % |
| Examen pratique chronométré sur cluster | 30 % |

Des grilles critériées sont associées à chaque acquis d'apprentissage.

### Références

- Documentation officielle : [kubernetes.io](https://kubernetes.io/docs/) et [docs.k3s.io](https://docs.k3s.io/)
- M. Lukša, *Kubernetes in Action*
- B. Ibryam et R. Huß, *Kubernetes Patterns*
- J. Rosso et al., *Production Kubernetes*
