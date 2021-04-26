# traefik_gateway_api

## install Traefik w/ KubernetesGateway provide
```
kubectl apply -f ./deploy/00_rbac.yaml
kubectl apply -f https://raw.githubusercontent.com/xxradar/traefik_k8s_ingressrouter_learnings/2.0/00_traefik_crd.yaml #Traefik CRD
kubectl apply -f ./deploy/01_crd.yaml #Gateway CRD
kubectl apply -f ./deploy/02_traefik.yaml
```
## install the whoami demo app
```
kubectl apply -f ./deploy/03_whoami.yaml
```

## define the gateway api resources
Create a certificate
```
openssl req -x509 -newkey rsa:2048 -keyout tls.key -out tls.crt -days 365 -nodes -subj "/CN=whoami"`
kubectl create secret tls mysecret --cert=./tls.crt --key=./tls.key
```
```
kubectl apply -f ./deploy/04_gateway_api.yaml

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
curl -kv -H "Host: whoami" https://127.0.0.1:32282/foo #Capture the TLS port 
 .... working example ....
 
curl -kv -H "Host: whoami" https://127.0.0.1:32282/ 
.... page not found ... no exact match ...
```
