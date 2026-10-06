# Mini-projet 1 : Déployer une application web à deux niveaux

## 1. Fiche du mini-projet

| Rubrique | Description |
|---|---|
| Cours | Orchestration de conteneurs avec Kubernetes (k3s) |
| Position | Fin de la Partie 1 : Fondations (chapitres 1 à 3) |
| Modalité | En binôme, sur le cluster k3s du cours (un namespace par binôme) |
| Durée | 3 h en séance + compte rendu à rendre sous une semaine |
| Prérequis | Labs 1.1, 1.2, 2.1, 2.2, 3.1 et 3.2 réalisés |
| Environnement | Poste Windows, VMware Workstation, 3 VMs Ubuntu Server avec k3s (voir [Environnement VMware](../annexes/environnement-vmware.md)) |
| Acquis visés | AA1 (expliquer l'architecture), AA2 (déployer et mettre à jour), AA3 (exposer des services) |

## 2. Contexte

Une jeune entreprise souhaite mettre en ligne la première version d'un service composé de deux briques :

- un **frontend** (le site web vu par l'utilisateur) ;
- une **API** (le service qui fournit les données).

Vous êtes l'équipe DevOps. Votre mission : déployer ces deux briques sur Kubernetes de façon **déclarative** (tout en fichiers YAML versionnés), les faire communiquer, les rendre accessibles depuis l'extérieur, puis démontrer que vous savez les **mettre à jour sans interruption** et **diagnostiquer une panne**.

## 3. Objectifs pédagogiques

À l'issue du mini-projet, vous serez capables de :

1. traduire une architecture applicative en objets Kubernetes ;
2. choisir le bon type de Service selon l'usage (interne ou externe) ;
3. exposer deux applications derrière un point d'entrée unique avec Ingress ;
4. réaliser un rolling update et un rollback, et en expliquer le mécanisme ;
5. mener une démarche de diagnostic méthodique (observer, formuler une hypothèse, corriger).

## 4. Architecture cible

```
                 Utilisateur
                     |
              Ingress (Traefik)
          web.k3s.local | api.k3s.local
              /                 \
   Service frontend-svc     Service api-svc
        (ClusterIP)           (ClusterIP)
             |                     |
   Deployment frontend     Deployment api
     (2 réplicas)           (3 réplicas)
```

Le frontend doit pouvoir joindre l'API **à l'intérieur du cluster** en utilisant son nom DNS de Service.

## 5. Cahier des charges

| N° | Exigence |
|---|---|
| E1 | Tout est créé dans un namespace dédié à votre binôme. |
| E2 | L'API est déployée par un Deployment de **3 réplicas**, avec une image dont la version est **explicitement indiquée** (pas de tag `latest`). |
| E3 | Le frontend est déployé par un Deployment de **2 réplicas**. |
| E4 | Chaque Deployment porte des labels cohérents (`app`, `tier`) utilisés par les sélecteurs. |
| E5 | L'API et le frontend sont chacun exposés en interne par un Service de type **ClusterIP**. |
| E6 | Depuis un Pod du frontend, l'API est joignable par son **nom DNS** (sans adresse IP). |
| E7 | Un Ingress rend le frontend accessible sur `web.k3s.local` et l'API sur `api.k3s.local`. |
| E8 | Les ressources sont décrites dans des **manifestes YAML** rangés dans un dépôt Git (aucune ressource créée uniquement en ligne de commande). |
| E9 | Une mise à jour de l'API vers une version plus récente se fait **sans coupure** (vérifiée par une boucle de requêtes). |
| E10 | Un incident volontaire est diagnostiqué et corrigé, avec explication écrite. |

## 6. Images suggérées

- **API :** `ghcr.io/stefanprodan/podinfo` (écoute sur le port 9898, renvoie du JSON dont sa version). Choisissez deux versions proches, par exemple `6.5.4` puis une plus récente.
- **Frontend :** `nginx:alpine` (écoute sur le port 80).

Votre enseignant peut vous imposer d'autres images équivalentes.

## 7. Démarche guidée en étapes

Chaque étape propose un but, des indices (pas la solution) et une question de réflexion à traiter dans votre compte rendu.

### Étape 0 : Préparer le terrain (15 min)

- **But :** disposer d'un espace de travail propre.
- **Indices :** créer le dépôt Git avec un dossier `manifests/`, créer votre namespace, le définir comme namespace par défaut de votre contexte.
- **Question :** pourquoi isoler un projet dans un namespace ?

### Étape 1 : Déployer l'API (30 min)

- **But :** obtenir 3 Pods d'API en fonctionnement.
- **Indices :** générer un squelette YAML avec `kubectl create deployment ... --dry-run=client -o yaml`, l'enregistrer dans `manifests/`, puis l'appliquer avec `kubectl apply -f`. Vérifier avec `get pods -o wide`.
- **Question :** sur quels nœuds les Pods sont-ils répartis, et qui a pris cette décision ?

### Étape 2 : Déployer le frontend (20 min)

- **But :** obtenir 2 Pods de frontend.
- **Indices :** même démarche que l'étape 1. Vérifiez que les labels diffèrent entre les deux Deployments.
- **Question :** que se passerait-il si les deux Deployments utilisaient exactement les mêmes labels ?

### Étape 3 : Créer les Services et tester la communication interne (30 min)

- **But :** rendre les deux briques joignables et prouver la communication frontend vers API.
- **Indices :** un Service par Deployment, de type ClusterIP. Exécutez une commande depuis un Pod du frontend (`kubectl exec`) pour appeler l'API par son nom DNS.
- **Questions :** quel est le nom DNS complet d'un Service ? Que renvoie `get endpoints` et pourquoi est-ce utile au diagnostic ?

### Étape 4 : Exposer avec Ingress (30 min)

- **But :** accéder aux deux applications depuis votre navigateur ou avec `curl`.
- **Indices :** un objet Ingress avec deux règles par hôte, géré par Traefik (fourni par k3s). Ajoutez les noms d'hôte dans le fichier `hosts` de votre poste Windows (ou utilisez l'en-tête `Host` avec `curl.exe`) : voir [Environnement VMware](../annexes/environnement-vmware.md).
- **Question :** pourquoi préfère-t-on un Ingress à deux Services de type NodePort ?

### Étape 5 : Mise à jour sans coupure (25 min)

- **But :** passer l'API à une nouvelle version sans interruption.
- **Indices :** lancez dans un terminal une boucle de requêtes vers l'API pendant que vous modifiez l'image dans le manifeste puis réappliquez. Observez `rollout status` et les ReplicaSets.
- **Questions :** combien de ReplicaSets existent après la mise à jour, et pourquoi l'ancien est-il conservé ? Quelle commande permet de revenir en arrière ?

### Étape 6 : Panne et diagnostic (25 min)

- **But :** appliquer une démarche de dépannage.
- **Scénario imposé :** votre enseignant (ou votre binôme voisin) modifie un manifeste pour introduire une panne, par exemple un tag d'image inexistant ou un sélecteur de Service erroné. Votre rôle est de la trouver et de la corriger.
- **Indices :** `get`, `describe`, `events`, `logs`, `get endpoints`. Notez l'ordre de vos investigations.
- **Question :** quels symptômes vous ont mis sur la piste, et quelle est la cause racine ?

## 8. Livrables

1. **Dépôt Git** contenant le dossier `manifests/` (au minimum : deux Deployments, deux Services, un Ingress) et un `README.md` expliquant comment déployer l'ensemble.
2. **Compte rendu** (2 à 4 pages) comprenant :
   - le schéma d'architecture réalisé par vos soins ;
   - les réponses aux questions de chaque étape ;
   - des captures ou sorties de commandes prouvant que chaque exigence est satisfaite ;
   - un court paragraphe « difficultés rencontrées et enseignements ».
3. **Démonstration orale** de 5 minutes (accès aux applications, mise à jour, rollback).

## 9. Grille d'évaluation (/20)

| Critère | Points | AA |
|---|---|---|
| Deployments corrects (réplicas, versions, labels) | 4 | AA2 |
| Services et communication interne par DNS | 3 | AA3 |
| Ingress fonctionnel pour les deux hôtes | 3 | AA3 |
| Mise à jour sans coupure et rollback | 3 | AA2 |
| Diagnostic de la panne (démarche et explication) | 3 | AA1 |
| Qualité du dépôt Git et du compte rendu | 4 | AA1 |

## 10. Pour aller plus loin (facultatif)

- Ajouter des `resources.requests` et `limits` à l'API et expliquer leur effet sur le placement.
- Faire échouer volontairement une mise à jour et utiliser `rollout undo`.
- Ajouter un troisième hôte `docs.k3s.local` servant une page statique.

## 11. Aide-mémoire

| Besoin | Commande |
|---|---|
| Générer un squelette YAML | `kubectl create deployment ... --dry-run=client -o yaml` |
| Appliquer des manifestes | `kubectl apply -f manifests/` |
| Voir Pods et nœuds | `kubectl get pods -o wide` |
| Diagnostiquer | `kubectl describe`, `kubectl logs`, `kubectl get events` |
| Vérifier un Service | `kubectl get endpoints` |
| Suivre une mise à jour | `kubectl rollout status deployment/<nom>` |
| Revenir en arrière | `kubectl rollout undo deployment/<nom>` |
