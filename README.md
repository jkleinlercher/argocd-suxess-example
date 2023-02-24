# argocd-suxess-example

# ArgoCD installieren

für Details siehe auch https://argo-cd.readthedocs.io/en/stable/getting_started/

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.6.2/manifests/install.yaml
```

# Ingress für ArgoCD installieren
``` 
kubectl apply -f https://raw.githubusercontent.com/jkleinlercher/suxess-argocd-example/ingress-argocd.yaml -n argocd
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

