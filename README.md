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

kubectl get pvc lh-test 

kubectl delete pvc lh-test
```

IP-Vergabe und ARP-Bekanntmachung von MetalLB prüfen:

```
kubectl create deployment nginx-test --image=nginx:alpine

kubectl expose deployment nginx-test --port=80 --type=LoadBalancer --name=nginx-test-lb

kubectl get svc nginx-test-lb -w

kubectl get servicel2status -A -o wide 

curl -sI http://192.168.3.246

kubectl delete deployment nginx-test && kubectl delete service nginx-test-lb
```

HAProxy Ingress und TLS prüfen

```
kubectl create deployment web --image=nginx:alpine

kubectl expose deployment web --port=80

cat > ingress-test.yaml <<'EOF'
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-test
  namespace: default
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-prod
spec:
  ingressClassName: haproxy
  tls:
    - hosts: ["portal.opendesk.example.com"]
      secretName: web-test-tls
  rules:
    - host: portal.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service: { name: web, port: { number: 80 } }
EOF

kubectl apply -f ingress-test.yaml

kubectl get certificate web-test-tls -w      

curl -v https://portal.opendesk.example.com

kubectl delete ingress web-test && kubectl delete deployment web && kubectl delete service web

kubectl delete secret web-test-tls
```
