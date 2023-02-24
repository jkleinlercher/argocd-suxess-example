# argocd-suxess-example

Eine kleine Anleitung wie man ArgoCD lokal mit k3d installiert und eine Beispiel-Applikation erstellt.

# ArgoCD installieren

für Details siehe auch https://argo-cd.readthedocs.io/en/stable/getting_started/

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.6.2/manifests/install.yaml
```
# ArgoCD ConfigMap erstellen

Hier konfigurieren wir dass der ArgoCD-Server "insecure" gestartet wird, damit wir der Ingress nicht über https auf den ArgoCD-Server zugreifen muss.

```
kubectl apply -f https://raw.githubusercontent.com/jkleinlercher/argocd-suxess-example/main/argocd-cmd-params-cm.yaml -n argocd
```
Anschließend muss der argocd-server durchgestartet werden:
```
kubectl rollout restart deployment argocd-server -n argocd
```
# Ingress für ArgoCD installieren
``` 
kubectl apply -f https://raw.githubusercontent.com/jkleinlercher/argocd-suxess-example/main/ingress-argocd.yaml -n argocd
```

Anmerkung:
https://nip.io/ service ist ein praktischer DNS wo man ohne die /etc/hosts zu faken einfach bestimmte Hostnamen verwenden kann, die von nip.io  dann auf 127.0.0.1 auflösen, z.B. argocd-127-0-0-1.nip.io

# ArgoCD aufrufen

Im Browser: http://argocd-127-0-0-1.nip.io:8081/

Username: admin

Das initiale Passwort kann wiefolgt ausgelesen werden: 

```
kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath='{.data.password}' |base64 -d
```
dieses Passwort im Browser eingeben

Anmerkung: mit dem Parameter jsonpath kann man bestimmte Felder ausgeben lassen


# Why-Kubernetes Applikation deklarativ erzeugen

```
kubectl apply -f https://raw.githubusercontent.com/jkleinlercher/argocd-suxess-example/main/why-kubernetes-application.yaml -n argocd
```

Anschließend auf die "why-kubernetes" Applikation klicken, dann auf den Button "Sync" klicken und rechts auf den Button "Synchronize" klicken.
Dann sollte die Applikation über http://why-kubernetes-127-0-0-1.nip.io:8081/ erreichbar sein.
