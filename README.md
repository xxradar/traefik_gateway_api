# Traefik_gateway_api

## Install Traefik w/ KubernetesGateway provide
```
kubectl apply -f ./deploy/00_rbac.yaml
kubectl apply -f https://raw.githubusercontent.com/xxradar/traefik_k8s_ingressrouter_learnings/2.0/00_traefik_crd.yaml #Traefik CRD
kubectl apply -f ./deploy/01_crd.yaml #Gateway CRD
kubectl apply -f ./deploy/02_traefik.yaml
```
## Install the whoami demo app
```
kubectl apply -f ./deploy/03_whoami.yaml
```

## Define the gateway api resources
Create a certificate
```
openssl req -x509 -newkey rsa:2048 -keyout tls.key -out tls.crt -days 365 -nodes -subj "/CN=whoami"
kubectl create secret tls mysecret --cert=./tls.crt --key=./tls.key
```
```
kubectl apply -f ./deploy/04_gateway_api.yaml

```
## Verify install
```
kubectl get crd | grep networking.x-k8s.io
kubectl get po 
kubectl logs traefik-xxxxxxx 
kubectl get gateway
kubectl get gatewayclasses
kubectl get httproutes
```
## Verify everything is working
```
kubectl get svc traefik
 .. capture the TLS port ...
 
curl -kv -H "Host: whoami" https://127.0.0.1:32282/foo 
 .... working example ....
 
curl -kv -H "Host: whoami" https://127.0.0.1:32282/ 
.... page not found ... no exact match ...
```

![whoami](https://github.com/xxradar/traefik_gateway_api/blob/main/images/Screenshot%202021-04-26%20at%2010.56.49.png)
![whoami](https://github.com/xxradar/traefik_gateway_api/raw/main/images/Screenshot%202021-04-26%20at%2010.51.17.png)

