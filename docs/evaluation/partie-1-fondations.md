# Évaluation – Partie 1 : Fondations

!!! tip "Instructions"
    Sélectionnez **une seule réponse** par question.  
    Le score se met à jour automatiquement.  
    Les bonnes réponses apparaissent en vert, les mauvaises en rouge, avec l’explication.

**Total : 20 questions** (5 faciles · 10 moyennes · 5 difficiles)

---

## Niveau Facile

<quiz>
Qu'est-ce que k3s ?

- [ ] Une version lourde et entreprise de Kubernetes nécessitant 16 Go de RAM minimum.
- [x] Une distribution Kubernetes légère, certifiée CNCF, optimisée pour l'IoT et la Edge Computing.
- [ ] Un outil de gestion de code source concurrent de Git.
- [ ] Un plugin réseau (CNI) pour Kubernetes.

k3s est une distribution Kubernetes entièrement conforme et certifiée, conçue pour être légère et simple à installer, souvent utilisée en edge computing ou en environnement de test.
</quiz>

<quiz>
Quel est l'objet Kubernetes le plus petit et le plus basique que l'on peut déployer ?

- [ ] Le Deployment
- [ ] Le Service
- [x] Le Pod
- [ ] Le ReplicaSet

Le Pod est l'unité de déploiement de base la plus petite dans le modèle d'objet Kubernetes, contenant un ou plusieurs conteneurs.
</quiz>

<quiz>
Quelle commande `kubectl` permet de lister l'ensemble des pods en cours d'exécution dans le namespace par défaut ?

- [ ] `kubectl show pods`
- [ ] `kubectl list pods`
- [x] `kubectl get pods`
- [ ] `kubectl display pods`

La commande standard pour afficher des ressources dans Kubernetes est `kubectl get <ressource>`.
</quiz>

<quiz>
Quel type de Service Kubernetes expose un port statique sur chaque nœud du cluster, permettant d'accéder au service de l'extérieur via l'adresse IP du nœud ?

- [ ] ClusterIP
- [x] NodePort
- [ ] ExternalName
- [ ] Ingress

Le type `NodePort` réserve un port spécifique sur chaque nœud du cluster pour acheminer le trafic externe vers le service cible.
</quiz>

<quiz>
Quel composant gère généralement la distribution du trafic HTTP/HTTPS externe vers les services internes du cluster en se basant sur des règles de routage par nom de domaine ou chemin ?

- [ ] kube-proxy
- [ ] CoreDNS
- [x] Ingress Controller
- [ ] etcd

L'Ingress (piloté par un Ingress Controller comme Traefik dans k3s) gère le routage du trafic HTTP/HTTPS de l'extérieur vers l'intérieur du cluster.
</quiz>

---

## Niveau Moyen

<quiz>
Quel est le rôle principal d'un Deployment dans Kubernetes ?

- [ ] Assurer le stockage persistant des données des applications.
- [x] Gérer des ReplicaSets et fournir des mises à jour déclaratives (rolling updates, rollbacks) pour les Pods.
- [ ] Assurer l'isolation réseau stricte entre les namespaces.
- [ ] Fournir un équilibrage de charge de niveau 4 TCP/UDP.

Le Deployment abstrait la gestion des ReplicaSets et permet d'administrer facilement les cycles de vie des applications (mises à jour fluides, retours en arrière).
</quiz>

<quiz>
Quelle est la différence fondamentale entre un service de type `ClusterIP` et un service de type `NodePort` ?

- [x] ClusterIP est accessible uniquement depuis l'intérieur du cluster, tandis que NodePort expose le service sur les nœuds vers l'extérieur.
- [ ] ClusterIP nécessite une adresse IP publique, contrairement à NodePort.
- [ ] ClusterIP ne fonctionne qu'avec les pods sans état (stateless).
- [ ] Il n'y a aucune différence technique, c'est un simple changement de nom.

