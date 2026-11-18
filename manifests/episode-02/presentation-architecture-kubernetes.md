# Épisode 2 – Architecture Kubernetes

## Objectif

Comprendre l'architecture interne de Kubernetes et le rôle de ses principaux composants.
Kubernetes repose sur deux grandes parties :
le Control Plane et les Worker Nodes.

## 1. Architecture générale

![Architecture Kubernetes](../assets/images/architecture-kubernetes.png)

## 2. Control Plane

Le Control Plane assure la gestion et le pilotage du cluster Kubernetes. 
Il pilote et supervise le cluster.

Principaux composants :
- kube-apiserver
- etcd
- kube-scheduler
- kube-controller-manager

## 3. Worker Nodes

Les Worker Nodes hébergent les applications conteneurisées. C'est à dire 
ils hébergent les Pods qui exécutent les applications.

Principaux composants :
- kubelet
- kube-proxy
- Container Runtime

## 4. Fonctionnement de kubectl

kubectl permet d'interagir avec l'API Kubernetes pour gérer les ressources du cluster.

## Ressources

- [Documentation officielle Kubernetes](https://kubernetes.io/docs/concepts/architecture/)