# Évaluation – Partie 2 : Configuration / Sécurité

!!! tip "Instructions"
Sélectionnez **une seule réponse** par question.  
 Le score se met à jour automatiquement.  
 Les bonnes réponses apparaissent en vert, les mauvaises en rouge, avec l’explication.

**Total : 20 questions** (5 faciles · 10 moyennes · 5 difficiles)

---

## Niveau Facile

<quiz>
Quel objet Kubernetes est utilisé pour stocker des données de configuration non sensibles sous forme de paires clé-valeur et les injecter dans les conteneurs ?

- [ ] Secret
- [x] ConfigMap
- [ ] PersistentVolume
- [ ] ServiceAccount

Le `ConfigMap` permet de découpler les données de configuration du code de l'application sous forme de texte brut.
</quiz>

<quiz>
Quel objet Kubernetes est conçu pour stocker des données sensibles (mots de passe, tokens OAuth, clés privées) de manière sécurisée ?

- [ ] ConfigMap
- [x] Secret
- [ ] Ingress
- [ ] RoleBinding

Le `Secret` stocke des informations sensibles, qui sont encodées en Base64 par défaut dans Kubernetes.
</quiz>

<quiz>
Que signifie l'acronyme RBAC dans le contexte de la sécurité Kubernetes ?

- [x] Role-Based Access Control
- [ ] Resource-Based Automated Cluster
- [ ] Reliable Backup And Recovery
- [ ] Remote Binary Artifact Cache

Le RBAC est le mécanisme standard de contrôle d'accès basé sur les rôles pour réglementer l'accès aux ressources du cluster.
</quiz>

<quiz>
Quel type de ressource fournit une identité aux processus s'exécutant à l'intérieur d'un Pod afin qu'ils puissent s'authentifier auprès de l'API Kubernetes ?

- [ ] UserGroup
- [x] ServiceAccount
- [ ] NetworkPolicy
- [ ] SecurityContext

Un `ServiceAccount` est géré par Kubernetes et fournit une identité aux applications qui tournent dans les pods pour dialoguer avec l'API server.
</quiz>

<quiz>
Quel type de ressource réseau permet d'isoler le trafic et d'appliquer des règles de filtrage (pare-feu) entre les pods d'un cluster ?

- [ ] Ingress
- [ ] ClusterIP
- [x] NetworkPolicy
- [ ] ServiceMesh

Une `NetworkPolicy` spécifie comment les groupes de pods sont autorisés à communiquer entre eux et avec d'autres réseaux.
</quiz>

---

## Niveau Moyen

<quiz>
Quelle méthode permet d'injecter l'ensemble des clés d'un ConfigMap en tant que variables d'environnement dans un conteneur ?

- [x] Utiliser le champ `envFrom` avec `configMapRef`.
- [ ] Utiliser le champ `volumeMounts` uniquement.
- [ ] Déclarer le ConfigMap directement dans le fichier Dockerfile de l'image.
- [ ] Utiliser la commande `kubectl inject configmap`.

`envFrom` combiné à `configMapRef` permet d'importer toutes les paires clé-valeur d'un ConfigMap sous forme de variables d'environnement d'un seul coup.
</quiz>

<quiz>
Les données d'un Secret Kubernetes stockées par défaut dans etcd ou SQLite sont-elles chiffrées au repos ?

- [ ] Oui, elles sont chiffrées en AES-256 de manière native et automatique.
- [x] Non, elles sont simplement encodées en Base64, ce qui n'est pas un chiffrement.
- [ ] Oui, mais uniquement si on utilise un conteneur Docker.
- [ ] Non, elles sont stockées en clair absolu sans aucun encodage.

L'encodage en Base64 n'est pas un chiffrement ; les secrets sont lisibles par quiconque a accès à l'API ou au stockage sous-jacent à moins d'activer explicitement le chiffrement au repos (EncryptionConfiguration).
</quiz>

<quiz>
Quelle est la différence structurelle principale entre un `Role` et un `ClusterRole` dans le modèle RBAC de Kubernetes ?

