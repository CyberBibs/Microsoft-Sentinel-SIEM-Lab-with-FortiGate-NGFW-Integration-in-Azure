# Microsoft Sentinel SIEM Lab with FortiGate NGFW Integration in Azure

## Overview

This lab simulates a real-world cloud network security environment by deploying a FortiGate Next-Gen Firewall on Microsoft Azure, integrating it with Microsoft Sentinel SIEM for log collection, threat detection, and automated incident response.

I routed all security logs generated during these activities to Microsoft Sentinel. From there, I created alerts and carried out real-time incident responses, including analyzing threats and blocking malicious IP addresses. This project gives me practical experience in cloud security, threat monitoring, and automated response using industry-standard tools.

### Key Components
- Setting up core Azure components such as Resource Group, Virtual Networks (VNETs), subnets, Bastion Services and networking configurations.
- Creation of 2 Virtual Machines is accessible via secure bastion RDP connection.
- Deploying FortiGate firewall and configuring firewall rules
- Implementation of Intrusion Prevention Systems (IPS) using FortiGate for robust network security.
- Creation and deployment of a Log Analytics Workspace and connecting the FortiGate firewall to enable centralized and efficient log management.
- Connecting Microsoft Sentinel to Log Analytics Workspace.
- Utilization of Microsoft Sentinel to create alerts, perform incident response, and maintain the security and integrity of the network..

### Skills Learned

- Cybersecurity & Threat Detection
- Cloud Security & Networking (Azure)
- Security Information and Event Management (SIEM)
- System Administration & VM Management
- Query Language & Data Analytics

### Tools Used
- Microsoft Azure
- FortiGate Next-Generation Firewall (NGFW)
- Basion Service for secure RDP Desktop into the VMs.
- Microsoft Sentinel
- Kusto Query Language for log Analysis.
- Linux & Windows Virtual Machines

### Project Setup and Configuration Steps

### 1. Azure Environment Setup

