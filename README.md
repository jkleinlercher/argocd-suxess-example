# argocd-suxess-example

Eine kleine Anleitung wie man ArgoCD lokal mit k3d installiert und eine Beispiel-Applikation erstellt.

# ArgoCD installieren

für Details siehe auch https://argo-cd.readthedocs.io/en/stable/getting_started/

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
# ArgoCD ConfigMap erstellen

Hier konfigurieren wir, dass der ArgoCD-Server "insecure" gestartet wird, damit der Ingress nicht über https auf den ArgoCD-Server zugreifen muss.

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
https://nip.io/  ist ein praktischer DNS wo man beliebige Subdomains von nip.io verwenden kann, die dann auf bestimmte IP-Adressen (je nach Hostname) auflösen, z.B. argocd-127-0-0-1.nip.io löst auf 127.0.0.1 auf. Details siehe https://nip.io/ . Damit spart man sich das Faken von DNS-Einträgen in der etc/hosts.

# ArgoCD aufrufen

Im Browser: http://argocd-127-0-0-1.nip.io:8081/

Username: admin

Das initiale Passwort kann wiefolgt ausgelesen werden: 

```
kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -d
```
dieses Passwort im Browser eingeben

Anmerkung: mit dem Parameter jsonpath kann man bestimmte Felder ausgeben lassen, siehe https://kubernetes.io/docs/reference/kubectl/jsonpath/


# Why-Kubernetes Applikation deklarativ erzeugen

Achtung: sollte why-kubernetes zuvor bereits ohne ArgoCD erstellt worden sein, sollte das ursprüngliche Deployment, Service und Ingress davor gelöscht werden:

```
kubectl delete deployment why-kubernetes
kubectl delete service why-kubernetes
kubectl delete ingress nginx
```

ArgoCD Applikationen kann man direkt in der ArgoCD-WebConsole erstellen, aber man kann sie auch deklarativ über eine K8s-CustomResource definieren und mit kubectl anwenden.

```
kubectl apply -f https://raw.githubusercontent.com/jkleinlercher/argocd-suxess-example/main/why-kubernetes-application.yaml -n argocd
```

Anschließend in der ArgoCD-WebConsole auf die "why-kubernetes" Applikation klicken, dann auf den Button "Sync" klicken und rechts auf den Button "Synchronize" klicken.
Dann sollten 10 Pods erstellt werden und die Applikation über http://why-kubernetes-127-0-0-1.nip.io:8081/ erreichbar sein.