- [x] Un Role s'applique uniquement aux ressources d'un Namespace spécifique, tandis qu'un ClusterRole s'applique à l'ensemble du cluster (ou à des ressources hors namespace).
- [ ] Un Role est destiné aux utilisateurs humains, un ClusterRole uniquement aux robots.
- [ ] Un Role est plus sécurisé qu'un ClusterRole.
- [ ] Il n'y a aucune différence, ce sont des synonymes syntaxiques.

Les `Roles` sont limités aux limites d'un namespace, alors que les `ClusterRoles` ont une portée globale sur tout le cluster (cluster-scoped).
</quiz>

<quiz>
Quel objet Kubernetes permet d'associer (lier) un `Role` ou un `ClusterRole` à un sujet (User, Group, ou ServiceAccount) ?

- [ ] RoleAssociation
- [ ] BindingContext
- [x] RoleBinding (ou ClusterRoleBinding)
- [ ] AccessConnector

Le `RoleBinding` accorde les permissions définies dans un Role à un ensemble de sujets dans un namespace donné.
</quiz>

<quiz>
Quel est le comportement par défaut d'un cluster Kubernetes concernant la communication réseau entre les pods si **aucune** NetworkPolicy n'est définie dans un namespace ?

- [ ] Tout le trafic est bloqué par défaut (Deny-All).
- [x] Tout le trafic est autorisé par défaut (Allow-All).
- [ ] Seul le trafic HTTP est autorisé.
- [ ] Le cluster se met en mode d'erreur critique.

Par défaut, le réseau Kubernetes est "non-isolé" (flat network) : tous les pods peuvent communiquer avec tous les autres pods et services par défaut tant qu'aucune NetworkPolicy restrictive n'est appliquée.
</quiz>

<quiz>
Quelle option de configuration permet de monter le contenu d'un Secret ou d'un ConfigMap sous forme de fichiers dans un répertoire d'un conteneur ?

- [ ] `envFrom`
- [x] `volumes` et `volumeMounts`
- [ ] `secretRef` direct dans la commande CMD
- [ ] `hostPath`

En déclarant un volume basé sur un Secret ou un ConfigMap, puis en l'associant via `volumeMounts`, chaque clé devient un fichier dont le contenu est la valeur.
</quiz>

<quiz>
Quel objet associe un ClusterRole à des sujets à l'échelle globale de tout le cluster ?

- [ ] NamespaceBinding
- [ ] GlobalRoleConnector
- [x] ClusterRoleBinding
- [ ] SecurityPolicyBinding

Le `ClusterRoleBinding` accorde les permissions d'un ClusterRole à l'échelle de l'ensemble du cluster.
</quiz>

<quiz>
Comment peut-on restreindre le trafic entrant dans une NetworkPolicy pour qu'il provienne uniquement des pods situés dans un autre namespace spécifique ?

- [x] En utilisant un `namespaceSelector` dans la règle d'ingress.
- [ ] En modifiant l'adresse IP globale du routeur.
- [ ] Ce n'est pas possible, les NetworkPolicies s'arrêtent aux frontières d'un namespace.
- [ ] En configurant un certificat SSL mutualisé.

Le `namespaceSelector` permet de cibler des espaces de noms entiers pour autoriser ou bloquer les flux entrants.
</quiz>

<quiz>
Quel est le rôle d'un `SecurityContext` au niveau d'un Pod ou d'un Conteneur ?

- [ ] Définir les règles de nommage des conteneurs.
- [x] Configurer les paramètres de sécurité et les privilèges d'exécution (ex: utilisateur UID, capacités Linux, mode root/non-root).
- [ ] Gérer les certificats TLS d'authentification externe.
- [ ] Assurer la sauvegarde automatique des bases de données.

Le `SecurityContext` définit les privilèges et les contrôles d'accès pour un pod ou un conteneur (comme `runAsNonRoot`).
</quiz>

<quiz>
Quelle commande `kubectl` permet de créer rapidement un Secret générique à partir de valeurs littérales (clé=valeur) ?

