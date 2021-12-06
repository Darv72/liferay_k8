# liferay_k8

## Overview
The manifests within this repo can be used to deploy a basic Liferay stack onto an Azure Kubernetes cluster.  The image used in the Elasticsearch manifest uses a build that was created by Liferay and includes the necessary plugins required to run Elasticsearch with Liferay.

## AKS Quickstart
This readme assumes that access to an AKS cluster is in place and that the AzureCLI tools have been installed and all noted commands are run locally.  First connect to the Azure Portal using the following:

> az login


Once authenticated you will need to connect to the AKS cluster.  The required command can be found on the Connect tab of the AKS cluster and will follow a syntax like this:

> az aks get-credentials --resource-group resourcegroup --name clustername


To deploy any of the manifests included in this repo into AKS first clone the repo to your local PC then run the following command:

> kubectl apply -f X_deploy.yaml


Where X is the application manifest you wish to deploy.  To delete any of the items defined within the manifest run the following:
  
> kubectl delete -f X_deploy.yaml


Once all three applications have been deployed it will be neccassary to copy over the configuration files needed for Liferay to connect to the database and Elasticsearch.  These files are found in the /mount directory.  To do so run the following commands:
  
Identify the name of the pod Liferay is running in:

> kubectl get pods


Example output:  

| NAME | READY | STATUS | RESTARTS | AGE |
| --- | --- | --- | --- | --- |
| liferay-556cdc748f-nk7wz | 1/1 | Running | 0 | 3h10m |  


Then copy the files to that pod with the following command:

> kubectl cp ./mount/files liferay-556cdc748f-nk7wz:/mnt/liferay


Then delete the Liferay pod, the pod will restart and apply the newly copied config files on startup:

> kubectl delete pod liferay-556cdc748f-nk7wz

To create a route to the Liferay instance first configure an ingress controller for the AKS cluster.  There are many options available for setting up an ingress controller but in my case I deployed the Kubernetes NGINX ingress controller.  This can be done by deploying the raw manifest content from the related git repo (https://github.com/kubernetes/ingress-nginx) using the following command:

> kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.0.4/deploy/static/provider/cloud/deploy.yaml

Once the ingress controller is in place then deploy the ingress manifest to create the route:

> kubectl apply -f ingress.yaml

Once the route has been completed you can check on it's status with the following command:

> kubectl get ingress

The output should look like this, once the External IP Address has been defined you should be able to access Liferay by putting that IP into your browser.

  
 
