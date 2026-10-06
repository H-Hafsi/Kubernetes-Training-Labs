# Lab 1.2 : Prise en main de kubectl

| | |
|---|---|
| **Durée** | 45 min |
| **Niveau** | Guidé |
| **Prérequis** | [Lab 1.1](lab-1-1-installer-k3s.md) réalisé (cluster à 3 nœuds `Ready`) |
| **Acquis visés** | AA1, AA2 |

!!! abstract "Objectifs"
    - Gagner en vitesse avec l'alias et l'autocomplétion
    - Travailler dans un namespace dédié
    - Explorer l'API avec `api-resources` et `explain`
    - Générer, appliquer et inspecter un manifeste

## Étapes

### Étape 1 : Alias et autocomplétion

```bash
echo 'source <(kubectl completion bash)' >> ~/.bashrc
echo 'alias k=kubectl' >> ~/.bashrc
echo 'complete -o default -F __start_kubectl k' >> ~/.bashrc
source ~/.bashrc
k version
```

!!! success "Point de contrôle"
    - [ ] `k get no` + touche Tab complète la commande

### Étape 2 : Travailler dans votre namespace

```bash
k create namespace tp-k8s
k config get-contexts
k config set-context --current --namespace=tp-k8s
k config view --minify | grep namespace
```

### Étape 3 : Explorer l'API

```bash
k api-resources | head -20
k explain pod
k explain pod.spec.containers
k explain deployment.spec.strategy
```

`explain` est votre documentation intégrée : vous pouvez l'utiliser pendant un examen comme en production.

### Étape 4 : Générer un manifeste sans l'écrire à la main

```bash
k run web --image=nginx:alpine --dry-run=client -o yaml > web.yaml
cat web.yaml
```

Repérez `apiVersion`, `kind`, `metadata` et `spec`. Appliquez-le, puis observez :

```bash
k apply -f web.yaml
k get pod web -o wide
k get pod web -o yaml | head -40
```

!!! success "Point de contrôle"
    - [ ] Le Pod `web` est `Running`
    - [ ] Vous savez dire sur quel nœud il s'exécute

### Étape 5 : Inspecter un Pod

```bash
k describe pod web
k logs web
k exec -it web -- sh
# dans le conteneur : hostname, wget -qO- localhost, exit
```

Dans `describe`, lisez la section **Events** en bas : elle raconte ce qui s'est passé (planification, téléchargement de l'image, démarrage).

### Étape 6 : Extraire des informations précises

```bash
k get pod web -o jsonpath='{.status.podIP}'; echo
k get pods -o custom-columns=NOM:.metadata.name,NOEUD:.spec.nodeName,IP:.status.podIP
```

### Étape 7 : Modifier puis recréer

```bash
k label pod web tier=frontend
k get pods --show-labels
k delete -f web.yaml
k get pods
```

## Questions de réflexion

??? question "Quelle différence entre `kubectl create` et `kubectl apply` ?"
    `create` échoue si l'objet existe déjà ; `apply` crée l'objet ou le met à jour pour qu'il corresponde au manifeste (approche déclarative).

??? question "À quoi sert `--dry-run=client -o yaml` ?"
    À générer un manifeste de départ sans rien créer dans le cluster, puis à l'adapter.

??? question "Où trouver la raison d'un Pod qui ne démarre pas ?"
    Dans `kubectl describe pod` (section Events) puis dans `kubectl logs`.

## Nettoyage

```bash
k delete namespace tp-k8s
k config set-context --current --namespace=default
```
