# traefik_gateway_api

## install
```
kubectl apply -f ./deploy/00_rbac.yaml
kubectl apply -f ./deploy/01_crd.yaml
kubectl apply -f ./deploy/02_traefik.yaml
kubectl apply -f ./deploy/04_crd.yaml
```
## Verify install
```
kubectl get crd | grep networking.x-k8s.io
kubectl get po 
kubectl get gateway
kubectl get gatewayclasses
kubectl get httproutes
```
## Verify everything is working
```
kubectl get svc traefik
curl -kv -H "Host: whoami" https://127.0.0.1:32282 #Capture the TLS port
```
