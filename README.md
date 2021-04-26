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

## Verifying the certificate
```
ubuntu@ip-10-11-2-85:~$ openssl s_client -servername whoami  -showcerts -connect 127.0.0.1:31451
CONNECTED(00000005)
depth=0 CN = whoami
verify error:num=18:self signed certificate
verify return:1
depth=0 CN = whoami
verify return:1
---
Certificate chain
 0 s:CN = whoami
   i:CN = whoami
-----BEGIN CERTIFICATE-----
MIIDAzCCAeugAwIBAgIUThs1RKNqu2RqrKyVQnC8iMgyNDUwDQYJKoZIhvcNAQEL
BQAwETEPMA0GA1UEAwwGd2hvYW1pMB4XDTIxMDQyNjA4NDExM1oXDTIyMDQyNjA4
NDExM1owETEPMA0GA1UEAwwGd2hvYW1pMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8A
MIIBCgKCAQEA7o4LFprnIb6QgdWhTR5IM7hI1OcfQm4XwhfSc9sjSElyztaSJ8E2
WbFs5f9C9y53KqIz1YXEnwVA+8pZbTsSFdESC6iIlWqmAOhg4nkvyho+Kd+ma03f
3U7b8iKxZSbAq373r9f6kWy7HvnHdbzh2ddUfoXcOiqkmeNa1IQFZLVJEKp35uMG
34I4INgmGdJJPrinFOMzjfJpu9T8IyLGKjcAu8kbiTIwOWEKgR8IyvqJGQYiyXQq
E7rlyrOvmSx7+fGKETGNra0YiMQCNPSdapPAjwL16oibajqaWpBCer7yDGQkOtlA
Y09JSOgn7HeuuwL/HPo5LFi/04u0gmF65QIDAQABo1MwUTAdBgNVHQ4EFgQUOhxq
O/ZlfRjF0CDeNdcq1Uq6uBwwHwYDVR0jBBgwFoAUOhxqO/ZlfRjF0CDeNdcq1Uq6
uBwwDwYDVR0TAQH/BAUwAwEB/zANBgkqhkiG9w0BAQsFAAOCAQEAiRzZoTMPKbf0
raWQ7C4wppro4xJW+obvft+8TfLbNSBvlOpdyBxvtVP4eLMteyHvWCdgR/5o2z6k
DlCsZy6eusLxKVdZRmK4UZqHAZNSGcQB/Ah5DuwvbkVAcVNr9llkXt9UUwCou4Cn
MRgHNtuI7FGPuh1klAbBY7JcVJbflwE3NHGw12op1+jn3a+7AAvteGm8WjJqjKGR
c0WrRMdlY1epR4vDmHKw5M7oR6M6WKT5GAubbI2dQqsUX+VnDfxLFykOYT328NLp
+srW0KdzItSPb275etW+JTPP5V44oRxNKqlPtIZLXsEOIXoXA1NIfQMTXJLFAVs/
0kWTDg5B4Q==
-----END CERTIFICATE-----
---
Server certificate
subject=CN = whoami

issuer=CN = whoami

---
No client certificate CA names sent
Peer signing digest: SHA256
Peer signature type: RSA-PSS
Server Temp Key: X25519, 253 bits
---
SSL handshake has read 1331 bytes and written 388 bytes
Verification error: self signed certificate
---
New, TLSv1.3, Cipher is TLS_AES_256_GCM_SHA384
Server public key is 2048 bit
Secure Renegotiation IS NOT supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
Early data was not sent
Verify return code: 18 (self signed certificate)
---
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 42E961FB1C226FEB254ECC91C8C10E8262545F904BF30DD15250A1CB86DFDF40
    Session-ID-ctx:
    Resumption PSK: 40EF30D1680F3B97E5814577E4996651B67A08E8192E59D17FCC9BFB8505066B16C3199D7B44DF94904B6163926616C0
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 604800 (seconds)
    TLS session ticket:
    0000 - 34 d7 26 82 0a 9d f3 30-de c0 e3 a4 36 6a ef 6c   4.&....0....6j.l
    0010 - e8 3a ba c9 96 8a 79 3d-03 c3 77 7c 24 56 cb bb   .:....y=..w|$V..
    0020 - d9 d2 72 7a ca 48 05 36-61 0f 00 de 33 5f 2a a7   ..rz.H.6a...3_*.
    0030 - 99 7d 07 03 8a 76 b7 40-74 53 72 cc 1e 26 99 25   .}...v.@tSr..&.%
    0040 - 7c e6 8c df 8f 43 04 f8-e7 ec 8e e5 d0 fc 13 db   |....C..........
    0050 - 02 85 8b 26 f1 b8 b4 0a-f1 da 58 57 73 cf 29 fb   ...&......XWs.).
    0060 - 89 d2 87 98 45 b1 d8 a9-ed 9a 3d ae a7 bf d3 58   ....E.....=....X
    0070 - 64 62 7e 82 2b 8e a9 b0-58 3c 29 ea cc 2d 4e 9d   db~.+...X<)..-N.
    0080 - f9                                                .

    Start Time: 1619428122
    Timeout   : 7200 (sec)
    Verify return code: 18 (self signed certificate)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
GET /foo HTTP/1.1
Host: whoami

HTTP/1.1 200 OK
Content-Length: 373
Content-Type: text/plain; charset=utf-8
Date: Mon, 26 Apr 2021 09:09:03 GMT

Hostname: whoami-75d5976d8d-t6mkr
IP: 127.0.0.1
IP: 192.168.210.67
RemoteAddr: 192.168.249.66:40646
GET /foo HTTP/1.1
Host: whoami
User-Agent: Go-http-client/1.1
Accept-Encoding: gzip
X-Forwarded-For: 192.168.216.128
X-Forwarded-Host: whoami
X-Forwarded-Port: 443
X-Forwarded-Proto: https
X-Forwarded-Server: traefik-76d5ffbb8f-w945w
X-Real-Ip: 192.168.216.128

ubuntu@ip-10-11-2-85:~$
```
