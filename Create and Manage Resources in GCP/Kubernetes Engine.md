# Kubernetes
Google Kubernetes Engine (GKE) facilitates a environment to deploy, manage and scale containerized applications.  
Kubernetes orchestrates many compute engine instances grouped into clusters.


### Primary Applications
- Load Balancing
- Node Pools
- Automatic Scaling
- Automatic Upgrades
- Node auto-repair
- Logging and Monitoring


## Create a Google Kubernetes Engine (GKE) Cluster
A Cluster consists of at least one master machine and multiple worker machines called nodes. These nodes are VM instances that run Kubernetes processes.  
Cluster names must start with a letter, end with an alphamuneric and cannot be longer than 40 characters.

To Create a cluster, run the following command in gcloud:
```
glcloud container clusters create myClusterName
```

### Authentication Credentials for a Cluster
To interact with a cluster, you need to initialise authentication credentials. This can be provided to the cluster seen below:
```
gcloud container clusters get-credentials myClusterName
```


## Deploying applications to a cluster
An authenticated cluster can now facilitate the deployment of containerized apllications. 
GKE uses Kubernetes objects to create and manage your clusters resources. 
- The [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) object supports deploying stateless applications like web servers
- The [Service](https://kubernetes.io/docs/concepts/services-networking/service/) objects define rules and load balancing for accessing your application from the internet.

To create a new Deployment use the kubectle create command:
```
kubectle create deployment myObject --image=myImage
```
Where myObject references the object to be deployed. 
--image references the container image to deploy. This command pulls the example image from a Container Registry bucket. 
you can specify the image version top be pulled with ```myImage/object:version```, else the latest version will be pulled.

To create a Kubernetes Service, a resource to expose applications to external traffic:
```
kubectl exoose deployment myObject --type=LoadBalancer --port 8080
```
inspect the object Service, run kubectl get:
```
kubectl get service
```
View the application from web browser with the following URL: http://[External_IP]:8080



## Delete a Cluster
To delete a cluster:
```
gcloud container clusters delete myClusterName
```
When prompted type Y to confirm

