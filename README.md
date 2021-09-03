# AKSTutorials

## References
Tutorial: 
1 - Prepare application for AKS](https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-prepare-app)
2 - Create container registry

## 1 Prepare an application for Azure Kubernetes Service (AKS)

Step 1 Get application code

Step 2 Create container images

## 2 Deploy and use Azure Container Registry

Stpe 1 Create an Azure Container Registry<br>
Create a resource group<br>
```bash
rg_name=testcentosrg
location_name=eastasia
az group create --name ${rg_name} --location ${location_name}
```
Create an Azure Container Registry instance<br>
```bash
acr_name=neotestmyacr
az acr create --resource-group ${rg_name} --name ${acr_name} --sku Basic
```

Step 2 Login to the container registry
```bash
az acr login --name ${acr_name}
```
