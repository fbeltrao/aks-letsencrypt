# Ingress to external URL

The goal of this step is deploy a ingress to a resource that is outside the cluster. In my case I wanted to have my blog alias URL (blog.&lt;your-domain>) redirected to my blog website hosted in an external provider.

There are better way to achieve that, for instance setting the rule directly on the DNS naming resolver. Just want to show how to make it possible.

## Requirements

- [You have an AKS Cluster deployed](./create-aks-cluster.md)
- [You have kubectl installed on your machine](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- [Kubectl has access to AKS cluster](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)
- [You have helm installed on AKS cluster](https://docs.microsoft.com/en-us/azure/aks/kubernetes-helm)
- [Azure CLI is installed](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
- [NGINX is installed on AKS](./install-nginx-ingress.md)
- [Certificate Manager is installed on AKS](./install-certificate-manager.md)
- This repository is cloned/forked (or you will type yaml files)

## Guide

### 1. Create the ingress for the external item

#### Using HTTP validation

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: blog
spec:
  secretName: blog
  issuerRef:
    name: letsencrypt
    kind: ClusterIssuer
  dnsNames:
  - blog.<your-domain>
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - blog.<your-domain>
```

And finally deploy the ingress

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: blog
  annotations:
    kubernetes.io/ingress.class: nginx
    kubernetes.io/tls-acme: 'true'
    nginx.ingress.kubernetes.io/permanent-redirect: <external-url-goes-here>
spec:
  tls:
  - hosts:
    - blog.<your-domain>
    secretName: blog
  rules:
  - host: blog.<your-domain>
    http:
      paths:
      - path: /
        backend:
          serviceName: default-http-backend
          servicePort: 80
```

#### Using the wildcard / Azure DNS validation

Only need to deploy the ingress, since a wildcard certificate was issued.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: blog
  annotations:
    kubernetes.io/ingress.class: nginx
    kubernetes.io/tls-acme: 'true'
    certmanager.k8s.io/acme-challenge-type: dns01
    certmanager.k8s.io/acme-dns01-provider: azure-dns
    nginx.ingress.kubernetes.io/permanent-redirect: <external-url-goes-here>
spec:
  tls:
  - hosts:
    - blog.<your-domain>
    secretName: wildcard
  rules:
  - host: blog.<your-domain>
    http:
      paths:
      - path: /
        backend:
          serviceName: default-http-backend
          servicePort: 80
```

***
[**First**](./readme.md) - [**Previous**](./install-certificate-manager.md)