`ClusterIP` est le type par défaut et interne au cluster. `NodePort` étend `ClusterIP` en ouvrant un port sur chaque interface de nœud.
</quiz>

<quiz>
Comment s'appelle le stockage de données par défaut utilisé par k3s pour remplacer etcd dans ses configurations légères standard ?

- [ ] MySQL
- [x] SQLite
- [ ] MongoDB
- [ ] Redis

k3s propose par défaut l'utilisation d'une base de données SQLite intégrée pour stocker l'état du cluster, simplifiant le déploiement.
</quiz>

<quiz>
Quel fichier de configuration `kubectl` utilise-t-il par défaut pour localiser et communiquer avec le cluster Kubernetes ?

- [ ] `/etc/kubernetes/manifests.yaml`
- [x] `~/.kube/config`
- [ ] `/var/lib/k3s/config.json`
- [ ] `/app/config.yaml`

Par convention, `kubectl` cherche son fichier de contexte et d'authentification dans le répertoire utilisateur `~/.kube/config`.
</quiz>

<quiz>
Dans la spécification d'un Deployment, quel paramètre de la stratégie de mise à jour (`RollingUpdate`) permet de limiter le nombre de pods créés en excès par rapport au nombre souhaité (`replicas`) pendant la mise à jour ?

- [ ] `maxUnavailable`
- [x] `maxSurge`
- [ ] `minReadySeconds`
- [ ] `progressDeadlineSeconds`

`maxSurge` définit le nombre maximal de pods qui peuvent être créés au-dessus du nombre souhaité de pods pendant le processus de mise à jour.
</quiz>

<quiz>
Quel objet Kubernetes permet de garantir qu'un (et un seul) Pod s'exécute sur chaque nœud (ou un sous-ensemble de nœuds) du cluster, typiquement utilisé pour les agents de monitoring ou de logs ?

- [ ] Deployment
- [ ] StatefulSet
- [x] DaemonSet
- [ ] Job

Le DaemonSet veille à ce que tous (ou certains) nœuds exécutent une copie d'un Pod spécifique.
</quiz>

<quiz>
Quelle commande permet de consulter les journaux (logs) d'un conteneur spécifique au sein d'un pod nommé `mon-application` ?

- [x] `kubectl logs mon-application`
- [ ] `kubectl get logs mon-application`
- [ ] `kubectl describe pod mon-application`
- [ ] `kubectl debug mon-application`

`kubectl logs` permet de récupérer la sortie standard (stdout/stderr) des conteneurs d'un pod.
</quiz>

<quiz>
Quel est l'effet de la commande `kubectl scale deployment mon-service --replicas=5` ?

- [ ] Il supprime le deployment et en recrée 5 nouveaux.
- [ ] Il redémarre les pods existants 5 fois de suite.
- [x] Il ajuste le nombre désiré de réplicas du deployment à 5.
- [ ] Il limite le nombre de requêtes à 5 par seconde.

La commande `scale` modifie directement le nombre de réplicas cibles d'un objet gérant des réplicas (Deployment, ReplicaSet, etc.).
</quiz>

<quiz>
Qu'est-ce qu'un Service de type Headless (`ClusterIP: None`) ?

- [ ] Un service qui bloque tout trafic réseau entrant.
- [x] Un service qui n'a pas d'adresse IP de cluster attribuée et retourne directement les IP individuelles des pods via le DNS.
- [ ] Un service obsolète qui n'est plus supporté par Kubernetes.
- [ ] Un service qui s'exécute sans aucun pod associé.

Un service Headless n'alloue pas de ClusterIP ; le DNS renvoie les adresses IP des pods correspondants, utile pour les bases de données distribuées nécessitant un accès direct aux pairs.
</quiz>

<quiz>
Quel contrôleur Ingress est configuré par défaut lors d'une installation standard de k3s ?

- [ ] NGINX Ingress Controller
- [x] Traefik
- [ ] HAProxy Ingress
- [ ] Envoy Gateway

