# Mini-projet 2 : Votre application trois niveaux, sécurisée et persistante

## 1. Fiche du mini-projet

| Rubrique | Description |
|---|---|
| Cours | Orchestration de conteneurs avec Kubernetes (k3s) |
| Position | Fin de la Partie 2 : Configuration, données et sécurité (chapitres 4 à 7) |
| Modalité | En binôme, sur le cluster k3s du cours (3 VMs sous VMware Workstation) |
| Durée | Travail préalable hors séance (phases 0 et 1) + 2 séances de 3 h (phases 2 à 10) + compte rendu sous une semaine |
| Prérequis | Labs 4.1 à 7.3 et Mini-projet 1 réalisés ; notions de Docker |
| Acquis visés | AA2 (déployer), AA4 (configuration, stockage, sécurité), AA5 (diagnostiquer) |

## 2. Contexte

Dans le Mini-projet 1, l'application vous était fournie. Cette fois, **vous apportez votre propre application** composée de trois niveaux : un frontend, une API et une base de données.

Votre mission : la faire passer d'un projet « qui marche sur mon poste » à un service **exploitable en production** :

- sa configuration est **externalisée** et ses secrets protégés ;
- ses données **survivent** à la perte d'un Pod ;
- ses données sont **sauvegardées** automatiquement ;
- ses ressources sont **maîtrisées** ;
- sa surface d'attaque est **réduite** (RBAC, durcissement des Pods, NetworkPolicies).

> **Important :** l'évaluation ne porte pas sur la qualité du code ni sur le design de l'application, mais sur son **exploitation sur Kubernetes**. Une application simple et bien déployée vaut mieux qu'une application riche mal exploitée.

## 3. Choix de l'application

### 3.1 Contraintes minimales

1. Trois niveaux distincts : **frontend**, **API REST**, **base de données**.
2. Au moins une entité avec les opérations de création, lecture, modification et suppression (CRUD).
3. Toute la configuration passe par des **variables d'environnement** (hôte et port de la base, nom, utilisateur, mot de passe, port d'écoute), **aucune valeur en dur** dans le code.
4. L'API expose deux points de contrôle : `/health` (le processus répond) et `/ready` (la connexion à la base fonctionne).
5. Les logs sont écrits sur la **sortie standard**.
6. L'application ne contient aucun secret dans son code ni dans son image.
7. Pas d'application toute faite (type WordPress) : l'objectif est de comprendre le lien entre votre code et Kubernetes.

### 3.2 Idées d'applications

| Application | Fonctionnalités minimales | Stack suggérée |
|---|---|---|
| Gestionnaire de tâches | Créer, terminer, supprimer des tâches | Vue ou React + Node/Express + PostgreSQL |
| Carnet de contacts | Fiches contact, recherche par nom | Angular + Spring Boot + MySQL |
| Raccourcisseur d'URL | Créer un lien court, compter les visites | HTML/JS + FastAPI (Python) + PostgreSQL |
| Gestion de stock | Articles, quantités, alerte de seuil | Vue + Laravel (PHP) + MariaDB |
| Réservation de salles | Salles, créneaux, réservations | React + Flask (Python) + PostgreSQL |
| Mini-blog | Articles, commentaires | HTML/JS + Node/Express + MongoDB |

