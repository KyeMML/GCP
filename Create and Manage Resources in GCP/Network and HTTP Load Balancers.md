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



## Create multiple Web Server Instances and Load Balancing
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
To verify the instances are now running, use the external IP addresses of the instances as follows: ```curl http://[EXTERNAL_IP_ADDRESS_HERE]```
To view running instance information including external IP Address:
```
gcloud compute instances list
```
