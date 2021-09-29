# liferay_k8
This readme assumes that the user has access to a Kubernetes environment, in my case I used an instance of Minikube, and that a namespace has been defined. Once checked out run the following from the root of this repo:

kubectl create -f <X>_deploy.yaml -n=<namespace>

Where X is the application manifest you wish to deploy and namespace is the namespace defined in the Kubernetes instance.  For example to create the components defined for Liferay in a namespace called liferay the command to deploy would be:
  
kubectl create -f lr_deploy.yaml -n=liferay
  
If you want to delete all components created in this manner run the following command:
  
kubectl delete -f <X>_deploy.yaml -n=<namespace>

Once all three applications have been deployed it will be neccassary to copy over the configuration files needed for Liferay to connect to the database and Elasticsearch.  These files are found in the /mount directory.  To do so run the following commands:
  
Identify the name of the pod Liferay is running in:
kubectl get pods -l app=liferay -n=<namespace>

Example output:
NAME                       READY   STATUS    RESTARTS   AGE
liferay-556cdc748f-nk7wz   1/1     Running   0          3h10m
  
Then copy the files to that pod with the following command:
kubectl cp ./mount/files liferay-556cdc748f-nk7wz:/mnt/liferay -n=<namespace>

Then delete the Liferay pod, the pod will restart and apply the newly copied config files on startup:
kubectl delete -n liferay pod liferay-556cdc748f-nk7wz
