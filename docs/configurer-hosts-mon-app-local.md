# Configurer `mon-app.local` dans le fichier hosts

Ce guide explique comment associer le nom local `mon-app.local` à l'adresse IP `127.0.0.1` sur Windows, Linux et macOS, afin de tester une application Kubernetes exposée par Ingress.

```text
http://mon-app.local
```

> **Important :** `127.0.0.1` désigne la boucle locale de la machine. Utilisez cette adresse si votre configuration Minikube/Ingress rend l'application accessible sur localhost (par exemple avec le tunnel adapté). Si Ingress est exposé sur une autre adresse IP, remplacez `127.0.0.1` par cette adresse.

## 1. Windows

Ouvrez **PowerShell en tant qu'administrateur**, puis exécutez :

```powershell
Add-Content -Path "$env:SystemRoot\System32\drivers\etc\hosts" -Value "127.0.0.1 mon-app.local"
```

Vérifier le fichier :

```powershell
Get-Content "$env:SystemRoot\System32\drivers\etc\hosts"
```

Vider le cache DNS :

```powershell
ipconfig /flushdns
```

Tester la résolution du nom :

```powershell
ping mon-app.local
```

Le ping vérifie la résolution du nom, mais ne garantit pas à lui seul que l'Ingress répond.

## 2. Linux

Dans un terminal :

```bash
echo "127.0.0.1 mon-app.local" | sudo tee -a /etc/hosts
```

Vérifier le fichier :

```bash
cat /etc/hosts
```

Tester la résolution :

```bash
getent hosts mon-app.local
```

Ou :

```bash
ping -c 1 mon-app.local
```

## 3. macOS

Dans l'application Terminal :

```bash
echo "127.0.0.1 mon-app.local" | sudo tee -a /etc/hosts
```

Saisissez le mot de passe de votre session si macOS le demande.

Vérifier le fichier :

```bash
cat /etc/hosts
```

Vider le cache DNS :

```bash
sudo dscacheutil -flushcache
sudo killall -HUP mDNSResponder
```

Tester la résolution :

```bash
ping -c 1 mon-app.local
```

## 4. Tester l'accès à l'application Kubernetes

Vérifier Minikube et les ressources Kubernetes :

```bash
minikube status
kubectl get pods -A
kubectl get ingress -A
```

Si votre configuration utilise `minikube tunnel`, lancez-le dans un terminal séparé et laissez-le actif :

```bash
minikube tunnel
```

Tester l'accès HTTP :

```bash
curl -i http://mon-app.local
```

Vous pouvez aussi ouvrir cette adresse dans votre navigateur :

```text
http://mon-app.local
```

Si le nom est résolu mais que l'application ne répond pas, vérifiez le contrôleur Ingress, la règle Ingress, le Service, les Pods et le mode d'exposition réseau de votre pilote Minikube.

## 5. Supprimer l'entrée

Ouvrez le fichier hosts avec les droits nécessaires et supprimez la ligne :

```text
127.0.0.1 mon-app.local
```

Emplacements du fichier :

| Système | Chemin |
|---|---|
| Windows | `C:\Windows\System32\drivers\etc\hosts` |
| Linux | `/etc/hosts` |
| macOS | `/etc/hosts` |

> Évitez d'ajouter plusieurs fois la même entrée. Si `mon-app.local` existe déjà, modifiez la ligne existante au lieu d'en ajouter une nouvelle.

## Résumé rapide

| Système | Commande |
|---|---|
| Windows (PowerShell administrateur) | `Add-Content -Path "$env:SystemRoot\System32\drivers\etc\hosts" -Value "\`n127.0.0.1 mon-app.local"` |
| Linux | `echo "127.0.0.1 mon-app.local" \| sudo tee -a /etc/hosts` |
| macOS | `echo "127.0.0.1 mon-app.local" \| sudo tee -a /etc/hosts` |

Entrée attendue dans le fichier hosts :

```text
127.0.0.1 mon-app.local
```
