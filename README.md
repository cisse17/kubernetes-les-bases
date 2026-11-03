# Kubernetes : les bases

Projet pour découvrir les objets fondamentaux de Kubernetes en local avec **Minikube**. Chaque manifest correspond à une brique de base, et la documentation pas à pas se trouve dans `docs/`.

> Support Bassirou Tech du tutoriel vidéo **Kubernetes - Les bases**  

## Ce que ce projet couvre

| Objet | Fichier | Rôle |
|---|---|---|
| **Pod** | `manifests/episode-01/pod.yaml` | La plus petite unité déployable : un ou plusieurs conteneurs |
| **Deployment** | `manifests/mon-deployment.yaml` | Gère plusieurs réplicas de pods, les mises à jour et le rollback |
| **Service** | `manifests/service.yaml` | Point d'accès stable (IP et DNS internes) vers un groupe de pods |
| **ConfigMap** | `manifests/configmap.yaml` | Configuration non sensible, injectée dans les pods |
| **Secret** | `manifests/secret.yaml` | Données sensibles (mots de passe, tokens) |
| **Ingress** | `manifests/ingress.yaml` | Routage HTTP depuis l'extérieur vers les services |
| **HPA** | `manifests/hpa.yaml` | Horizontal Pod Autoscaler : ajuste le nombre de pods selon la charge |

## Structure du dépôt

```
kubernetes-les-bases/
│
├── README.md
├── .gitignore
│
├── docs/
│   ├── README.md
│   ├── installation-minikube-kubernetes.md  # Installer Minikube et kubectl
│   ├── configurer-hosts-mon-app-local.md    # Accéder à l'app via un nom 
│   ├── architecture-kubernetes.md
│   ├── pods-deployments-services.md
│   ├── configmap-secrets-ingress.md
│   ├── namespaces-rolling-updates-probes.md
│   └── hpa.md
│
├── assets/
│   ├── images/
│       ├── architecture-kubernetes.png
│       
│   
│
└── manifests/
    ├── README.md
    ├── episode-01/
    │   └── pod.yaml
    ├── episode-02/
    │   └── presentation-arhitecture.md
    ├── episode-03/
    │   ├── mon-deployment.yaml
    │   └── service.yaml
    ├── episode-04/
    │   ├── configmap.yaml
    │   ├── secret.yaml
    │   └── ingress.yaml
    ├── episode-05/
    │   ├── deployment-rolling-update-rollback-annotations-namespace-probes.md
    │   
    └── episode-06/
        └── hpa.yaml
```

## Prérequis

- Un moteur de conteneurs (Docker Desktop par exemple)
- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)

L'installation détaillée est décrite dans [`docs/installation-minikube-kubernetes.md`](docs/installation-minikube-kubernetes.md).

## Démarrage rapide

### 1. Démarrer le cluster local

```bash
minikube start
kubectl get nodes
```

### 2. Activer les addons nécessaires

```bash
# Contrôleur Ingress (NGINX)
minikube addons enable ingress

# Métriques de ressources, requises par le HPA
minikube addons enable metrics-server
```

### 3. Déployer les manifests

L'ordre compte : la configuration doit exister avant les pods qui l'utilisent.

```bash
kubectl apply -f manifests/configmap.yaml
kubectl apply -f manifests/secret.yaml
kubectl apply -f manifests/mon-deployment.yaml
kubectl apply -f manifests/service.yaml
kubectl apply -f manifests/ingress.yaml
kubectl apply -f manifests/hpa.yaml
```

> `pod.yaml` est un exemple pédagogique d'un pod seul. Il est indépendant du Deployment : applique-le séparément pour comprendre la différence entre un pod nu et un pod géré.

### 4. Vérifier

```bash
kubectl get all
kubectl get ingress
kubectl get hpa
```

### 5. Accéder à l'application

Configure le nom de domaine local en suivant [`docs/configurer-hosts-mon-app-local.md`](docs/configurer-hosts-mon-app-local.md), puis ouvre l'adresse définie dans `ingress.yaml` dans ton navigateur.

Selon ton système et ton driver Minikube, il peut être nécessaire de lancer `minikube tunnel` dans un terminal séparé.

## Commandes utiles

```bash
# Observer
kubectl get pods -o wide
kubectl describe pod <nom-du-pod>
kubectl logs <nom-du-pod>
kubectl logs -f <nom-du-pod>              # suivre les logs en direct

# Déboguer
kubectl exec -it <nom-du-pod> -- sh
kubectl get events --sort-by=.metadata.creationTimestamp

# Mettre à l'échelle et mettre à jour
kubectl scale deployment <nom> --replicas=3
kubectl rollout status deployment <nom>
kubectl rollout undo deployment <nom>

# Accès rapide sans Ingress
kubectl port-forward service/<nom-du-service> 8080:80
```

## Nettoyage

```bash
kubectl delete -f manifests/
minikube stop        # arrêter le cluster
minikube delete      # supprimer le cluster
```

## Notes importantes

- **Un Secret n'est pas chiffré**, il est seulement encodé en base64. Ne commite jamais de vrais secrets dans Git. En production, utilise un gestionnaire dédié (AWS Secrets Manager, Sealed Secrets, etc.).
- **Le HPA** ne fonctionne que si `metrics-server` est actif et que le Deployment définit des `resources.requests`.
- **ConfigMap vs Secret** : ConfigMap pour la configuration publique, Secret pour tout ce qui est sensible.

## Pour aller plus loin

- Passer sur un cluster managé : **Amazon EKS**
- Remplacer l'Ingress NGINX par l'**AWS Load Balancer Controller** (ALB)
- Ajouter des `readinessProbe` et `livenessProbe`
- Gérer les manifests avec **Helm** ou **Kustomize**
- Provisionner le cluster avec **Terraform**

## Ressources

- [Documentation officielle Kubernetes](https://kubernetes.io/docs/home/)
- [Aide-mémoire kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- [Documentation Minikube](https://minikube.sigs.k8s.io/docs/)

