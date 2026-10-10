# Aide-mémoire kubectl

Cette annexe rassemble les commandes utilisées dans le cours, classées par besoin. Dans tout le cours, `k` est un alias de `kubectl`. Les majuscules (`POD`, `NOM`) sont à remplacer par vos valeurs.

## Préparation du poste

```bash
alias k=kubectl
source <(kubectl completion bash)
complete -o default -F __start_kubectl k
```

Ajoutez ces lignes à `~/.bashrc` pour les conserver. L'autocomplétion (touche `Tab`) fonctionne alors aussi avec `k`.

## Contexte et namespace

| Besoin | Commande |
|---|---|
| Voir les contextes | `k config get-contexts` |
| Changer de contexte | `k config use-context NOM` |
| Fixer le namespace par défaut | `k config set-context --current --namespace=tp-k8s` |
| Revenir au namespace `default` | `k config set-context --current --namespace=default` |
| Lister les namespaces | `k get namespaces` |
| Agir sur un autre namespace | `-n NAMESPACE` (ou `-A` pour tous) |

## Lire l'état du cluster

| Besoin | Commande |
|---|---|
| Lister les nœuds | `k get nodes -o wide` |
| Lister les Pods | `k get pods`, `k get pods -o wide`, `k get pods -A` |
| Plusieurs types à la fois | `k get deploy,svc,pods` |
| Tout ce qui est courant | `k get all` (ne couvre pas ConfigMap, Secret, PVC, Ingress…) |
| Détail d'un objet | `k describe TYPE NOM` (lire la section `Events` en bas) |
| Manifeste complet | `k get TYPE NOM -o yaml` |
| Filtrer par label | `k get pods -l app=web` |
| Afficher les labels | `k get pods --show-labels` |
| Suivre les changements | `k get pods -w` (arrêt : `Ctrl+C`) |
| Événements récents | `k get events --sort-by=.lastTimestamp` |
| Types d'objets disponibles | `k api-resources` |
| Documentation d'un champ | `k explain pod.spec.containers` |
| Types ajoutés (CRD) | `k get crd` |

### Formats de sortie

| Format | Exemple |
|---|---|
| Colonnes supplémentaires | `k get pods -o wide` |
| YAML ou JSON | `k get pod POD -o yaml` |
| Une valeur précise | `k get svc NOM -o jsonpath='{.spec.clusterIP}'` |
| Une liste de valeurs | `k get pods -o jsonpath='{.items[*].metadata.name}'` |
| Colonnes choisies | `k get nodes -o custom-columns=NOM:.metadata.name,CPU:.status.allocatable.cpu` |
| Noms seuls | `k get pods -o name` |

## Créer, modifier, supprimer

| Besoin | Commande |
|---|---|
| Appliquer un fichier | `k apply -f FICHIER.yaml` |
| Appliquer un dossier | `k apply -f DOSSIER/` |
| Voir les différences avant d'appliquer | `k diff -f FICHIER.yaml` |
| Valider sans créer | `k apply -f FICHIER.yaml --dry-run=server` |
| Générer un manifeste de départ | `k create deployment web --image=nginx:1.26-alpine --dry-run=client -o yaml > web.yaml` |
| Créer un namespace | `k create namespace NOM` |
| Créer un Pod jetable | `k run tmp --rm -it --image=busybox:1.36 --restart=Never -- sh` |
| Créer un ConfigMap | `k create configmap NOM --from-literal=CLE=valeur` ou `--from-file=FICHIER` |
| Créer un Secret | `k create secret generic NOM --from-literal=CLE=valeur` |
| Créer un Secret TLS | `k create secret tls NOM --cert=app.crt --key=app.key` |
| Exposer un Deployment | `k expose deployment NOM --port=80` |
| Modifier un champ | `k patch TYPE NOM --type=merge -p '{"spec":{"replicas":3}}'` |
| Éditer dans un éditeur | `k edit TYPE NOM` |
| Ajouter un label ou une annotation | `k label TYPE NOM cle=valeur`, `k annotate TYPE NOM cle=valeur` |
| Retirer un label | `k label TYPE NOM cle-` (le tiret final retire le label) |
| Supprimer par fichier | `k delete -f FICHIER.yaml` |
| Supprimer par nom | `k delete TYPE NOM` |
| Supprimer tout un type | `k delete pods --all` |
| Supprimer un namespace et son contenu | `k delete namespace NOM` |
| Attendre une condition | `k wait --for=condition=Ready pod/POD --timeout=60s` |

!!! warning "Suppression forcée"
    `k delete pod POD --grace-period=0 --force` retire l'objet de l'API sans attendre l'arrêt du conteneur. Dernier recours uniquement : vérifiez d'abord pourquoi le Pod reste en `Terminating`.

## Déploiements et mises à jour