k3s intègre Traefik par défaut comme contrôleur Ingress prêt à l'emploi.
</quiz>

---

## Niveau Difficile

<quiz>
Comment Kubernetes gère-t-il la résolution des noms de services (Service Discovery) au sein du cluster ?

- [x] Via un serveur DNS interne au cluster (généralement CoreDNS) qui associe le nom du service à son IP de cluster ou ses endpoints.
- [ ] En modifiant dynamiquement le fichier `/etc/hosts` de chaque machine physique de l'infrastructure.
- [ ] Par l'intermédiaire du navigateur web de l'utilisateur.
- [ ] Grâce à un serveur DHCP centralisé externe.

CoreDNS résout les noms DNS internes (ex: `mon-service.mon-namespace.svc.cluster.local`) en adresses IP de cluster pour faciliter la communication inter-services.
</quiz>

<quiz>
Quelle est la différence fondamentale entre une sonde de vivacité (`livenessProbe`) et une sonde de préparation (`readinessProbe`) ?

- [ ] `livenessProbe` vérifie si le pod peut recevoir du trafic, tandis que `readinessProbe` vérifie si le pod est authentifié.
- [x] `livenessProbe` redémarre le conteneur en cas d'échec, tandis que `readinessProbe` retire temporairement le pod des endpoints du service sans le redémarrer.
- [ ] `livenessProbe` s'exécute uniquement au démarrage, tandis que `readinessProbe` s'exécute en continu.
- [ ] Il n'y a aucune différence fonctionnelle, ce sont des synonymes.

Si la `livenessProbe` échoue, le conteneur est tué et redémarré. Si la `readinessProbe` échoue, le trafic réseau est coupé vers ce pod jusqu'à ce qu'il redevienne prêt.
</quiz>

<quiz>
Lors d'une mise à jour de type rolling update, que contrôlent précisément les paramètres `maxUnavailable` et `maxSurge` ?

- [ ] La quantité maximale de CPU et de mémoire consommée par le déploiement.
- [x] Le nombre maximal de pods qui peuvent être indisponibles simultanément et le nombre maximal de pods en excès pendant la mise à jour.
- [ ] Le nombre maximal de requêtes HTTP acceptées par seconde et la latence maximale tolérée.
- [ ] Le nombre maximal de tentatives de redémarrage en cas de plantage du conteneur.

`maxUnavailable` définit combien de pods peuvent manquer pendant la transition, et `maxSurge` définit combien de pods supplémentaires peuvent être créés au-delà du nombre initial.
</quiz>

<quiz>
Quel est l'impact d'un label selector configuré dans un objet Service Kubernetes ?

- [ ] Il détermine quels nœuds physiques du cluster vont exécuter les pods du service.
- [x] Il sélectionne dynamiquement les pods ciblés par le service en fonction de leurs étiquettes (labels) correspondantes.
- [ ] Il filtre les utilisateurs autorisés à se connecter au service via le RBAC.
- [ ] Il choisit la version de l'image Docker à télécharger.

Le selector fait correspondre les clés/valeurs des labels des pods avec ceux du service pour acheminer le trafic vers les bons pods cibles.
</quiz>

<quiz>
Comment k3s parvient-il à regrouper les rôles de plan de contrôle (control plane) et de worker dans un seul et même binaire léger ?

- [ ] En supprimant totalement l'API Server de Kubernetes.
- [x] En combinant les composants Kubernetes natifs (API server, controller manager, scheduler, kubelet) au sein d'un processus unique modulaire écrit en Go, couplé à un stockage simplifié.
- [ ] En utilisant une machine virtuelle Java (JVM) pour exécuter l'ensemble du code source.
- [ ] En s'appuyant obligatoirement sur Docker Swarm en arrière-plan.

k3s repackage les différents démons de Kubernetes (généralement distincts) dans un processus unique et épuré, tout en retirant les composants obsolètes ou in-tree cloud providers pour réduire l'empreinte mémoire.
</quiz>