The first step I took was creating  a **Resource Group**: `biblab` in Canada Central region. A resource group in Azure is a logical grouping of tools, services, configurations and more that exist under one banner so they can be created and deleted at the same time (they share the same lifespan). If I have resources outside of a particular resource group, if I delete that resource group the ones outside of it will still exist. It makes it easier to manage your resources if they're all in the same place. 
![Screenshot 2025-04-19 231135](https://github.com/user-attachments/assets/1dc6f81b-346a-4b1e-bcaa-a0756d7fd82b)

The next step is to create a Virtual networt (Vnet). A Vnet is basically fundamental building block in Microsoft Azure for your private network in the cloud. It allows many types of Azure resources—like virtual machines (VMs), databases, and application services—to securely communicate with each other, the internet, and on-premises networks.  I named my Vnet bibnet
![vnet](https://github.com/user-attachments/assets/b0635440-2c60-4c74-af07-df0f6d057245)

Under the security tab in the vnet creation process, I enabled Azure Bastion with Hostname: `bibnet-Bastion` & Public IP: `bibnet-bastion-public-IP`. Azure Bastion is a fully managed Platform-as-a-Service (PaaS) that provides secure and seamless RDP (Remote Desktop Protocol) and SSH (Secure Shell) access to your virtual machines directly through the Azure portal, without needing any public IP addresses on those VMs.
![vnet](https://github.com/user-attachments/assets/588aacab-287f-44e7-9581-ce859fc5efd5)

I created 2 subnets `bibDMZ` (10.10.2.0/24) acting our my internal network (LAN) & `bibWAN` (10.10.4.0/24) as our gateway network to the internet. A third network was created by default because as mentioned earlier I enabled Azure Bastion. However I edited the address block to my preffered choice `AzureBastionSubnet` (10.10.9.0/24, manually edited).

![Screenshot 2025-04-19 233647](https://github.com/user-attachments/assets/430d8c23-96cf-46b3-9a42-1f13b428f8bc)
![Screenshot 2025-04-19 234218](https://github.com/user-attachments/assets/01048956-4b8c-49e3-805a-e0feeba6112d)
![Screenshot 2025-04-19 235033](https://github.com/user-attachments/assets/2e5d74bc-09f7-478d-8e7a-be7a8f504425)
![Screenshot 2025-04-19 235248](https://github.com/user-attachments/assets/943e11af-85d1-40be-af33-a66b6e88ce48)

I waited to confirm the bibnet and all subnets have been created and successfully deployed. 
![Screenshot 2025-04-20 000358](https://github.com/user-attachments/assets/b27cf6a7-02e0-4d5d-93bc-d36230f955d9)

### 2. FORTINET FortiGate Firewall Deployment
Deployed Fortinet FortiGate NGFW: `bibFW`. Fortinet FortiGate NGFW stands for Next-Generation Firewall, and it's a powerful network security device developed by Fortinet. It's not just a traditional firewall it goes beyond basic packet filtering and includes advanced features to protect modern networks from evolving cyber threats.
![Screenshot 2025-04-20 001347](https://github.com/user-attachments/assets/ae8c280b-1690-4648-9c9a-c23b74c9d4ce)

During the creation of the firewall, I configured the gateway address for our bibDMZ and bibWAN by selecting the subnet I created in step 2 to the appropriate interfaces and selecting our bibnet virtual network. See screenshot below 
![Screenshot 2025-04-20 004713](https://github.com/user-attachments/assets/aa0fdb9e-b31e-4242-8d92-ce045cf0fdf3)

By default, under the public IP tab a default publicIP interface name is created, and I renamed it to  `bibFW-public`.  `bibFW-public` will be used for public services hosted on the FortiGate such as IPSEC termination, management of the FortiGate from external or services behind the FortiGate such as webserver. 
![Screenshot 2025-04-20 005523](https://github.com/user-attachments/assets/c4936ab6-d232-442e-bbe0-ae61fa37846b)

After successful deployment, a public IP and a DNS name we can use to connect to our bibFW NGFW was created.
![Screenshot 2025-04-20 010918](https://github.com/user-attachments/assets/c647875e-b3c5-4325-9cd3-d204918a3336)

I proceeded to sign in to `BibFW NGFW` by accessing the web application using the management IP address created from step 4 and entering the username and password I created earlier.
![Screenshot 2025-04-20 011727](https://github.com/user-attachments/assets/c39b0273-82fb-45cf-b893-d115ef3b5f0e)
![Screenshot 2025-04-20 011908](https://github.com/user-attachments/assets/372b678b-a04b-46da-84b7-dea22b37b3cc)

### 3. Firewall Configuration

I renamed the firewall interfaces by navigating to the network section on the hamburger menu and selecting the interface option. I allowed ssh and ping traffic for `bibDMZ` interface and for the `bibWAN` interface, I allowed https and ping traffic for management purposes. (Note: in an Enterprise environment, this is not recommended).
![Screenshot 2025-04-20 012903](https://github.com/user-attachments/assets/4c0930b2-bfae-473c-9217-9115e2f49d8c)
![Screenshot 2025-04-20 013217](https://github.com/user-attachments/assets/41ef0118-28f3-4dad-87b8-e9cd97516301)
Next, I confirmed the static routes configuration 
![Screenshot 2025-04-20 020429](https://github.com/user-attachments/assets/4e37bc29-8524-43ac-af20-eecc51f43f52)


### 4. Deploy Virtual Machines

In the next step, I added Windows10 and a Linux Virtual machine to the network. Adding both to our `bibDMZ` network whilst nmaking sure all inbound and outbound traffic passes through our `bibFW` NGFW firewall. 

I created a window 10 Virtual Machine named `bibWIN` 
![Screenshot 2025-04-20 022642](https://github.com/user-attachments/assets/881ab577-c240-436d-9ce3-c4bf88ca7b74)

I set up a username and password for the windows 10 machine and disabled RDP traffic available via public internet
![Screenshot 2025-04-20 022917](https://github.com/user-attachments/assets/339788bf-f6e2-4fb1-93e7-b441c8a8cb51)

Under the networking tab, I selected the bibnet we created as our virtual network, selected the `bibDMZ` subnet as our internal network, did not select and public IP address and disabled NIC network security group because I want all network security implemented via the `bibFW` firewall I created earlier.
![Screenshot 2025-04-20 023638](https://github.com/user-attachments/assets/5a1af4fb-4dc0-4c5a-9859-8e300b1d95f9)

The window10 virtual machine is successfully deployed
![Screenshot 2025-04-20 024232](https://github.com/user-attachments/assets/df9fd9af-c432-493c-9d66-91bd01890d01)

Next, I created a Linux Virtual Machine named `bibLIN`
![Screenshot 2025-04-20 024849](https://github.com/user-attachments/assets/fcf1aa1a-d490-43a8-b201-c8feff13f965)

I set up a username and password for the windows 10 machine and disabled RDP traffic available via public internet
![Screenshot 2025-04-20 025011](https://github.com/user-attachments/assets/ffc5a34e-a2d2-4423-8c8a-35b3e98a3310)

Under the networking tab, I selected the `bibnet` we created as our virtual network, selected the `bibDMZ` subnet as our internal network, did not select and public IP address and disabled NIC network security group because I want all network security implemented via the `bibFW` firewall I created earlier.
![Screenshot 2025-04-20 025354](https://github.com/user-attachments/assets/faa2b698-28db-4e04-8ae7-8330fe65d948)

The Linux virtual machine is successfully deployed
![Screenshot 2025-04-20 025823](https://github.com/user-attachments/assets/2c77a324-0822-4ebc-b383-90b2b8de1cab)

### 5. Custom Route Tables

I created two (2) route tables to override Azure’s default routing. This allows us route traffic to `bibFW` firewall we created earlier. An Azure Route Table (also known as a User-Defined Route (UDR)) is a networking feature in Microsoft Azure that allows you to control the routing of network traffic in your virtual network (VNet).

I created a route table and named it `bibDMZ-RouteTable` 
![Screenshot 2025-04-20 031416](https://github.com/user-attachments/assets/49a25402-4942-462b-a3d4-61510dd66360)


I created `ToTheInternet` route and added it to the routing table.
![Screenshot 2025-04-20 033532](https://github.com/user-attachments/assets/64f0f3a7-2548-4dba-8123-57b799662bdc)

I created a route table and named it `bibWAN-RouteTable`
![Screenshot 2025-04-20 035651](https://github.com/user-attachments/assets/d5a7b728-c8c7-4412-972f-cf4dd1fe1877)

I created `ToDMZ` route and added it to the routing table.
![Screenshot 2025-04-20 041212](https://github.com/user-attachments/assets/52f50d7c-1a5e-450e-8194-406719307ede)

Next I associated `bibDMZ` to the `ToTheInternet` `bibWAN` subnet to the `ToDMZ` route. This allows all network traffic from `bibDMZ` subnet to pass through the firwall interface with ip 10.10.4.4.
![Screenshot 2025-04-20 041642](https://github.com/user-attachments/assets/c2b93115-4056-4a9f-8ee0-fd196ae4c12e)
![Screenshot 2025-04-20 041758](https://github.com/user-attachments/assets/71ce5774-e4ad-480a-b866-d55169a64fea)

### 6. Network Connectivity Tests

To test and troubleshoot network connectivity. I performed the following steps. I signed into `bibWIN` via Bastion with the username and password I created when deploying the windows 10 virtual machine.
![Screenshot 2025-04-20 044057](https://github.com/user-attachments/assets/2aec46d2-8e02-418d-8519-2bfa107f904e)

Upon successful signing in to `bibWIN`, I opened command prompt and was able to successfully ping `bibLIN` with its ip address 10.10.2.6. 
![Screenshot 2025-04-20 045129](https://github.com/user-attachments/assets/1663f0b0-8f46-4323-9bc1-a8546648ebc8)

However, I was unsuccessful when trying to test internet connectivity by pinging google at 8.8.8.8 with the request timed out
![Screenshot 2025-04-20 045427](https://github.com/user-attachments/assets/33c88228-35c2-4be3-8fc5-92c3ccf744fe)

To fix this, I had to do some troubleshooting, I signed into `bibFW` and checked the firewall policy and discovered all network traffic are explicitly denied by default. As a result, I was not able to access the internet from `bibWIN` machine.
![Screenshot 2025-04-20 045803](https://github.com/user-attachments/assets/23c1546a-8aea-4cc7-bdb6-bcf03d1c5551)

To resolve the connectivity issue, I created a new firewall policy(`DMZtoTheInternet`) on `bibFW` to allow all ping, https, smtp, and dns network traffic from all devices connected to `bibDMZ` subnet
![Screenshot 2025-04-20 050725](https://github.com/user-attachments/assets/341e718e-76ca-4fde-8472-96f115dd31b2)
![Screenshot 2025-04-20 051112](https://github.com/user-attachments/assets/dafd0b1f-1f8e-4a1c-9eee-856195184426)

Upon creating the `DMZtoTheInternet` above, I was able to ping 8.8.8.8. This proves internet connectivity.
![Picture2](https://github.com/user-attachments/assets/c565503f-1cf5-4153-a240-2ce483e366cf)

The above step proves outbound connectivity to the internet from `bibDMZ` subnet.

Next, I created the path for inbound connectivity from the Internet to `bibDMZ` subnet . I configures NAT on `bibFW` firewall by creating a virtual IP named Window-NAT-RDP. I enabled port forwarding and mapped the external IP address to a my `bibWIN` IP address
![Screenshot 2025-04-20 090450](https://github.com/user-attachments/assets/c36b9247-408f-4e1e-9e66-88859b155d84)

I then created a firewall policy on `bibFW` called `InternetTobibWinRDP` to allow only RDP traffic from the internet to `bibWIN` via the firewall
![Screenshot 2025-04-20 091651](https://github.com/user-attachments/assets/bb2a2574-f77e-40e9-8c8b-0e1d745457ad)
![Screenshot 2025-04-20 091844](https://github.com/user-attachments/assets/51839f73-edd6-4837-a841-f065dab67370)

To test the RDP connection from the Internet to `bibWIN`, I disabled the firewall in `bibWIN`, allowed all traffic in `bibWIN` by changing RDP settings in  `bibWIN`  and proceeded to using RDP from my personal computer to `bibWin` hosted in azure
![Screenshot 2025-04-20 093118](https://github.com/user-attachments/assets/81a413b9-bfa2-444c-baf8-926f4fbd28d7)
![Screenshot 2025-04-20 093250](https://github.com/user-attachments/assets/75eaba5b-7204-4614-b6c6-c228bfed7a59)

After disabling `bibWIN` firewall, I was able to connect. This proves RDP traffic from the Internet to bibWIN is good and working as expected as I was able to RDP from my personal computer
![Screenshot 2025-04-20 094006](https://github.com/user-attachments/assets/8fbe4334-23df-48b3-8b56-fab0b4500088)


### 7. Intrusion Prevention System (IPS)

An Intrusion Prevention System (IPS) is a network security tool that monitors network traffic to detect and prevent identified threats.