| Besoin | Commande |
|---|---|
| Changer d'image | `k set image deployment/NOM CONTENEUR=image:tag` |
| Suivre un déploiement | `k rollout status deployment/NOM` |
| Historique | `k rollout history deployment/NOM` |
| Revenir en arrière | `k rollout undo deployment/NOM` |
| Revenir à une révision précise | `k rollout undo deployment/NOM --to-revision=N` |
| Redémarrer les Pods | `k rollout restart deployment/NOM` |
| Changer le nombre de réplicas | `k scale deployment NOM --replicas=N` |
| Autoscaling | `k get hpa`, `k describe hpa NOM` |

## Journaux et accès aux conteneurs

| Besoin | Commande |
|---|---|
| Journaux d'un Pod | `k logs POD` |
| Suivre en continu | `k logs -f POD` |
| Conteneur précédent (après un crash) | `k logs POD --previous` |
| Un conteneur précis | `k logs POD -c CONTENEUR` |
| Journaux par label | `k logs -l app=web --tail=20` |
| Journaux d'un Deployment | `k logs deploy/NOM` |
| Lancer une commande | `k exec POD -- COMMANDE` |
| Shell interactif | `k exec -it POD -- sh` |
| Un conteneur précis | `k exec -it POD -c CONTENEUR -- sh` |
| Redirection de port | `k port-forward svc/NOM 8080:80` ou `k port-forward pod/POD 8080:80` |
| Copier un fichier | `k cp POD:/chemin/fichier ./fichier` |

## Ressources et métriques

| Besoin | Commande |
|---|---|
| Consommation des nœuds | `k top nodes` |
| Consommation des Pods | `k top pods`, `k top pods --containers` |
| Quotas d'un namespace | `k describe resourcequota`, `k get limitrange` |
| Ressources allouables d'un nœud | `k describe node NŒUD` (sections `Allocatable` et `Allocated resources`) |

## Réseau

| Besoin | Commande |
|---|---|
| Services et leurs endpoints | `k get svc`, `k get endpoints NOM` |
| Ingress | `k get ingress`, `k describe ingress NOM` |
| NetworkPolicies | `k get networkpolicy`, `k describe networkpolicy NOM` |
| Tester un nom DNS interne | `k run tmp --rm -it --image=busybox:1.36 --restart=Never -- nslookup NOM` |

## Stockage

| Besoin | Commande |
|---|---|
| Volumes et réclamations | `k get pv`, `k get pvc` |
| Classes de stockage | `k get storageclass` |
| Un PVC reste `Pending` | `k describe pvc NOM` |

## Sécurité

| Besoin | Commande |
|---|---|
| Tester un droit | `k auth can-i list pods -n tp-k8s` |
| Tester en tant qu'un ServiceAccount | `k auth can-i list pods --as=system:serviceaccount:NAMESPACE:COMPTE -n NAMESPACE` |
| Lister les droits d'un compte | `k auth can-i --list --as=system:serviceaccount:NAMESPACE:COMPTE -n NAMESPACE` |
| Rôles et liaisons | `k get role,rolebinding`, `k get clusterrole,clusterrolebinding` |
| ServiceAccounts | `k get serviceaccount` |
| Décoder un Secret | `k get secret NOM -o jsonpath='{.data.CLE}' \| base64 -d` |

## Nœuds, placement et maintenance

| Besoin | Commande |
|---|---|
| Interdire de nouveaux Pods sur un nœud | `k cordon NŒUD` |
| Vider un nœud | `k drain NŒUD --ignore-daemonsets --delete-emptydir-data` |
| Réautoriser le nœud | `k uncordon NŒUD` |
| Étiqueter un nœud | `k label node NŒUD cle=valeur` |
| Poser un taint | `k taint nodes NŒUD cle=valeur:NoSchedule` |
| Retirer un taint | `k taint nodes NŒUD cle=valeur:NoSchedule-` |
| Afficher une étiquette en colonne | `k get nodes -L topology.kubernetes.io/zone` |
| Budgets de disruption | `k get pdb` |

## Helm et Kustomize

| Besoin | Commande |
|---|---|
| Ajouter un dépôt | `helm repo add NOM URL`, puis `helm repo update` |
| Chercher un chart | `helm search repo MOT`, `helm search repo CHART --versions` |
| Voir les values d'un chart | `helm show values CHART --version VERSION` |
| Installer | `helm install RELEASE CHART --version VERSION -f values.yaml -n NAMESPACE` |
| Mettre à jour | `helm upgrade RELEASE CHART -f values.yaml -n NAMESPACE` |
| Lister, historique | `helm list -A`, `helm history RELEASE -n NAMESPACE` |
| Revenir en arrière | `helm rollback RELEASE REVISION -n NAMESPACE` |
| Désinstaller | `helm uninstall RELEASE -n NAMESPACE` |
| Vérifier un chart | `helm lint CHART`, `helm template RELEASE CHART -f values.yaml` |
| Empaqueter | `helm package CHART` |
| Valeurs fournies, ou toutes | `helm get values RELEASE`, `helm get values RELEASE --all` |
| Rendre un overlay Kustomize | `k kustomize DOSSIER` |
| Appliquer, supprimer un overlay | `k apply -k DOSSIER`, `k delete -k DOSSIER` |

