
# Virtual Machines in GCP 
References[^1].  
You can start a Virtual Machine in GCP with the console or the command line tool gcloud, pre-installed in Google cloud shell.

## Virtual Machines in GCP Console
### Creating a new Compute Engine:
The Parameters you can configure when creating a new instance could include:
- Name
- [Region and Zone](https://github.com/KyeMML/GCP/blob/main/Create%20and%20Manage%20Resources%20in%20GCP/Regions%20and%20Zones.md)
- Series
- Machine Type
- Boot Disk
- Firewall

To use SSH to connect to the virtual machine, you can use the console where it displays current running VM instances. 


## Virtual Machines in GCP Command Line Gcloud.
### Creating a new Virtual Machine Instance
In the Cloud Shell, use gcloud to create a new VM instance from the command line:  
```
gcloud compute instances create myVM --machine-type n1-standard-2 --zone us-central1-f
```  
I have named the VM instance myVM.  
In the Cloud Console, under Compute Engine > VM instances, this created VM instance should be listed.   
You can connect to your instance with SSH via gcloud:
```
gcloud compute ssh myVM --zone us-central1-f
```
Press y to continue when prompted, and ENTER to leave the passphrase empty. To disconnect from SSH type ```exit``` to leave the remote shell.



## Install a NGINX web server
SSH into the VM and get root access ```sudo su -```  
As a root user, update OS with ``` apt-get update```  
Install NGINX: ```apt-get install nginx -y```  
Confirm that NGINX is running: ```ps auwx | grep nginx```  


[^1]:Google Cloud Skills Boost, [Creating a Virtual Machine](https://www.cloudskillsboost.google/focuses/3563?parent=catalog)
