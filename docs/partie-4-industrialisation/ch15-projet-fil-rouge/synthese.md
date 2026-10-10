# Glossaire

Les termes sont classés par ordre alphabétique. La colonne « Chap. » indique le chapitre où la notion est étudiée. Les termes anglais gardés tels quels sont ceux que vous rencontrerez dans la documentation et les outils.

## A

| Terme | Définition | Chap. |
|---|---|---|
| **Affinité de nœud** (*node affinity*) | Règle portée par un Pod qui l'oriente vers certains nœuds selon leurs labels, de façon obligatoire ou préférée | 10 |
| **Annotation** | Métadonnée libre attachée à un objet, non utilisée pour le sélectionner (outils, descriptions, empreintes) | 2, 11 |
| **Anti-affinité de Pod** | Règle qui éloigne un Pod d'autres Pods, pour répartir les réplicas | 10 |
| **API server** | Point d'entrée du control plane : reçoit, valide et stocke les objets, et sert tous les clients (`kubectl`, kubelet, contrôleurs) | 1 |
| **Application** (Argo CD) | Objet personnalisé qui indique à Argo CD où lire l'état voulu (dépôt, chemin) et où l'appliquer (cluster, namespace) | 12 |
| **Argo CD** | Outil GitOps qui synchronise le cluster avec un dépôt Git | 12 |
| **Autoscaling** | Adaptation automatique du nombre de Pods (HPA), de leur taille (VPA) ou du nombre de nœuds (Cluster Autoscaler) | 10 |

## B

| Terme | Définition | Chap. |
|---|---|---|
| **Base** (Kustomize) | Ensemble de manifestes YAML valides, que les overlays personnalisent | 11 |
| **Build** | Étape d'un pipeline qui construit l'image d'un conteneur | 12 |

## C

| Terme | Définition | Chap. |
|---|---|---|
| **Canary** | Déploiement d'une nouvelle version sur une petite part du trafic avant de la généraliser | 14 |
| **cert-manager** | Composant tiers qui obtient et renouvelle automatiquement les certificats TLS | 13, 14 |
| **Chart** | Paquet Helm : templates et valeurs par défaut décrivant une application | 11 |
| **CI/CD** | Intégration continue (construire et tester à chaque modification) et livraison ou déploiement continus | 12 |
| **Cluster** | Ensemble formé d'un control plane et de nœuds de travail, géré comme un tout | 1 |
| **ClusterIP** | Type de Service par défaut : une adresse virtuelle joignable seulement dans le cluster | 3 |
| **ClusterRole**, **ClusterRoleBinding** | Rôle et liaison de droits valables pour tout le cluster, et non pour un seul namespace | 7 |
| **ConfigMap** | Objet qui stocke de la configuration non confidentielle (variables, fichiers) | 4 |
| **Conteneur** | Processus isolé qui embarque son application et ses dépendances, lancé à partir d'une image | 1 |
| **Contrôleur** | Programme qui compare l'état réel à l'état voulu d'une famille d'objets et agit pour les rapprocher | 1, 14 |
| **Control plane** | Ensemble des composants qui pilotent le cluster : API server, etcd, scheduler, contrôleurs | 1 |
| **CoreDNS** | Serveur DNS du cluster : résout les noms des Services | 3 |
| **cordon** | Commande qui marque un nœud comme non planifiable : aucun nouveau Pod n'y est placé | 8 |
| **CRD** (*CustomResourceDefinition*) | Déclaration d'un nouveau type d'objet dans l'API, avec son schéma de validation | 14 |
| **CronJob** | Objet qui crée des Jobs selon un calendrier | 6 |

## D

| Terme | Définition | Chap. |
|---|---|---|
| **DaemonSet** | Objet qui maintient un Pod sur chaque nœud (ou sur certains nœuds) | 6 |
| **Déclaratif** | Mode de fonctionnement où l'on décrit l'état voulu plutôt que les actions à effectuer | 1 |
| **Deployment** | Objet qui gère des réplicas d'un Pod sans état et leurs mises à jour progressives | 2 |
| **Dérive** (*drift*) | Écart entre l'état réel du cluster et l'état voulu décrit dans Git | 12 |
| **Digest** | Identifiant d'une image (`sha256:...`) qui désigne un contenu précis et ne change jamais | 12 |
| **drain** | Commande qui vide un nœud de ses Pods en les évinçant proprement, avant une maintenance | 8 |

## E

| Terme | Définition | Chap. |
|---|---|---|
| **etcd** | Base de données clé-valeur distribuée qui stocke tout l'état du cluster | 1, 8 |
| **Éviction** | Arrêt d'un Pod pour libérer un nœud, par exemple lors d'un `drain` | 8, 10 |
| **Événement** (*event*) | Objet qui trace un fait récent sur une ressource (planification, échec, redémarrage) ; premier outil de diagnostic | 9 |

## F

| Terme | Définition | Chap. |
|---|---|---|
| **Flannel** | Plugin réseau (CNI) fourni par défaut avec k3s | 1 |

## G

