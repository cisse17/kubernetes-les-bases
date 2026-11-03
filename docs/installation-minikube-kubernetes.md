# Installer Minikube - Préparer son environnement Kubernetes local

> Support Bassirou Tech du tutoriel vidéo **Kubernetes - Les bases**  
> Objectif : installer un cluster Kubernetes local pour apprendre et tester sans louer de serveur cloud.

Minikube permet de lancer un petit cluster Kubernetes sur son propre ordinateur. C'est idéal pour découvrir les Pods, Deployments, Services, ConfigMaps, Secrets et les commandes `kubectl`.

## 1. Prérequis

Avant de commencer, vérifiez que vous disposez de :

- Un ordinateur sous **Windows, macOS ou Linux**.
- Une connexion Internet pour télécharger les outils et les images de conteneurs.
- La virtualisation activée si le pilote choisi en a besoin.
- Un pilote compatible. Pour commencer, **Docker** est un choix pratique si Docker est déjà installé.

> **À retenir :** Minikube est local. Les ressources utilisées sont celles de votre ordinateur. Ce tutoriel ne crée pas de ressources AWS payantes.

## 2. Installer un pilote de conteneurs

Minikube a besoin d'un environnement pour exécuter les nœuds du cluster. Le pilote Docker est un choix courant.

Installez Docker Desktop sur Windows/macOS, ou Docker Engine sur Linux, si vous souhaitez utiliser ce pilote. Démarrez Docker puis vérifiez son fonctionnement :

```bash
docker --version
docker info
```

Si vous ne souhaitez pas utiliser Docker, Minikube prend également en charge d'autres pilotes selon votre système. Consultez la documentation officielle : <https://minikube.sigs.k8s.io/docs/drivers/>.

## 3. Installer Minikube

Choisissez **une seule** méthode correspondant à votre système d'exploitation.

### macOS — avec Homebrew

Si Homebrew est installé :

```bash
brew install minikube
```

### Linux — téléchargement du binaire officiel

