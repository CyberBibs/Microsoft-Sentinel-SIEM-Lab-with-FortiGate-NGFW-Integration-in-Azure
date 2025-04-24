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

- Create a **Resource Group**: `biblab` in Canada Central region.
![Screenshot 2025-04-19 231135](https://github.com/user-attachments/assets/1dc6f81b-346a-4b1e-bcaa-a0756d7fd82b)

- Create a **Virtual Network**: `bibnet`.
![Screenshot 2025-04-19 232315](https://github.com/user-attachments/assets/a07769e8-8dd8-4c66-9bf4-cab4108ff75f)

- Enable **Azure Bastion**:
  - Hostname: `bibnet-Bastion`
  - Public IP: `bibnet-bastion-public-IP`
- Subnets:
  - `bibDMZ` (10.10.2.0/24)
  - `bibWAN` (10.10.4.0/24)
  - `AzureBastionSubnet` (10.10.9.0/24, manually edited)






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