| Terme | Définition | Chap. |
|---|---|---|
| **GitOps** | Pratique où Git est la source de vérité de l'état voulu, appliqué et réconcilié automatiquement par un agent | 12 |
| **Grafana** | Outil de visualisation de métriques en tableaux de bord | 9 |

## H

| Terme | Définition | Chap. |
|---|---|---|
| **Haute disponibilité** (HA) | Capacité à rester en service malgré la panne d'un composant | 8, 13 |
| **Helm** | Gestionnaire de paquets de Kubernetes, fondé sur des charts, des values et des releases | 11 |
| **HPA** (*HorizontalPodAutoscaler*) | Objet qui ajuste le nombre de réplicas d'une application selon une métrique, le plus souvent le CPU | 10 |

## I

| Terme | Définition | Chap. |
|---|---|---|
| **Image** | Modèle en lecture seule à partir duquel un conteneur est lancé, publié dans un registre | 1, 12 |
| **Ingress** | Objet qui décrit le routage HTTP(S) de l'extérieur vers les Services, selon l'hôte et le chemin | 3 |
| **Ingress controller** | Composant qui applique les règles des Ingress (ici Traefik) | 3 |
| **Init container** | Conteneur exécuté jusqu'à son terme avant le démarrage des conteneurs principaux d'un Pod | 6 |

## J

| Terme | Définition | Chap. |
|---|---|---|
| **Job** | Objet qui exécute une tâche jusqu'à son achèvement | 6 |

## K

| Terme | Définition | Chap. |
|---|---|---|
| **k3s** | Distribution légère de Kubernetes, utilisée pour les travaux pratiques | 1 |
| **kubeconfig** | Fichier qui indique à `kubectl` et à Helm comment joindre un cluster et avec quelle identité | 1 |
| **kubectl** | Outil en ligne de commande pour interagir avec l'API de Kubernetes | 1 |
| **kubelet** | Agent présent sur chaque nœud, qui lance et surveille les conteneurs des Pods | 1 |
| **Kustomize** | Outil qui personnalise des manifestes YAML par des overlays et des patches, sans template | 11 |

## L

| Terme | Définition | Chap. |
|---|---|---|
| **Label** | Paire clé-valeur attachée à un objet, utilisée pour le sélectionner | 2 |
| **Let's Encrypt** | Autorité de certification gratuite et automatisée | 13 |
| **LimitRange** | Objet qui fixe des valeurs par défaut et des bornes de ressources pour les conteneurs d'un namespace | 4 |
| **LoadBalancer** | Type de Service qui obtient une adresse externe ; dans un cloud, un load balancer du fournisseur est créé et facturé | 3, 13 |
| **local-path** | Classe de stockage de k3s, qui utilise un dossier du disque du nœud | 5 |

## M

| Terme | Définition | Chap. |
|---|---|---|
| **Manifeste** | Fichier YAML qui décrit un ou plusieurs objets Kubernetes | 1 |
| **metrics-server** | Composant qui collecte la consommation de CPU et de mémoire des nœuds et des Pods (`kubectl top`, HPA) | 9, 10 |
| **Middleware** (Traefik) | Objet qui modifie le traitement d'une requête : redirection, limitation de débit | 14 |
| **mTLS** | TLS mutuel : les deux parties s'authentifient par certificat ; fonction typique d'un service mesh | 14 |

## N

| Terme | Définition | Chap. |
|---|---|---|
| **Namespace** | Espace de noms qui isole et regroupe des ressources dans un cluster | 1 |
| **NetworkPolicy** | Objet qui filtre le trafic réseau entre Pods | 7 |
| **Nœud** (*node*) | Machine, physique ou virtuelle, qui exécute des Pods | 1 |
| **NodePort** | Type de Service qui ouvre un port sur tous les nœuds | 3 |
| **nodeSelector** | Contrainte simple qui oriente un Pod vers les nœuds portant un label donné | 10 |

## O

| Terme | Définition | Chap. |
|---|---|---|
| **Opérateur** | Contrôleur qui encode le savoir-faire d'exploitation d'une application, associé à une CRD | 14 |
| **Overlay** (Kustomize) | Variante d'une base, qui la modifie par des patches et des transformations | 11 |

## P

| Terme | Définition | Chap. |
|---|---|---|
| **Patch** | Modification partielle d'un objet ; en Kustomize, fichier qui ne contient que les champs à changer | 11 |
| **PDB** (*PodDisruptionBudget*) | Objet qui limite le nombre de Pods indisponibles lors d'interruptions volontaires | 10 |
| **Pipeline** | Suite d'étapes automatisées déclenchée par un événement (build, test, publication, déploiement) | 12 |
| **Pod** | Plus petite unité déployable : un ou plusieurs conteneurs qui partagent réseau et volumes | 2 |
| **Pod Security Standards** | Niveaux de sécurité (privileged, baseline, restricted) qui encadrent ce qu'un Pod peut demander | 7 |
| **Probe** (sonde) | Test périodique d'un conteneur : *readiness* (prêt à recevoir du trafic), *liveness* (à redémarrer ?), *startup* (démarrage terminé ?) | 9 |
| **Prometheus** | Système de collecte et de stockage de métriques, avec un langage de requêtes (PromQL) et des règles d'alerte | 9 |
| **Prune** (Argo CD) | Suppression du cluster des objets qui ont disparu de Git | 12 |
| **PV** (*PersistentVolume*) | Volume de stockage du cluster | 5 |
| **PVC** (*PersistentVolumeClaim*) | Demande de stockage faite par une application, satisfaite par un PV | 5 |

