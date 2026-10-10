---
title: Syllabus
template: home.html
hide:
  - navigation
  - toc
---

## Syllabus { #syllabus }

!!! info "Site en construction"
    Toutes les pages existent ; celles marquées « Under construction » sont en cours de rédaction et seront complétées au fil de l'avancement du cours.

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
| 1. Architecture de Kubernetes (AA1) | Du conteneur à l'orchestration, control plane, modèle déclaratif | [Cours](partie-1-fondations/ch01-architecture/cours.md) · [Lab 1.1 : Installer k3s et explorer le cluster](partie-1-fondations/ch01-architecture/lab-1-1-installer-k3s.md) · [Lab 1.2 : Prise en main de kubectl](partie-1-fondations/ch01-architecture/lab-1-2-kubectl.md) |
| 2. Pods et Deployments (AA1, AA2) | Pods et cycle de vie, ReplicaSets, Deployments, stratégies de mise à jour, labels et sélecteurs | [Cours](partie-1-fondations/ch02-pods-deployments/cours.md) · [Lab 2.1 : Mon premier Pod](partie-1-fondations/ch02-pods-deployments/lab-2-1-premier-pod.md) · [Lab 2.2 : Deployments et mises à jour](partie-1-fondations/ch02-pods-deployments/lab-2-2-deployments-mises-a-jour.md) |
| 3. Services et réseau (AA3) | Services (ClusterIP, NodePort, LoadBalancer), DNS interne, Ingress et Gateway API | [Cours](partie-1-fondations/ch03-services-reseau/cours.md) · [Lab 3.1 : Exposer une application](partie-1-fondations/ch03-services-reseau/lab-3-1-exposer-application.md) · [Lab 3.2 : Ingress avec Traefik](partie-1-fondations/ch03-services-reseau/lab-3-2-ingress-traefik.md) |
| **Mini-projet 1** | Application web à deux niveaux | [Énoncé](partie-1-fondations/mini-projet-1.md) |

#### Partie 2 : Configuration, données et sécurité (12 h) { #partie-2 }

[Présentation de la Partie 2](partie-2-configuration-donnees-securite/index.md)

| Chapitre | Contenu | Ressources |
|---|---|---|
| 4. Configuration et ressources (AA4) | ConfigMaps, Secrets, variables d'environnement et volumes de configuration, requests et limits, ResourceQuotas, LimitRanges | [Cours](partie-2-configuration-donnees-securite/ch04-configuration-ressources/cours.md) · [Lab 4.1 : Externaliser la configuration](partie-2-configuration-donnees-securite/ch04-configuration-ressources/lab-4-1-externaliser-configuration.md) · [Lab 4.2 : Maîtriser les ressources](partie-2-configuration-donnees-securite/ch04-configuration-ressources/lab-4-2-maitriser-ressources.md) |
| 5. Stockage (AA4) | Volumes, PV, PVC, StorageClass, StatefulSets, service headless | [Cours](partie-2-configuration-donnees-securite/ch05-stockage/cours.md) · [Lab 5.1 : Volumes persistants](partie-2-configuration-donnees-securite/ch05-stockage/lab-5-1-volumes-persistants.md) · [Lab 5.2 : Base de données avec StatefulSet](partie-2-configuration-donnees-securite/ch05-stockage/lab-5-2-base-donnees-statefulset.md) |
| 6. Workloads spécialisés (AA2) | Jobs, CronJobs, DaemonSets, patterns multi-conteneurs (init, sidecar, ambassador) | [Cours](partie-2-configuration-donnees-securite/ch06-workloads-specialises/cours.md) · [Lab 6.1 : Jobs et CronJobs](partie-2-configuration-donnees-securite/ch06-workloads-specialises/lab-6-1-jobs-cronjobs.md) · [Lab 6.2 : Patterns multi-conteneurs](partie-2-configuration-donnees-securite/ch06-workloads-specialises/lab-6-2-patterns-multi-conteneurs.md) |
| 7. Sécurité (AA4) | RBAC, ServiceAccounts, SecurityContext, Pod Security Standards, NetworkPolicies | [Cours](partie-2-configuration-donnees-securite/ch07-securite/cours.md) · [Lab 7.1 : RBAC](partie-2-configuration-donnees-securite/ch07-securite/lab-7-1-rbac.md) · [Lab 7.2 : Durcir un Pod](partie-2-configuration-donnees-securite/ch07-securite/lab-7-2-durcir-un-pod.md) · [Lab 7.3 : NetworkPolicies](partie-2-configuration-donnees-securite/ch07-securite/lab-7-3-networkpolicies.md) |
| **Mini-projet 2** | Votre application trois niveaux, sécurisée et persistante | [Énoncé](partie-2-configuration-donnees-securite/mini-projet-2.md) |