- [x] `kubectl create secret generic mon-secret --from-literal=ma-cle=ma-valeur`
- [ ] `kubectl make secret generic mon-secret --value=...`
- [ ] `kubectl generate secret --literal=...`
- [ ] `kubectl apply secret-literal ...`

La syntaxe avec `--from-literal` est la méthode imperative courante pour créer des secrets directement depuis la ligne de commande.
</quiz>

---

## Niveau Difficile

<quiz>
Qu'advient-il des variables d'environnement injectées à partir d'un ConfigMap (via `env` ou `envFrom`) si ce ConfigMap est mis à jour après le démarrage du Pod ?

- [ ] Les variables d'environnement du conteneur sont automatiquement et dynamiquement mises à jour en temps réel.
- [x] Les variables d'environnement restent inchangées ; le conteneur doit être redémarré pour prendre en compte les nouvelles valeurs.
- [ ] Le pod est automatiquement supprimé et recréé par le plan de contrôle.
- [ ] Le conteneur se plante en raison d'une incohérence de type.

Contrairement aux volumes montés (qui se mettent à jour dynamiquement), les variables d'environnement injectées au lancement ne se mettent pas à jour dynamiquement sans redémarrer le pod.
</quiz>

<quiz>
Où les Secrets Kubernetes montés en tant que volumes (`volumeMounts`) sont-ils généralement stockés par le nœud au niveau du système de fichiers du conteneur ?

- [ ] Sur un disque dur physique permanent chiffré en BitLocker.
- [x] Dans un système de fichiers en mémoire vive volatile de type `tmpfs`.
- [ ] Directement dans le registre Docker distant.
- [ ] Dans le dossier `/root/.ssh` du nœud maître.

Par défaut, Kubernetes monte les secrets sous forme de volumes `tmpfs`, ce qui signifie qu'ils résident en RAM et ne sont jamais écrits de manière persistante sur le stockage physique du nœud.
</quiz>

<quiz>
Dans une NetworkPolicy, comment spécifier une politique de refus par défaut totale pour tout le trafic entrant (deny-all) ?

- [x] En écrivant `policyTypes: [Ingress]` avec un sélecteur vide `podSelector: {}` et une liste `ingress` vide.
- [ ] En supprimant tous les noeuds du cluster.
- [ ] En activant le mode maintenance via `kubectl cordon`.
- [ ] En mettant `ingress: [deny-all]` sans autre précision.

Une politique avec `podSelector: {}` (sélectionnant tous les pods du namespace) et un tableau `ingress` vide interdit tout trafic entrant non explicitement autorisé.
</quiz>

<quiz>
Quel mécanisme sous-jacent les plugins CNI (comme Calico ou Cilium) utilisent-ils généralement pour appliquer concrètement les règles des NetworkPolicies au niveau du noyau Linux ?

- [ ] Des scripts Bash périodiques exécutés par cron.
- [x] La manipulation des tables de filtrage du noyau (`iptables`, `ip6tables` ou `eBPF`).
- [ ] La modification manuelle du code source du noyau Linux par compilation à la volée.
- [ ] Un serveur proxy HTTP externe obligatoire.

Les CNI traduisent les NetworkPolicies déclaratives de Kubernetes en règles de filtrage au niveau bas niveau du noyau (via `iptables`, `nftables` ou des programmes `eBPF`).
</quiz>

<quiz>
Comment empêcher de manière robuste qu'un conteneur ne s'exécute avec les privilèges root dans son propre namespace de conteneur ?

- [ ] En renommant le fichier binaire principal de l'application.
- [x] En configurant `securityContext.runAsNonRoot: true` combiné avec un ID d'utilisateur non nul (`runAsUser`) dans le manifeste du Pod ou du Conteneur.
- [ ] En utilisant obligatoirement un service cloud propriétaire.
- [ ] En désactivant le démon Docker.

`runAsNonRoot: true` force le kubelet à rejeter le démarrage du conteneur si celui-ci s'exécute en tant qu'UID 0 (root), garantissant ainsi un niveau de sécurité accru.
</quiz>