Cette commande télécharge la version stable la plus récente pour un PC Linux **x86-64 / amd64** :

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
rm minikube-linux-amd64
```

Sur une machine ARM64, utilisez le binaire correspondant à l'architecture de votre système. Consultez la page officielle d'installation pour les autres options Linux.

### Windows — avec WinGet

Ouvrez PowerShell :

```powershell
winget install Kubernetes.minikube
```

### Ou Sur Windows (PowerShell admin)
```powershell
winget install minikube
```

Selon votre configuration, il peut être nécessaire de rouvrir le terminal après l'installation.


### Vérifier l'installation

Dans un nouveau terminal :

```bash
minikube version
```

Si la commande n'est pas reconnue, vérifiez que le dossier d'installation est présent dans votre `PATH`, puis rouvrez le terminal.

## 4. Installer kubectl

`kubectl` est l'outil en ligne de commande qui permet de communiquer avec Kubernetes.

Minikube peut télécharger et exécuter une version de `kubectl` via `minikube kubectl -- ...`. Pour apprendre, vous pouvez aussi installer `kubectl` séparément en suivant la documentation officielle :

<https://kubernetes.io/docs/tasks/tools/>

Vérifiez ensuite :

```bash
kubectl version --client
```

Si `kubectl` n'est pas installé mais que Minikube fonctionne, utilisez par exemple :

```bash
minikube kubectl -- get pods -A
```

## 5. Démarrer le cluster Kubernetes

### Démarrage simple

Si vous avez un pilote par défaut correctement configuré :

```bash
minikube start
```

### Démarrage avec Docker comme pilote

Si Docker est installé et démarré :

```bash
minikube start --driver=docker
```

Le premier démarrage peut prendre quelques minutes : Minikube télécharge les composants nécessaires.

## 6. Vérifier que tout fonctionne

Vérifiez l'état du cluster :

```bash
minikube status
kubectl cluster-info
kubectl get nodes
kubectl get pods -A
```

Le nœud doit apparaître avec le statut `Ready`. Certains Pods système peuvent prendre un peu de temps à démarrer ; relancez `kubectl get pods -A` après quelques instants si nécessaire.

Si vous n'avez pas installé `kubectl` séparément, vous pouvez utiliser :

```bash
minikube kubectl -- get nodes
minikube kubectl -- get pods -A
```

## 7. Faire un premier déploiement de test

Déployons une application NGINX pour vérifier que Kubernetes peut lancer un workload :

```bash
kubectl create deployment demo-nginx --image=nginx
kubectl get deployments
kubectl get pods
```

Attendez que le Pod soit prêt :

```bash
kubectl rollout status deployment/demo-nginx
```

Exposez ensuite le déploiement dans le cluster :

```bash
kubectl expose deployment demo-nginx --type=NodePort --port=80
kubectl get services
```

Ouvrez l'application dans votre navigateur :

```bash
minikube service demo-nginx
```

Cette commande récupère l'adresse appropriée pour votre environnement et peut ouvrir le navigateur. Si elle affiche une URL sans ouvrir de fenêtre, copiez cette URL dans votre navigateur.

Pour supprimer le déploiement de test après la démonstration :

```bash
kubectl delete service demo-nginx
kubectl delete deployment demo-nginx
```

## 8. Commandes utiles pendant l'apprentissage

| Commande | À quoi sert-elle ? |
|---|---|
| `minikube status` | Vérifier l'état de Minikube |
| `minikube dashboard` | Ouvrir le tableau de bord Kubernetes |
| `kubectl get nodes` | Lister les nœuds du cluster |
| `kubectl get pods` | Lister les Pods du namespace courant |
| `kubectl get pods -A` | Lister les Pods de tous les namespaces |
| `kubectl get deployments` | Lister les Deployments |
| `kubectl get services` | Lister les Services |
| `kubectl describe pod NOM_DU_POD` | Voir les détails et événements d'un Pod |
| `kubectl logs NOM_DU_POD` | Afficher les logs d'un Pod |
| `kubectl delete -f fichier.yaml` | Supprimer les ressources définies dans un fichier YAML |

Pour ouvrir le tableau de bord :

```bash
minikube dashboard
```

## 9. Arrêter ou supprimer le cluster

Arrêter Minikube sans supprimer le cluster :

```bash
minikube stop
```

Redémarrer plus tard :

```bash
minikube start
```

Supprimer le cluster local et ses données associées :

```bash
minikube delete
```

> Attention : `minikube delete` supprime le cluster Minikube. Ne l'utilisez pas si vous souhaitez conserver les ressources de vos exercices.

## 10. Problèmes fréquents

### `minikube: command not found` ou commande non reconnue

- Vérifiez que l'installation s'est terminée correctement.
- Rouvrez le terminal.
- Vérifiez le `PATH` de votre système.

### Docker ne répond pas

- Démarrez Docker Desktop ou le service Docker.
- Testez `docker info`.
- Puis relancez `minikube start --driver=docker`.

### Le cluster ne démarre pas

Consultez les informations de diagnostic :

```bash
minikube logs
minikube status
```

Vérifiez également que votre ordinateur dispose de suffisamment de mémoire et d'espace disque, et que le pilote choisi est compatible.

### `kubectl` ne trouve pas le cluster

Vérifiez que Minikube est démarré :

```bash
minikube status
kubectl config current-context
kubectl get nodes
```

Si besoin, utilisez la commande intégrée :

```bash
minikube kubectl -- get nodes
```

## 11. Et après ?

Une fois l'installation validée, suivez cet ordre pour votre premier parcours Kubernetes :

1. **Pod** — la plus petite unité déployable.
2. **Deployment** — gérer les réplicas et les mises à jour des Pods.
3. **Service** — donner un point d'accès réseau stable aux Pods.
4. **ConfigMap et Secret** — séparer la configuration de l'application.
5. **Namespace** — organiser les ressources.
6. **Volumes** — comprendre le stockage.
7. **Ingress** — étudier le routage HTTP/HTTPS vers les applications.

Ensuite, vous pourrez conteneuriser une application Django, automatiser le build de l'image avec GitHub Actions, puis explorer Helm et AWS EKS. Gardez d'abord les exercices Kubernetes sur Minikube : cela permet de se concentrer sur les concepts avant d'ajouter la complexité du cloud.

## Documentation officielle

- Minikube — démarrage : <https://minikube.sigs.k8s.io/docs/start/>
- Minikube — pilotes : <https://minikube.sigs.k8s.io/docs/drivers/>
- Kubernetes — installer les outils : <https://kubernetes.io/docs/tasks/tools/>
- Kubernetes — concepts : <https://kubernetes.io/docs/concepts/>

---

**Fin de la fiche d'installation.**  
Quand vous publiez ce support, adaptez les commandes à votre système et consultez les documentations officielles si les instructions d'installation évoluent.





```bash 
Add-Content -Path "C:\Windows\System32\drivers\etc\hosts" -Value "127.0.0.1 mon-app.local" 
```


Pour vérifier :
Get-Content C:\Windows\System32\drivers\etc\hosts


```bash 
Get-Content C:\Windows\System32\drivers\etc\hosts
```