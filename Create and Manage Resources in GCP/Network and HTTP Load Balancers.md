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
HTTP(S) Load Balancing is implemented on Google Frent End (GFE).
You can configure URL rules to route between given a URL and a set of instances. Requests are always routed to the instance group that is closest to the user, assuming the group has enough capacity and is appropriate for the request. Otherwise, it is routed to the next closest group that does have capacity.  
  
To set up a load balancer with a compute engine backend, your VMs need to be in an instance group. This managed instance group provides VMs running the backed servers of an external HTTP loaad balancer. For this example, backends serve their own hostnames  
  
Create a load balancer template:
```
gcloud compute instance-templates create lb-backend-template \
--region=us-central1 \
--network=default \
--subnet=default \
--tags=allow-health-check \
--image-family=debian-9 \
--image-project=debian-cloud \
--metadata=startup-script='#! /bin/bash
 apt-get update
 apt-get install apache2 -y
 a2ensite default-ssl
 a2enmod ssl
 vm_hostname="$(curl -H "Metadata-Flavor:Google" \
 http://169.254.169.254/computeMetadata/v1/instance/name)"
 echo "Page served from: $vm_hostname" | \
 tee /var/www/html/index.html
 systemctl restart apache2'
```
Create a managed instance group based on the template:
```
gcloud compute instance-groups managed create lb-backend-group \
--template=lb-backend-template --size=2 --zone=us-central1-a
```
Create the fw-allow-health-check firewall rule.  
This is an ingress rule that allows traffic from the Google Cloud health checking systems (130.211.0.0/22 and 35.191.0.0/16). This lab uses the target tag allow-health-check to identify the VMs.  
```
gcloud compute firewall-rules create fw-allow-health-check \
--network=default \
--action=allow \
--direction=ingress \
--source-ranges=130.211.0.0/22,35.191.0.0/16 \
--target-tags=allow-health-check \
--rules=tcp:80
```
With the instances up and running, set up a global static external IP address that your customers use to reach your load balancer.
```
gcloud compute addresses create lb-ipv4-1 \
--ip-version=IPV4 \
--global
```
Note the IPv4 address that was resereved:
```
gcloud compute addresses describe lb-ipv4-1 \
--format="get(address)" \
--global
```
Create a healthcheck for the load balancer:
```
gcloud compute health-checks create http http-basic-check \
--port 80
```
Create a backend service:
```
gcloud compute backend-services create web-backend-service \
--protocol=HTTP \
--port-name=http \
--health-checks=http-basic-check \
--global
```
Add your instance group as the backend to the backend service:
```
gcloud compute backend-services add-backend web-backend-service \
--instance-group=lb-backend-group \
--instance-group-zone=us-central1-a \
--global
```
Create a URL map to route the incoming requests to the default backend service:
```
gcloud compute url-maps create web-map-http \
--default-service web-backend-service
```
Create a URL map to route the incoming requests to the default backend service:
```
gcloud compute url-maps create web-map-http \
--default-service web-backend-service
```
Create a target HTTP proxy to routwe requests to your URL map:
```
gcloud compute target-http-proxies create http-lb-proxy \
--url-map web-map-http
```
Create a global rule to route incoming requests to the proxy:
```
gcloud compute forwarding-rules create http-content-rule \
--address=lb-ipv4-1\
--global \
--target-http-proxy=http-lb-proxy \
--ports=80
```

### Test traffic sent to your instances
In the Cloud Console, from the Navigation menu, go to Network services > Load balancing.
Click on the load balancer that you just created (web-map-http).
In the Backend section, click on the name of the backend and confirm that the VMs are Healthy. If they are not healthy, wait a few moments and try reloading the page.
When the VMs are healthy, test the load balancer using a web browser, going to ```http://IP_ADDRESS/```, replacing IP_ADDRESS with the load balancer's IP address.
