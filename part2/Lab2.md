# Creating Virtual Machines
## Objectives
    In this lab, you explore the available options for VMs and see the differences between locations.

    In this lab, you learn how to perform the following tasks:

        > Create several standard VMs

        > Create advanced VMs


### Task 1: Create a utility virtual machine
#### Create a VM
    gcloud compute instances create myvm-instance --zone=us-central1-c --machine-type=n1-standard-1 --subnet=default --no-address  --image=debian-10-buster-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=myvm-instance --no-shielded-secure-boot  

#### Explore the VM details
    gcloud compute instances describe  myvm-instance 


### Task 2: Create a Windows virtual machine
    Create a Windows virtual machine
    gcloud  compute  instances create my-window-vm --zone=europe-west2-a --machine-type=n1-standard-2 --subnet=default  --tags=http-server,https-server --image=windows-server-2016-dc-core-v20200813 --image-project=windows-cloud --boot-disk-size=100GB --boot-disk-type=pd-ssd --boot-disk-device-name=my-window-vm --no-shielded-secure-boot 

    Create a firewall rule to allow http requests 
    gcloud compute firewall-rules create default-allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server

    Create a firewall rule to allow https requests 
    gcloud compute --project=qwiklabs-gcp-03-50b7d91028bc firewall-rules create default-allow-https --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:443 --source-ranges=0.0.0.0/0 --target-tags=https-server

### Task 3: Create a custom virtual machine
    gcloud beta compute --project=qwiklabs-gcp-03-50b7d91028bc instances create my-custom-vm --zone=us-west1-b --machine-type=e2-custom-6-16384 --subnet=default --image=debian-10-buster-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=my-custom-vm  