#### Partie 3 : Exploitation (9 h) { #partie-3 }

[Présentation de la Partie 3](partie-3-exploitation/index.md)

| Chapitre | Contenu | Ressources |
|---|---|---|
| 8. Administration du cluster k3s (AA1, AA5) | Haute disponibilité avec etcd embarqué, sauvegarde et restauration, mise à jour de version, maintenance des nœuds | [Cours](partie-3-exploitation/ch08-administration-cluster/cours.md) · [Lab 8.1 : Cluster HA](partie-3-exploitation/ch08-administration-cluster/lab-8-1-cluster-ha.md) · [Lab 8.2 : Sauvegarde, restauration et mise à jour](partie-3-exploitation/ch08-administration-cluster/lab-8-2-sauvegarde-restauration-mise-a-jour.md) |
| 9. Observabilité et dépannage (AA5, AA6) | Logs, événements, probes, métriques, Prometheus, Grafana, alerting, méthodologie de diagnostic | [Cours](partie-3-exploitation/ch09-observabilite-depannage/cours.md) · [Lab 9.1 : Probes](partie-3-exploitation/ch09-observabilite-depannage/lab-9-1-probes.md) · [Lab 9.2 : Monitoring](partie-3-exploitation/ch09-observabilite-depannage/lab-9-2-monitoring.md) · [Lab 9.3 : Dépannage](partie-3-exploitation/ch09-observabilite-depannage/lab-9-3-depannage.md) |
| 10. Autoscaling et scheduling (AA6) | HPA, VPA, affinités, taints et tolerations, PodDisruptionBudget | [Cours](partie-3-exploitation/ch10-autoscaling-scheduling/cours.md) · [Lab 10.1 : HPA](partie-3-exploitation/ch10-autoscaling-scheduling/lab-10-1-hpa.md) · [Lab 10.2 : Placement](partie-3-exploitation/ch10-autoscaling-scheduling/lab-10-2-placement.md) |
| **Mini-projet 3** | Exploitation d'une application sous charge | [Énoncé](partie-3-exploitation/mini-projet-3.md) |

#### Partie 4 : Industrialisation (15 h) { #partie-4 }

[Présentation de la Partie 4](partie-4-industrialisation/index.md)

| Chapitre | Contenu | Ressources |
|---|---|---|
| 11. Helm et Kustomize (AA7) | Charts, values, releases, templates, overlays Kustomize | [Cours](partie-4-industrialisation/ch11-helm-kustomize/cours.md) · [Lab 11.1 : Utiliser Helm](partie-4-industrialisation/ch11-helm-kustomize/lab-11-1-utiliser-helm.md) · [Lab 11.2 : Packager son application](partie-4-industrialisation/ch11-helm-kustomize/lab-11-2-packager-application.md) |
| 12. CI/CD et GitOps (AA7) | Pipeline de build et de livraison, registre d'images, principes GitOps, Argo CD | [Cours](partie-4-industrialisation/ch12-cicd-gitops/cours.md) · [Lab 12.1 : Pipeline CI](partie-4-industrialisation/ch12-cicd-gitops/lab-12-1-pipeline-ci.md) · [Lab 12.2 : GitOps avec Argo CD](partie-4-industrialisation/ch12-cicd-gitops/lab-12-2-gitops-argocd.md) |
| 13. Cloud managé et haute disponibilité (AA8) | Kubernetes managé (AKS, EKS), modèle de responsabilité partagée, coûts, TLS et exposition publique | [Cours](partie-4-industrialisation/ch13-cloud-manage-ha/cours.md) · [Lab 13.1 : Haute disponibilité multi-zones](partie-4-industrialisation/ch13-cloud-manage-ha/lab-13-1-cluster-vms-cloud.md) · [Lab 13.2 : Exposition sécurisée avec TLS](partie-4-industrialisation/ch13-cloud-manage-ha/lab-13-2-exposition-publique.md) |
| 14. Extensibilité (AA8) | CRD, opérateurs, service mesh (aperçu) | [Cours](partie-4-industrialisation/ch14-extensibilite/cours.md) · [Lab 14.1 : CRD et opérateur](partie-4-industrialisation/ch14-extensibilite/lab-14-1-crd-operateur.md) · [Lab 14.2 : Découverte du service mesh](partie-4-industrialisation/ch14-extensibilite/lab-14-2-service-mesh.md) |
| 15. Projet fil rouge et synthèse (AA1 à AA8) | Application microservices complète, soutenance | [Synthèse](partie-4-industrialisation/ch15-projet-fil-rouge/synthese.md) · [Projet fil rouge](partie-4-industrialisation/ch15-projet-fil-rouge/projet-fil-rouge.md) |

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