# Environnement de travail : VMware Workstation sur poste Windows

Cette page décrit l'environnement utilisé pour les labs et mini-projets : un poste Windows, VMware Workstation et 3 machines virtuelles Ubuntu Server exécutant k3s.

## 1. Topologie

| Machine | Rôle | Ressources conseillées |
|---|---|---|
| Poste Windows (hôte) | `kubectl`, navigateur, `curl.exe`, Git | 16 Go de RAM recommandés pour l'hôte |
| VM 1 (Ubuntu Server) | Serveur k3s (control plane) | 2 vCPU, 2 Go de RAM |
| VM 2 (Ubuntu Server) | Agent k3s | 2 vCPU, 2 Go de RAM |
| VM 3 (Ubuntu Server) | Agent k3s | 2 vCPU, 2 Go de RAM |

## 2. Réseau

- Placez les 3 VMs sur le même réseau VMware : **NAT (VMnet8)** pour un accès Internet et un accès depuis Windows, ou **bridged** pour utiliser le réseau local.
- Attribuez des **IP statiques** aux VMs (configuration `netplan` sous Ubuntu). Si les IP changent, les agents ne retrouvent plus le serveur et vos entrées `hosts` deviennent fausses.
- Si le pare-feu `ufw` est actif sur les VMs, désactivez-le pour le TP ou ouvrez les ports nécessaires à k3s.

## 3. Accéder aux applications depuis Windows

1. Ouvrez le Bloc-notes **en administrateur** puis le fichier `C:\Windows\System32\drivers\etc\hosts`.
2. Ajoutez une ligne associant l'IP d'un nœud k3s aux deux noms d'hôte :
   `<IP_NOEUD>  web.k3s.local  api.k3s.local`
3. Testez dans PowerShell avec `curl.exe` (et non `curl`, qui est un alias de `Invoke-WebRequest`) :
   `curl.exe http://api.k3s.local`
4. Alternative sans modifier `hosts` : `curl.exe -H "Host: api.k3s.local" http://<IP_NOEUD>`.

Boucle de requêtes pour l'étape 5, dans PowerShell :
`while ($true) { curl.exe -s http://api.k3s.local; Start-Sleep -Milliseconds 500 }`

## 4. Utiliser kubectl depuis Windows (facultatif)

1. Installez `kubectl` sur Windows.
2. Copiez le fichier `/etc/rancher/k3s/k3s.yaml` du serveur vers votre poste (par exemple avec `scp`).
3. Remplacez `127.0.0.1` par l'IP de la VM serveur dans ce fichier.
4. Définissez la variable `KUBECONFIG` vers ce fichier. Sinon, travaillez directement en SSH sur la VM serveur.

## 5. Test de validation avant de commencer

- `kubectl get nodes` affiche **3 nœuds en état `Ready`**.
- `kubectl get pods -A` montre les Pods système (dont Traefik) en `Running`.
- `kubectl run test --image=nginx:alpine` aboutit (les VMs ont accès à Internet), puis supprimez ce Pod.
- Depuis Windows, `curl.exe -I http://<IP_NOEUD>` reçoit une réponse de Traefik (même une erreur 404 prouve que le chemin réseau fonctionne).
