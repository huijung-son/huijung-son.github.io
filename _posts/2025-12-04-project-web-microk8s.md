---
title: "Web Project - Chat Infrastructure (MikroK8s)"
date: 2025-12-04 14:00:00
categories: [Project, Web]
tags: [Chat]
description: ""
---

### 개발 환경 구축

[Caddy Install]({% post_url 2026-01-05-reference-install-caddy %})

```text
http://127.0.0.1:8080 {
  handle_path /api/* {
    reverse_proxy 127.0.0.1:8000
  }

  handle {
    reverse_proxy 127.0.0.1:3000
  }
}
```

- Caddyfile 을 활용하여 쿠버네티스 운영 환경을 비슷하게 구축한다
- "127.0.0.1:8080" -> 실제 DNS 주소
- "/api/*" -> 주소/api/* 로 들어오는 요청은 reverse_proxy api 서버 주소로 프록시
- 해당 방법은 ws 소켓 요청을 노출하되 api 요청은 내부에서만 호출 가능하도록 만든 구조

### Ubuntu multipass

[https://canonical.com/multipass](https://canonical.com/multipass){: target="_blank"}

- Ubuntu 22.04

### Shell Command

```shell
sudo apt update
sudo microk8s status
sudo microk8s enable rbac
sudo microk8s enable dashboard
```

- dashboard description

```text
If RBAC is not enabled access the dashboard using the token retrieved with:

microk8s kubectl describe secret -n kube-system microk8s-dashboard-token

Use this token in the https login UI of the kubernetes-dashboard service.

In an RBAC enabled setup (microk8s enable RBAC) you need to create a user with restricted
permissions as shown in:
https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md
```

```shell
sudo microk8s kubectl port-forward -n kube-system service/kubernetes-dashboard 10443:443 --address 0.0.0.0
sudo microk8s enable registry
sudo microk8s enable ingress
sudo microk8s enable cert-manager
```

- cert-manager description

```text
Cert-manager is installed. As a next step, try creating an Issuer
for Let's Encrypt by creating the following resource:

$ microk8s kubectl apply -f - <<EOF
---
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: letsencrypt
spec:
  acme:
    # You must replace this email address with your own.
    # Let's Encrypt will use this to contact you about expiring
    # certificates, and issues related to your account.
    email: me@example.com
    server: https://acme-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      # Secret resource that will be used to store the account's private key.
      name: letsencrypt-account-key
    # Add a single challenge solver, HTTP01 using nginx
    solvers:
    - http01:
        ingress:
          ingressClassName: nginx
EOF

Then, you can create an ingress to expose 'my-service:80' on 'https://my-service.example.com' with:

$ microk8s enable ingress
$ microk8s kubectl create ingress my-ingress \
    --annotation cert-manager.io/issuer=letsencrypt \
    --rule 'my-service.example.com/*=my-service:80,tls=my-service-tls'
```

```shell
sudo microk8s enable argocd
```

- argocd description

```text
ubuntu@electric-woodlouse:~$ sudo microk8s enable argocd
Infer repository community for addon argocd
Infer repository core for addon helm3
Addon core/helm3 is already enabled
Installing ArgoCD (Helm v5.34.3)
"argo" has been added to your repositories
Release "argo-cd" does not exist. Installing it now.
NAME: argo-cd
LAST DEPLOYED: Fri Jan  2 08:11:37 2026
NAMESPACE: argocd
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
In order to access the server UI you have the following options:

1. kubectl port-forward service/argo-cd-argocd-server -n argocd 8080:443

   and then open the browser on http://localhost:8080 and accept the certificate

2. enable ingress in the values file `server.ingress.enabled` and either
  - Add the annotation for ssl passthrough: https://argo-cd.readthedocs.io/en/stable/operator-manual/ingress/#option-1-ssl-passthrough
  - Set the `configs.params."server.insecure"` in the values file and terminate SSL at your ingress: https://argo-cd.readthedocs.io/en/stable/operator-manual/ingress/#option-2-multiple-ingress-objects-and-hosts


After reaching the UI the first time you can login with username: admin and the random password generated during the installation. You can find the password by running:

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

(You should delete the initial secret afterwards as suggested by the Getting Started Guide: https://argo-cd.readthedocs.io/en/stable/getting_started/#4-login-using-the-cli)
ArgoCD is installed
```

```shell
sudo microk8s kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
sudo microk8s kubectl port-forward service/argo-cd-argocd-server -n argocd 8080:443 --address 0.0.0.0
```
