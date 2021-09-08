# AKSTutorials

## References
Tutorial: 
1 - Prepare application for AKS](https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-prepare-app)<br>
2 - Create container registry

## 1 Prepare an application for Azure Kubernetes Service (AKS)

Step 1 Get application code

Step 2 Create container images

## 2 Deploy and use Azure Container Registry

**Stpe 1 Create an Azure Container Registry**<br>
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

**Step 2 Login to the container registry**<br>
```bash
az acr login --name ${acr_name}
```
The command returns a *Login Succeeded* message once completed.<br>

**Step 3 Tag a container image**<br>
To see a list of your current local images<br>
```bash
docker images
```
To use the *azure-vote-front* container image with ACR, the image needs to be tagged with the login server address of your registry. This tag is used for routing when pushing container images to an image registry.<br>

To get the login server address<br>
```bash
az acr list --resource-group ${rg_name} --query "[].{acrLoginServer:loginServer}" --output table
```
Tag your local *azure-vote-front* image with the acrLoginServer address of the container registry. To indicate the image version, add *:v1* to the end of the image name:<br>
```bash
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 <acrLoginServer>/azure-vote-front:v1
```
To verify the tags are applied<br>
```bash
docker images
```

**Step 4 Push images to registry**<br>
With your image built and tagged, push the azure-vote-front image to your ACR instance.<be>
```bash
docker push <acrLoginServer>/azure-vote-front:v1
```
It may take a few minutes to complete the image push to ACR.<br>

**Step 5 List images in registry**<br>
To return a list of images that have been pushed to your ACR instanc.<br>
```bash
az acr repository list --name ${acr_name} --output table
```
To see the tags for a specific image.
```bash
az acr repository show-tags --name ${acr_name} --repository azure-vote-front --output table
```

3 - Create Kubernets cluster

Step 1. Create a Kubernetes cluster

Create an AKS cluster using az aks create.
```bash
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Step 2. Install the Kubernets CLI

```bash
az aks install-cli
```

Step 3. Connect to cluster using kubectl

To configure kubectl to connect to your Kubernetes cluster
```bash
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
To verify the connection to your cluster
```bash
kubectl get nodes
```

4 - Run application, Tutorial: Run applications in Azure Kubernetes Service (AKS)
 
Step 1. Update the manifest file
Get the ACR login server name
```bash
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```
Make sure that you're in the cloned azure-voting-app-redis directory, then open the manifest file with a text editor
```bash
vi azure-vote-all-in-one-redis.yaml
```
Replace microsoft with your ACR login server name. The image name is found on line 60 of the manifest file.
```bash
```
Step 2. Deploy the application
deploy your application
```bash
kubectl apply -f azure-vote-all-in-one-redis.yaml
```
Step 3. Test the application
To monitor progress
```bash
kubectl get service azure-vote-front --watch
```
When the EXTERNAL-IP address changes from pending to an actual public IP address, use CTRL-C to stop the kubectl watch process.<br>
To see the application in action, open a web browser to the external IP address of your service.<br>
view the status of your containers<br>
```bash
kubectl get pods -o wide
```

Troubleshoot

Step 1. Create the SSH connection to a Linux node, e.g. aks-nodepool1-12345678-vmss000000.
```bash
kubectl debug node/<aks node name> -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Step 2. Enter the Containerd environment.
```bash
chroot /host
```

Step 3. Check the container images. 
```bash
ctr -n k8s.io image list | grep -I <Container Image Name>
```

Step 4. Check the Container.
```bash
ctr -n k8s.io container list
```
