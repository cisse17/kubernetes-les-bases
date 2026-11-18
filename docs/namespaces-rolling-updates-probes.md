# Épisode 5 – Administration des ressources Kubernetes

## Objectif

Découvrir plusieurs mécanismes permettant d'administrer les ressources Kubernetes et de contrôler la disponibilité des applications.

## 1. Namespace

Les Namespaces permettent d'organiser les ressources dans un cluster Kubernetes.

Commandes :

```bash
kubectl get namespaces
kubectl create namespace dev
kubectl get pods -n dev
```

## 2. Annotations

Les annotations permettent d'ajouter des métadonnées aux ressources Kubernetes.

Exemple :

```bash
kubectl annotate deployment mon-deployment description="Application de démonstration"
```

## 3. Rolling Update

Permet de mettre à jour progressivement une application.

```bash
kubectl set image deployment/mon-deployment nginx=nginx:1.27
kubectl rollout status deployment/mon-deployment
kubectl rollout history deployment/mon-deployment
```

## 4. Rollback

Permet de revenir à une version précédente du Deployment.

```bash
kubectl rollout undo deployment/mon-deployment
```

## 5. Readiness Probe et Liveness Probe

- Readiness Probe : vérifie si le conteneur est prêt à recevoir du trafic.
- Liveness Probe : permet de détecter un conteneur qui ne fonctionne plus correctement et peut entraîner son redémarrage.

## 6. Manifest utilisé

Le Deployment utilisé dans cette démonstration est disponible ici :

[Consulter mon-deployment.yaml](../manifests/episode-03/mon-deployment.yaml)

## Ressources

- [Documentation officielle Kubernetes](https://kubernetes.io/docs/home/)