## Spécifique à k3s

| Besoin | Commande ou emplacement |
|---|---|
| Fichier kubeconfig | `/etc/rancher/k3s/k3s.yaml` (lisible par root) |
| État du service | `sudo systemctl status k3s` (serveur) ou `k3s-agent` (agent) |
| Journaux du service | `sudo journalctl -u k3s -n 50` (ou `-u k3s-agent`) |
| Jeton pour joindre un nœud | `sudo cat /var/lib/rancher/k3s/server/node-token` |
| Snapshot etcd | `sudo k3s etcd-snapshot save --name NOM`, `sudo k3s etcd-snapshot ls` |
| Emplacement des snapshots | `/var/lib/rancher/k3s/server/db/snapshots/` |
| Composants intégrés | `k get pods -n kube-system` (Traefik, CoreDNS, metrics-server…) |
| Désinstaller k3s | `/usr/local/bin/k3s-uninstall.sh` (serveur), `/usr/local/bin/k3s-agent-uninstall.sh` (agent) |

## Depuis le poste Windows

| Besoin | Commande |
|---|---|
| Tester une URL | `curl.exe -i http://app.k3s.local` |
| Ignorer la vérification TLS (TP uniquement) | `curl.exe -k https://app.k3s.local` |
| Faire confiance à une autorité locale | `curl.exe --ssl-no-revoke --cacert ca.crt https://app.k3s.local` |
| Tester un port | `Test-NetConnection IP -Port 6443` (PowerShell) |

!!! note "`curl.exe` et non `curl`"
    Dans PowerShell, `curl` est un alias d'une autre commande. Écrivez toujours `curl.exe`.

## Modèles minimaux de manifestes

| Objet | `apiVersion` | `kind` |
|---|---|---|
| Pod, Service, ConfigMap, Secret, PVC, Namespace, ServiceAccount | `v1` | `Pod`, `Service`, `ConfigMap`, `Secret`, `PersistentVolumeClaim`, `Namespace`, `ServiceAccount` |
| Deployment, StatefulSet, DaemonSet | `apps/v1` | `Deployment`, `StatefulSet`, `DaemonSet` |
| Job, CronJob | `batch/v1` | `Job`, `CronJob` |
| Ingress, NetworkPolicy | `networking.k8s.io/v1` | `Ingress`, `NetworkPolicy` |
| Role, RoleBinding, ClusterRole, ClusterRoleBinding | `rbac.authorization.k8s.io/v1` | `Role`, `RoleBinding`, `ClusterRole`, `ClusterRoleBinding` |
| HorizontalPodAutoscaler | `autoscaling/v2` | `HorizontalPodAutoscaler` |
| PodDisruptionBudget | `policy/v1` | `PodDisruptionBudget` |
| StorageClass | `storage.k8s.io/v1` | `StorageClass` |
| CustomResourceDefinition | `apiextensions.k8s.io/v1` | `CustomResourceDefinition` |

Pour retrouver la bonne version d'un objet : `k api-resources` (colonnes `APIVERSION` et `KIND`), ou `k explain TYPE`.

Squelette d'un Deployment :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: web
          image: nginx:1.26-alpine
          ports:
            - containerPort: 80
          resources:
            requests:
              cpu: 20m
              memory: 16Mi
            limits:
              cpu: 100m
              memory: 64Mi
```

!!! tip "Retenir la relation clé"
    Le `selector.matchLabels` du Deployment doit correspondre aux `labels` du gabarit de Pod (`template.metadata.labels`) ; le `selector` d'un Service doit correspondre aux labels des Pods.

## États d'un Pod et premier réflexe

| État | Signification | Premier réflexe |
|---|---|---|
| `Pending` | Pas encore placé ou volume non lié | `k describe pod POD` : `Events` |
| `ContainerCreating` | Image en cours de téléchargement, volume en cours de montage | Attendre, puis `k describe pod POD` |
| `ImagePullBackOff`, `ErrImagePull` | Image introuvable ou inaccessible | Nom, tag, registre, accès Internet |
| `CrashLoopBackOff` | Le conteneur démarre puis s'arrête en boucle | `k logs POD --previous` |
| `Running` mais `READY 0/1` | Sonde readiness en échec | `k describe pod POD`, chemin et port de la sonde |
| `Terminating` bloqué | Nœud injoignable, finalizer, arrêt lent | `k describe pod POD`, état du nœud |
| `Evicted`, `OOMKilled` | Mémoire insuffisante ou limite dépassée | `k describe pod POD`, `limits` mémoire |
| `Completed` | Fin normale d'un Job | Rien à faire |