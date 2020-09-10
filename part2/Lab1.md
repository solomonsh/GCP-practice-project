# Virtual Private Networks (VPN)
## Objectives
    > In this lab, you learn how to perform the following tasks:

    > Create VPN gateways in each network

    > Create VPN tunnels between the gateways

    > Verify VPN connectivity

### Steps 
#### Task 1 Explore the networks and instances
1.1 Verify that vpn-network-1 and vpn-network-2 have been created with subnets in separate regions.

    list networks
    gcloud compute networks list

    list subnets of network vpn-network-1
    gcloud compute networks subnets list   --network=vpn-network-1


    list subnets of network vpn-network-2
    gcloud compute networks subnets list   --network=vpn-network-2


1.2 Explore the firewall rules

    list firewall of network vpn-network-1
     gcloud compute firewall-rules list --filter network=vpn-network-1
    
    list firewall of network vpn-network-2
      gcloud compute firewall-rules list --filter network=vpn-network-2

1.3 Explore the instances and their connectivity

    list VM instances
    gcloud compute instances list

    For server-1, click SSH to launch a terminal and connect.
        gcloud compute ssh --project qwiklabs-gcp-02-b18c42de434c  --zone us-central1-b server-1

    To test connectivity to server-2's external IP address, run the following command, replacing server-2's external IP address with the value noted earlier:
        ping -c 3  34.123.229.88 
    
    To test connectivity to server-2's internal IP address, run the following command, replacing server-2's internal IP address with the value noted earlier:
        ping -c 3   10.5.4.2 
    
    Exit the SSH terminal.
        exit

#### Task 2: Create the VPN gateways and tunnels
2.1 Reserve two static IP addresses

    gcloud compute addresses create vpn-1-static-ip   --region us-central1

    gcloud compute addresses create vpn-2-static-ip   --region europe-west1
 
2.2 create gateway and tunnel

    Create the vpn-1 gateway and tunnel1to2

    gcloud compute --project "qwiklabs-gcp-02-b18c42de434c" target-vpn-gateways create "vpn-1" --region "us-central1" --network "vpn-network-1"

    gcloud compute --project "qwiklabs-gcp-02-b18c42de434c" forwarding-rules create "vpn-1-rule-esp" --region "us-central1" --address "35.188.176.67" --ip-protocol "ESP" --target-vpn-gateway "vpn-1"

    gcloud compute --project "qwiklabs-gcp-02-b18c42de434c" forwarding-rules create "vpn-1-rule-udp500" --region "us-central1" --address "35.188.176.67" --ip-protocol "UDP" --ports "500" --target-vpn-gateway "vpn-1"

    gcloud compute --project "qwiklabs-gcp-02-b18c42de434c" forwarding-rules create "vpn-1-rule-udp4500" --region "us-central1" --address "35.188.176.67" --ip-protocol "UDP" --ports "4500" --target-vpn-gateway "vpn-1"

    gcloud compute --project "qwiklabs-gcp-02-b18c42de434c" vpn-tunnels create "tunnel1to2" --region "us-central1" --peer-address "34.77.139.171" --shared-secret "gcprocks" --ike-version "2" --local-traffic-selector "0.0.0.0/0" --target-vpn-gateway "vpn-1"

    gcloud compute --project "qwiklabs-gcp-02-b18c42de434c" routes create "tunnel1to2-route-1" --network "vpn-network-1" --next-hop-vpn-tunnel "tunnel1to2" --next-hop-vpn-tunnel-region "us-central1" --destination-range "10.1.3.0/24"


    Create the vpn-2 gateway and tunnel2to1
    gcloud compute --project "qwiklabs-gcp-02-b18c42de434c" target-vpn-gateways create "vpn-2" --region "europe-west1" --network "vpn-network-2"

    gcloud compute --project "qwiklabs-gcp-02-b18c42de434c" forwarding-rules create "vpn-2-rule-esp" --region "europe-west1" --address "34.77.139.171" --ip-protocol "ESP" --target-vpn-gateway "vpn-2"

    gcloud compute --project "qwiklabs-gcp-02-b18c42de434c" forwarding-rules create "vpn-2-rule-udp500" --region "europe-west1" --address "34.77.139.171" --ip-protocol "UDP" --ports "500" --target-vpn-gateway "vpn-2"

    gcloud compute --project "qwiklabs-gcp-02-b18c42de434c" forwarding-rules create "vpn-2-rule-udp4500" --region "europe-west1" --address "34.77.139.171" --ip-protocol "UDP" --ports "4500" --target-vpn-gateway "vpn-2"

    gcloud compute --project "qwiklabs-gcp-02-b18c42de434c" vpn-tunnels create "vpn-2-tunnel-1" --region "europe-west1" --peer-address "35.188.176.67" --shared-secret "gcprocks" --ike-version "2" --local-traffic-selector "0.0.0.0/0" --target-vpn-gateway "vpn-2"

    gcloud compute --project "qwiklabs-gcp-02-b18c42de434c" routes create "vpn-2-tunnel-1-route-1" --network "vpn-network-2" --next-hop-vpn-tunnel "vpn-2-tunnel-1" --next-hop-vpn-tunnel-region "europe-west1" --destination-range "10.5.4.0/24"


#### Task 3: Verify VPN connectivity
3.1 Verify server-1 to server-2 connectivity
    gcloud compute ssh --project qwiklabs-gcp-02-b18c42de434c  --zone us-central1-b server-1
    
    To test connectivity to server-2's internal IP address
        ping -c 3  10.1.3.2
    exit
  
    To test connectivity to server-1's internal IP address,
        gcloud compute ssh --project qwiklabs-gcp-02-b18c42de434c  --zone europe-west1-b server-2
        ping -c 3 10.5.4.2
    exit

 
  