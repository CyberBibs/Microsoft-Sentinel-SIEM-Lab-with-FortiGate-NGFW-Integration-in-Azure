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









1. OWAS ZAP: Used OWASP ZAP, an open-source web application security scanner, to conduct automated vulnerability scans on the http://localhost/clientdetailsystem. We Configured ZAP to perform comprehensive scans, including both passive and active scanning, then reviewed the scan results to identify and prioritize potential security vulnerabilities detected by OWASP ZAP. As shown in Proof of Concept, BLIND SQL injection and UNION Query SQL inject were identified as critical vulnerability.  
![Picture2](https://github.com/user-attachments/assets/8c621698-fb08-40e9-bbab-f3403fe1b5df)

2. Burp Suite: we use this to carry out manual tests and also capture HTTP Post Request Header. Extracted payloads and attack vectors identified by OWASP ZAP for manual verification, we then Utilized Burp Suite's Repeater tool to replay requests and manipulate parameters with the identified payloads by Conducting targeted manual testing, including input validation bypass, XSS, and other injection attacks, to validate and further exploit identified vulnerabilities.
![Picture3](https://github.com/user-attachments/assets/6f51cdcb-d689-422a-aaf0-888ef4dcb94d)

3. SQLMAP: used for detecting and performing SQL injection vulnerability present in the Client Detail System. The steps perform using SQLMap are descriped below; <br>
   i. Capture and Save HTTP POST Request Header: We initiated the testing process by          capturing the HTTP POST request header generated from the login request with            invalid username and password inputs within the Client Details System (CDS).            This interception was conducted using Burp Suite. The captured request,                 containing the attempted exploit, was then saved as a text file named request.txt       for further analysis and exploitation.
 ii.  Enumerate Databases and Discover Injection Point: Utilizing the saved HTTP              request file, SQLMap was employed to scan the Client Details System for SQL             injection vulnerabilities. Command executed was:
   sqlmap.py -r C:\Users\musta\OneDrive\Desktop\request.txt --dbs --flush     session.
                            Output
   ![Picture4](https://github.com/user-attachments/assets/d481269c-870d-4319-b7cf-2becb942f468)




Every screenshot should have some text explaining what the screenshot is about.

Example below.

*Ref 1: Network Diagram*
