# Network and HTTP Load Balancers

###### Setting default regions and zones for all resources:
Set default Region:
```
gcloud config set compute/region us-central1
```
Set default Zone:
```
gcloud config set compute/zone us-central1-a
```



## Create multiple Web Server Instances and a Firewall
For a load balancing scenario we must consider:
- creating multiple Compute Engine VM instances
- installing Apache 
- Adding a firewall rule to facilitate HTTP traffic

Adding first VM Instance:
```
gcloud compute instances create myFirstInstance --i age-family debian-9 --image-project debian-cloud \
--zone us-central1-a --tags myNetworkTag \ 
--metadata startup-script="#! /bin/bash
    sudo apt-get update
    sudo apt-get install apache2 -y
    sudo service apache2 restart
    echo '<!doctype html><html><body><h1>myFirstInstance</h1></body></html>' | tee /var/www/html/index.html"
```
Adding second VM Instance:
```
gcloud compute instances create mySecondInstance --i age-family debian-9 --image-project debian-cloud \
--zone us-central1-a --tags myNetworkTag \ 
--metadata startup-script="#! /bin/bash
    sudo apt-get update
    sudo apt-get install apache2 -y
    sudo service apache2 restart
    echo '<!doctype html><html><body><h1>mySecondInstance</h1></body></html>' | tee /var/www/html/index.html"
```
The gcloud call sets up the relevant instance parameters to create the VM, including the --metadata startup-script to facilitate Apache installtion.  
Crucially, the instances use ```--tags``` to group many VM instances together. This is used when deploying a firewall to all VM instances with a shared tag.
```
gcloud compute firewall-rules create myFirewallName --target-tags myNetworkTag --allow tcp:80
```
#### Verification
To verify the instances are now running, use the external IP addresses of the instances as follows:  
```curl http://[EXTERNAL_IP_ADDRESS_HERE]```.  
To view running instance information including external IP Address:
```
gcloud compute instances list
```

### Configure the load balancing service
Configuring a load balancing service involves determining a IP address. The VM instances will recieve packets that are destined for this static IP address.  
Instances made with a Compute engine image are automatically configured to handle this IP address. 

Create a static external IP address for you load balancer:
```
gcloud compute addresses create network-1b-ip-1 --region us-central1
```
Add legacy HTTP health check resource:
```
gcloud compute http-health-checks create basic-check
```
Create a target pool and use health check (required for the service to function):
```
gcloud compute target-pools create myPoolName --region us-central1 --http-health-check basic-check
```
Add instances to the pool:
```
gcloud compute target-pools add-instances myPoolName --instances myFirstInstance,mySecondInstance
```
Add a forwarding rule:
```
gcloud compute forwarding-rules create myRuleName --region us-central1 --ports 80 --address network-1b-ip-1 --target-pool myPoolName
```
You have configured your load balancing service!

### Sending Traffic to your instances
With a configured load balancing service deploed to your VM instances, you can begin sending traffic to the forwarding rule and watch the traffic despresed to different instances. 
To view external IP address of the forwarding rule used by the load balancer:
```
gcloud compute forwarding-rules describe myRuleName --region us-central1
```
Access the external IP address:
```
while true; do curl -m IP_ADDRESS_HERE; done
```
The response from the curl command will alternate across all instances. 
To exit, use ```CTRL + c```



## Createa a HTTP Load balancer
