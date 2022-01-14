# Regions and Zones
further information on regions and zones in GCP

## Regions
Different Google Compute Resources exist across different locations around the world.  
A region is a sprecific geographical location where compute resources can be accessed through GCP.  

## Zones
Each Region has at least one or more zones.  
For example, in Eastern Asia Region 'asia-east1', there exists three zones 'asia-east1-a','asia-east1-b'and 'asia-eeast1-c'.  
Resources in these zones are referenced as zonal resources. An example could be a virtual machine instance.  
Zonal resources must be located in the same zone.

To view your default region and zone:
```
gcloud config get-value copmpute/zone
gcloud config get-value copmpute/region
```

You can save values to environment variables. This can be demonstrated with zone:
```
export ZONE=<your_zone_here>
```
and verify the variable was set correct with ```echo $ZONE```

Use this new variable setting up a VM instance:
```
gcloud compute instances create myVM --machine-type n1-standard-2 --zone $ZONE
```
