Vorlagen für den Bau eines Kubernetes-Clusters für openDesk auf Basis von Talos Linux für ein Deployment von openDesk in der Community Edition, wie im Artikel 
"openDesk auf einem Kubernetes-Cluster installieren" (erschienen bei heise+ und im c't Magazin) beschrieben.

<img width="6720" height="4480" alt="ndi Digitale_-2035560561-Produktdetail missi-1" src="https://github.com/user-attachments/assets/5a2d5bf3-917f-479c-98a4-912aeb7ef8c4" />

Bild: Melissa Ramson / heise medien

Nützliche Befehle, falls es bei der Anleitung Probleme gibt:

Prüfen, ob Longhorn als CSI funktioniert:

```
kubectl apply -f - <<'EOF'
apiVersion: v1
kind: PersistentVolumeClaim
metadata: { name: lh-test }
spec:
  accessModes: ["ReadWriteOnce"]
  storageClassName: longhorn
  resources: { requests: { storage: 1Gi } }
EOF
kubectl get pvc lh-test        # muss binnen Sekunden Bound werden
kubectl delete pvc lh-test
```