## Q

| Terme | Définition | Chap. |
|---|---|---|
| **Quorum** | Majorité des membres d'etcd nécessaire pour décider : 2 sur 3, 3 sur 5 | 8 |

## R

| Terme | Définition | Chap. |
|---|---|---|
| **RBAC** | Contrôle d'accès par rôles : qui peut faire quelle action sur quelles ressources | 7 |
| **Réconciliation** | Boucle qui compare l'état réel à l'état voulu et agit pour les rapprocher | 1, 14 |
| **Registre** (*registry*) | Serveur qui stocke et distribue des images (Docker Hub, `ghcr.io`) | 12 |
| **Release** (Helm) | Instance installée d'un chart dans un cluster, avec un nom et un historique de révisions | 11 |
| **ReplicaSet** | Objet qui maintient un nombre donné de Pods identiques ; géré par un Deployment | 2 |
| **requests**, **limits** | Ressources garanties pour le placement (`requests`) et plafond de consommation (`limits`) d'un conteneur | 4 |
| **ResourceQuota** | Objet qui plafonne les ressources totales consommables dans un namespace | 4 |
| **Révision** | Numéro de version d'une release Helm ou d'un Deployment, incrémenté à chaque changement | 2, 11 |
| **Rolling update** | Mise à jour progressive : les Pods sont remplacés par vagues, sans interruption du service | 2 |

## S

| Terme | Définition | Chap. |
|---|---|---|
| **Scheduler** | Composant du control plane qui choisit le nœud de chaque Pod (filtrage, puis score) | 1, 10 |
| **Secret** | Objet qui stocke des données confidentielles ; encodées en base64, non chiffrées par défaut | 4 |
| **SecurityContext** | Paramètres de sécurité d'un Pod ou d'un conteneur : utilisateur, privilèges, système de fichiers | 7 |
| **Selector** (sélecteur) | Expression qui désigne des objets d'après leurs labels | 2 |
| **Service** | Objet qui donne une adresse et un nom stables à un ensemble de Pods et répartit le trafic entre eux | 3 |
| **Service mesh** | Couche d'infrastructure qui gère les échanges entre services (mTLS, nouvelles tentatives, trafic, observabilité) grâce à des proxys | 14 |
| **ServiceAccount** | Identité utilisée par un Pod pour s'adresser à l'API | 7 |
| **ServiceLB** | Composant de k3s qui permet aux Services `LoadBalancer` d'utiliser l'adresse des nœuds | 3 |
| **Sidecar** | Conteneur auxiliaire qui s'exécute dans le même Pod que l'application | 6, 14 |
| **Snapshot** (etcd) | Sauvegarde de l'état du cluster | 8 |
| **StatefulSet** | Objet pour les applications avec état : identité stable et stockage dédié par réplica | 5 |
| **StorageClass** | Définition d'un type de stockage et de son mode de provisionnement | 5 |
| **Sync** (Argo CD) | Application de l'état de Git au cluster ; états `Synced` et `OutOfSync` | 12 |

## T

| Terme | Définition | Chap. |
|---|---|---|
| **Tag** | Étiquette d'une image (`1.26-alpine`, identifiant de commit) ; modifiable, contrairement au digest | 12 |
| **Taint**, **toleration** | Le taint repousse les Pods d'un nœud ; la toleration permet à un Pod de l'accepter | 10 |
| **TLS** | Protocole qui chiffre une communication et authentifie le serveur par un certificat | 13 |
| **topologySpreadConstraints** | Contrainte qui répartit les Pods équitablement entre des domaines (nœuds, zones) | 10, 13 |
| **Traefik** | Ingress controller livré avec k3s | 3 |

## V

| Terme | Définition | Chap. |
|---|---|---|
| **Values** (Helm) | Paramètres d'un chart, avec des valeurs par défaut que l'on surcharge par fichier ou `--set` | 11 |
| **Volume** | Répertoire accessible à un conteneur, dont la durée de vie et la source dépendent de son type | 5 |
| **VPA** | Composant qui recommande ou ajuste automatiquement les `requests` des conteneurs ; à installer | 10 |

## W

| Terme | Définition | Chap. |
|---|---|---|
| **Workflow** (GitHub Actions) | Pipeline décrit dans un fichier YAML du dépôt, exécuté sur un *runner* | 12 |

## Z

| Terme | Définition | Chap. |
|---|---|---|
| **Zone de disponibilité** | Domaine de panne indépendant (datacenter) d'une région de cloud ; les réplicas se répartissent entre zones | 13 |