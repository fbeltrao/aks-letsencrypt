# Create AKS Cluster

The goal of this step is to create an AKS cluster in Azure.

## Requirements

- You have an Azure Subscription
- [You have access to AKS cluster](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

## Guide

1. Create the AKS cluster using the [cli](https://docs.microsoft.com/en-us/azure/aks/create-cluster) or [portal](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough-portal). Just make sure that:
   - You have access to service principal used (clientId and password), we will need it later. A way to accomplish is by [pre-creating](https://docs.microsoft.com/en-us/azure/aks/kubernetes-service-principal#pre-create-a-new-sp) and copying the generated values
   - Do not install HTTP application routing. At the time of writing it does not support custom domains.
  
   
***
[**First**](./readme.md) - [**Previous**](./use-azure-dns-for-a-domain.md) - [**Next**](./install-nginx-ingress.md)