Les exemples de cet énoncé utilisent **PostgreSQL** ; adaptez les ports, variables et chemins si vous choisissez MySQL, MariaDB ou MongoDB. Vous pouvez aussi proposer votre propre idée (à faire valider par l'enseignant).

## 4. Architecture cible

```
                      Utilisateur
                          |
                  Ingress (Traefik)
                    app.k3s.local
                          |
                  Service frontend-svc
                          |
              Deployment frontend (nginx)
                  /api/ -> api-svc
                          |
                   Service api-svc
                          |
                Deployment api (2 réplicas)
                          |
                    Service db (headless)
                          |
             StatefulSet db  <--- PVC data (1 Gi)
                          ^
                          |
        CronJob db-backup ---> PVC backup

   Namespace : ResourceQuota + LimitRange + RBAC + NetworkPolicies
```

Choix d'architecture à retenir : **seul le frontend est exposé** par l'Ingress. L'API n'est pas accessible de l'extérieur : le frontend relaie les appels `/api/` vers elle, de l'intérieur du cluster. La base de données n'est joignable que par l'API (et par le job de sauvegarde).

## 5. Cahier des charges

| N° | Thème | Exigence |
|---|---|---|
| E1 | Images | Les trois images sont construites, versionnées (pas de `latest`) et accessibles à tous les nœuds. |
| E2 | Namespace | Tout est dans un namespace dédié, avec un **ResourceQuota** et un **LimitRange**. |
| E3 | Configuration | Les paramètres non sensibles sont dans un **ConfigMap**, les identifiants de base dans un **Secret**. |
| E4 | Secrets | **Aucun secret réel n'est versionné dans Git** (fichier modèle fourni à la place). |
| E5 | Base de données | La base est déployée par un **StatefulSet** avec un volume persistant (`volumeClaimTemplates`) et un Service headless. |
| E6 | Persistance | Les données survivent à la suppression du Pod de la base (preuve demandée). |
| E7 | Démarrage | L'API attend que la base soit prête (init container ou probe `/ready`). |
| E8 | Ressources | Chaque conteneur définit `requests` et `limits`. |
| E9 | Sondes | L'API a une readiness probe (`/ready`) et une liveness probe (`/health`). |
| E10 | Exposition | Seul le frontend est exposé par un **Ingress** sur `app.k3s.local`. |
| E11 | Sauvegarde | Un **CronJob** sauvegarde la base sur un volume dédié ; une **restauration** est testée. |
| E12 | RBAC | Les Pods applicatifs utilisent un ServiceAccount dédié sans jeton monté ; un compte de consultation limité au namespace est créé. |
| E13 | Durcissement | Pods non-root, `allowPrivilegeEscalation: false`, capabilities supprimées ; exceptions justifiées par écrit. |
| E14 | Réseau | Des **NetworkPolicies** n'autorisent que les flux nécessaires (matrice de flux au §7, phase 10). |
| E15 | Dépôt | Les manifestes sont rangés par dossier dans un dépôt Git, avec un `README.md` de déploiement. |

## 6. Images : où construire et où publier ?

Les trois nœuds k3s doivent pouvoir récupérer vos images. Choisissez une option :

| Option | Principe | Remarque |
|---|---|---|
| A. Registre public | Construire l'image, la pousser vers Docker Hub ou GHCR (dépôt public), la référencer dans le manifeste | La plus simple ; les VMs doivent avoir accès à Internet |
| B. Import manuel | `docker save` (ou `podman save`) sur votre poste, copie vers chaque VM, puis `sudo k3s ctr images import` sur **chaque nœud** | Sans registre ; pensez à `imagePullPolicy: IfNotPresent` |

Construisez vos images soit sur votre poste Windows (Docker Desktop ou Podman), soit sur l'une des VMs avec Docker ou Podman installé.

## 7. Démarche guidée en phases

Chaque phase donne un but, des indices détaillés, parfois un squelette à compléter (les `<À COMPLÉTER>`), un point de contrôle et une question de réflexion pour le compte rendu.

### Phase 0 : Développer et tester en local (travail préalable)

- **But :** disposer d'une application qui fonctionne avant de la déployer.
- **Indices :**
  - Développez l'API et le frontend, puis testez l'ensemble avec Docker Compose (trois services) ou en lançant la base dans un conteneur.
  - Lisez la configuration depuis les variables d'environnement (`DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD`, `API_PORT`).
  - Implémentez `/health` (toujours `200`) et `/ready` (`200` seulement si la base répond).
  - Créez la table nécessaire au démarrage (migration automatique) ou par un script SQL séparé.
- **Point de contrôle :** vous créez, lisez, modifiez et supprimez une donnée depuis l'interface, en local.
- **Question :** pourquoi un secret ne doit-il jamais figurer dans le code ni dans l'image ?

### Phase 1 : Conteneuriser et publier

- **But :** produire trois images prêtes pour Kubernetes.
- **Indices :**
  - Écrivez un `Dockerfile` par composant. Pour l'API : une image de base légère, installation des dépendances, un **utilisateur non-root** (`USER`), une commande de démarrage claire.
  - Pour le frontend : servez les fichiers statiques avec `nginxinc/nginx-unprivileged` (écoute sur le port **8080**, sans droits root).
  - Un fichier `.dockerignore` évite d'embarquer inutilement `node_modules` ou `.git`.
  - Versionnez : `monimage:1.0.0`. Publiez selon l'option A ou B du §6.
- **Point de contrôle :** `kubectl run test --image=<votre_image> --restart=Never` démarre sans erreur d'image, puis supprimez ce Pod.
- **Question :** quelle différence entre `imagePullPolicy: Always` et `IfNotPresent`, et que risque-t-on avec le tag `latest` ?

### Phase 2 : Namespace, quotas et limites (séance 1)

- **But :** poser un cadre de ressources pour votre application.
- **Indices :**
  - Créez le namespace puis un **ResourceQuota** (`requests.cpu`, `requests.memory`, `limits.cpu`, `limits.memory`, `pods`, `persistentvolumeclaims`) dimensionné selon les valeurs communiquées par l'enseignant.
  - Ajoutez un **LimitRange** qui impose des valeurs par défaut aux conteneurs.
  - Vérifiez avec `kubectl describe quota`.
- **Point de contrôle :** un Pod sans `resources` reçoit automatiquement des valeurs par défaut.
- **Question :** que se passe-t-il quand un quota est dépassé au moment de créer un Pod ?

### Phase 3 : ConfigMap et Secret

- **But :** externaliser toute la configuration.
- **Indices :**
  - ConfigMap `app-config` : `DB_HOST`, `DB_PORT`, `DB_NAME`, `API_PORT`.
  - Secret `db-credentials` : `POSTGRES_USER`, `POSTGRES_PASSWORD`.
  - Générez le Secret sans l'écrire à la main :
    `kubectl create secret generic db-credentials --from-literal=POSTGRES_USER=<...> --from-literal=POSTGRES_PASSWORD=<...> --dry-run=client -o yaml`
  - **Ne committez pas** ce fichier : ajoutez-le à `.gitignore` et versionnez un `secret.example.yaml` avec de fausses valeurs.
- **Point de contrôle :** `git status` ne montre aucun mot de passe réel dans le dépôt.
- **Question :** un Secret est-il chiffré ? Que faut-il pour que son contenu reste réellement confidentiel ?

### Phase 4 : Base de données en StatefulSet

- **But :** une base qui conserve ses données.
- **Indices :**
  - Créez d'abord un Service **headless** (`clusterIP: None`) nommé `db`, puis le StatefulSet.
  - Pour PostgreSQL, définissez `PGDATA` dans un **sous-dossier** du point de montage, sinon le démarrage peut échouer sur un volume non vide.
  - Ajoutez une readiness probe avec `pg_isready`.
  - Avec MySQL, MariaDB ou MongoDB, adaptez l'image, le port (3306 ou 27017), les variables d'environnement et le chemin de données.

Squelette à compléter :

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: db
spec:
  serviceName: db                      # Service headless
  replicas: 1
  selector:
    matchLabels: { app: <À COMPLÉTER>, tier: db }
  template:
    metadata:
      labels: { app: <À COMPLÉTER>, tier: db }
    spec:
      containers:
        - name: postgres
          image: postgres:16-alpine
          ports:
            - containerPort: 5432
          env:
            - name: POSTGRES_USER
              valueFrom:
                secretKeyRef: { name: <À COMPLÉTER>, key: <À COMPLÉTER> }
            - name: POSTGRES_PASSWORD
              valueFrom:
                secretKeyRef: { name: <À COMPLÉTER>, key: <À COMPLÉTER> }
            - name: POSTGRES_DB
              valueFrom:
                configMapKeyRef: { name: <À COMPLÉTER>, key: <À COMPLÉTER> }
            - name: PGDATA
              value: /var/lib/postgresql/data/pgdata
          volumeMounts:
            - name: data
              mountPath: /var/lib/postgresql/data
          readinessProbe:
            exec:
              command: ["pg_isready", "-U", "<À COMPLÉTER>"]
          resources: <À COMPLÉTER>
  volumeClaimTemplates:
    - metadata:
        name: data
      spec:
        accessModes: ["ReadWriteOnce"]
        storageClassName: local-path
        resources:
          requests:
            storage: 1Gi
```

- **Point de contrôle :** `kubectl get pvc` montre un PVC `Bound` ; vous pouvez vous connecter à la base avec `kubectl exec`.
- **Question :** pourquoi un StatefulSet plutôt qu'un Deployment pour une base de données ?

### Phase 5 : API et frontend (fin de la séance 1)

- **But :** déployer les deux applications avec la configuration externalisée.
- **Indices pour l'API :**
  - Deployment de 2 réplicas ; injectez les variables avec `envFrom` (ConfigMap) et `secretKeyRef` (Secret).
  - Ajoutez un **init container** qui attend la base (par exemple une boucle sur `pg_isready` ou `nc -z db 5432`).
  - Définissez `readinessProbe` sur `/ready` et `livenessProbe` sur `/health`.
  - Définissez `requests` et `limits`.
- **Indices pour le frontend :**
  - Le frontend relaie les appels `/api/` vers l'API. Placez cette configuration nginx dans un **ConfigMap monté en fichier** (avec `subPath` sur `/etc/nginx/conf.d/default.conf`) :

```nginx
server {
  listen 8080;
  location / {
    root /usr/share/nginx/html;
    try_files $uri /index.html;
  }
  location /api/ {
    proxy_pass http://api-svc:<PORT_API>/;
  }
}
```

  - Après modification de ce ConfigMap, relancez le frontend avec `kubectl rollout restart` (un montage `subPath` n'est pas rafraîchi automatiquement).
- **Point de contrôle :** vos Pods sont `Running` et `Ready` ; `kubectl logs` de l'API montre une connexion réussie à la base.
- **Question :** que fait Kubernetes de l'API tant que la readiness probe échoue ?

### Phase 6 : Ingress

- **But :** accéder à l'application depuis Windows.
- **Indices :** un Ingress sur `app.k3s.local` vers `frontend-svc` ; ajoutez l'entrée dans le fichier `hosts` de Windows comme au Mini-projet 1 (voir [Environnement VMware](../annexes/environnement-vmware.md)).
- **Point de contrôle :** vous utilisez l'application depuis votre navigateur et les données saisies sont en base.
- **Question :** pourquoi ne pas exposer aussi l'API et la base par l'Ingress ?

### Phase 7 : Persistance et résilience (début de la séance 2)

- **But :** démontrer que les données survivent aux pannes.
- **Indices :** saisissez des données, supprimez le Pod `db-0`, attendez son retour, vérifiez que les données sont toujours là. Supprimez ensuite un Pod de l'API en suivant le trafic.
- **Point de contrôle :** zéro donnée perdue, application de nouveau disponible sans intervention.
- **Question :** où se trouvent physiquement les données avec `local-path`, et que se passe-t-il si le nœud qui les héberge tombe ?

### Phase 8 : Sauvegarde planifiée et restauration

- **But :** automatiser la sauvegarde puis prouver qu'on sait restaurer.
- **Indices :**
  - Créez un second PVC `backup-pvc` et un CronJob qui exécute `pg_dump` vers ce volume.
  - Pour la démonstration, planifiez-le toutes les 5 minutes.
  - Déclenchez un test immédiat : `kubectl create job --from=cronjob/db-backup test-backup`.
  - Testez la restauration : supprimez une donnée, puis rechargez le dump avec `psql`.

Squelette à compléter :

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: db-backup
spec:
  schedule: "*/5 * * * *"
  concurrencyPolicy: Forbid
  successfulJobsHistoryLimit: 3
  jobTemplate:
    spec:
      template:
        metadata:
          labels: { app: <À COMPLÉTER>, tier: backup }
        spec:
          restartPolicy: OnFailure
          containers:
            - name: backup
              image: postgres:16-alpine
              command: ["/bin/sh", "-c"]
              args:
                - pg_dump -h db -U "$POSTGRES_USER" "$POSTGRES_DB" > /backup/dump-$(date +%Y%m%d-%H%M).sql
              env:
                # POSTGRES_USER, POSTGRES_DB, PGDATABASE ... et PGPASSWORD
                # (variable lue par pg_dump) : à relier au Secret / ConfigMap
                - <À COMPLÉTER>
              volumeMounts:
                - { name: backup, mountPath: /backup }
          volumes:
            - name: backup
              persistentVolumeClaim:
                claimName: backup-pvc
```

- **Point de contrôle :** un fichier `.sql` apparaît dans le volume de sauvegarde et la restauration est validée.
- **Question :** une sauvegarde stockée sur le même nœud que la base protège-t-elle d'une panne de ce nœud ? Que proposeriez-vous ?

### Phase 9 : RBAC

- **But :** appliquer le moindre privilège aux identités du namespace.
- **Indices :**
  - Créez un ServiceAccount par composant (`api-sa`, `frontend-sa`, `db-sa`) avec `automountServiceAccountToken: false` : ces applications n'appellent jamais l'API Kubernetes.
  - Créez un ServiceAccount `dev-viewer`, un **Role** autorisant uniquement `get`, `list`, `watch` sur les Pods, leurs logs et les Services, et un **RoleBinding**.
  - Vérifiez avec `kubectl auth can-i ... --as=system:serviceaccount:<ns>:dev-viewer`.
- **Point de contrôle :** `dev-viewer` peut lister les Pods mais ne peut ni supprimer un Pod ni lire un Secret.
- **Question :** pourquoi désactiver le montage automatique du jeton dans un Pod qui n'en a pas besoin ?

### Phase 10 : Durcissement et NetworkPolicies

- **But :** réduire la surface d'attaque, d'abord dans les Pods puis sur le réseau.

**Durcissement des Pods**

- Pour l'API et le frontend, définissez : `runAsNonRoot: true`, `allowPrivilegeEscalation: false`, `capabilities.drop: ["ALL"]`, et si possible `readOnlyRootFilesystem: true` avec un `emptyDir` monté sur les dossiers d'écriture (`/tmp`, cache nginx).
- La base impose des contraintes (utilisateur propre à l'image, écriture sur son volume) : appliquez ce qui est possible et **justifiez par écrit** chaque exception.
- Activez ensuite le label `pod-security.kubernetes.io/enforce=baseline` sur le namespace, puis essayez `restricted` et notez ce qui échoue.

**NetworkPolicies : matrice de flux à respecter**

| Source | Destination | Autorisé |
|---|---|---|
| Traefik (namespace `kube-system`) | frontend | Oui |
| frontend | api | Oui |
| api | db (port 5432) | Oui |
| job de sauvegarde | db (port 5432) | Oui |
| frontend | db | **Non** |
| tout autre Pod | api, db | **Non** |

Démarche : commencez par interdire tout le trafic entrant du namespace, puis ajoutez une politique par flux autorisé. Squelette à compléter :

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
spec:
  podSelector: {}
  policyTypes: ["Ingress"]
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-api-to-db
spec:
  podSelector:
    matchLabels: { tier: db }
  policyTypes: ["Ingress"]
  ingress:
    - from:
        - podSelector:
            matchLabels: { tier: api }
        - podSelector:
            matchLabels: { tier: backup }
      ports:
        - protocol: TCP
          port: 5432
```

Pour autoriser Traefik, utilisez un `namespaceSelector` sur le label `kubernetes.io/metadata.name: kube-system` dans la politique du frontend.

- **Point de contrôle (à prouver par des captures) :**
  - depuis un Pod de test **sans** label `tier: api`, la connexion à la base échoue (délai dépassé) ;
  - depuis le Pod de l'API, elle réussit ;
  - l'application reste utilisable depuis le navigateur.
- **Question :** pourquoi poser d'abord un « deny all » avant d'ajouter des autorisations ? Quelle est la limite d'une politique qui ne filtre que le trafic entrant ?

## 8. Livrables

1. **Dépôt Git** contenant :
   - `app/` : code et `Dockerfile` des trois composants ;
   - `k8s/` : manifestes rangés par dossier (`00-namespace`, `10-config`, `20-db`, `30-app`, `40-security`) ;
   - `secret.example.yaml` (jamais le vrai secret) ;
   - `README.md` : prérequis, ordre de déploiement, procédure de restauration.
2. **Compte rendu** (4 à 6 pages) :
   - description de l'application et de son architecture (schéma) ;
   - tableau des exigences E1 à E15 avec la preuve associée (sortie de commande ou capture) ;
   - la matrice des flux réseau et les tests de blocage ;
   - les justifications des exceptions de durcissement ;
   - les réponses aux questions de chaque phase ;
   - un paragraphe « difficultés rencontrées et enseignements ».
3. **Démonstration orale** de 10 minutes : application en usage, suppression du Pod de la base (données conservées), sauvegarde et restauration, tentative d'accès bloquée par NetworkPolicy, vérification des droits avec `auth can-i`.

## 9. Grille d'évaluation (/20)

| Critère | Points | AA |
|---|---|---|
| Images versionnées et application fonctionnelle sur le cluster | 2 | AA2 |
| Configuration externalisée (ConfigMap, Secret, rien de secret dans Git) | 3 | AA4 |
| Base en StatefulSet, volume persistant et preuve de persistance | 3 | AA4 |
| Sauvegarde CronJob et restauration testée | 2 | AA2 |
| Ressources : quota, LimitRange, requests et limits, sondes | 2 | AA4, AA5 |
| Sécurité des Pods et RBAC | 3 | AA4 |
| NetworkPolicies conformes à la matrice et testées | 3 | AA4 |
| Qualité du dépôt, du compte rendu et de la démonstration | 2 | AA5 |

## 10. Erreurs fréquentes et conseils de diagnostic

| Symptôme | Pistes à explorer |
|---|---|
| `ImagePullBackOff` | Nom ou tag d'image erroné, image absente sur un nœud (option B), dépôt privé |
| API en `CrashLoopBackOff` | `kubectl logs` : variable d'environnement manquante, base injoignable |
| API jamais `Ready` | Route `/ready` incorrecte, mauvais nom de Service ou de port pour la base |
| PVC bloqué en `Pending` | `StorageClass` absente ou mal orthographiée, quota de PVC atteint |
| Pod base `CrashLoopBackOff` après ajout du volume | `PGDATA` à placer dans un sous-dossier, droits sur le volume |
| Frontend en erreur 502 | `proxy_pass` vers un mauvais nom ou port de Service |
| Tout bloqué après les NetworkPolicies | Flux oublié dans la matrice (Traefik, job de sauvegarde, DNS si vous filtrez la sortie) |
| Pod refusé à la création | Quota dépassé, Pod Security (`baseline` ou `restricted`) non respecté |

## 11. Pour aller plus loin (facultatif)

- Filtrer aussi le trafic **sortant** (egress), en autorisant explicitement le DNS.
- Ajouter une conservation limitée des sauvegardes (suppression des dumps anciens).
- Ajouter un second composant (cache, file de messages) et mettre à jour la matrice de flux.
