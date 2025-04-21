# Cloud SIEM Implementation and Log Analysis

## Overview

In this hands-on lab, I deployed a FortiGate Next-Generation Firewall within Microsoft Azure to build a realistic cloud network with publicly accessible resources. I simulated brute-force attacks and performed network scans to test the firewall’s threat detection and response capabilities.

I routed all security logs generated during these activities to Microsoft Sentinel. From there, I created alerts and carried out real-time incident responses, including analyzing threats and blocking malicious IP addresses. This project gives me practical experience in cloud security, threat monitoring, and automated response using industry-standard tools.

### Key Components
- Setting up core Azure components such as Resource Group, Virtual Networks (VNETs),      subnets, Bastion Services and networking configurations.
- Creation of 2 Virtual Machines is accessible via secure bastion RDP connection.
- Deploying FortiGate firewall and configuring firewall rules
- Implementation of Intrusion Prevention Systems (IPS) using FortiGate for robust         network security.
- Creation and deployment of a Log Analytics Workspace and connecting the FortiGate       firewall to enable centralized and efficient log management.
- Connecting Microsoft Sentinel to Log Analytics Workspace.
- Utilization of Microsoft Sentinel to create alerts, perform incident response, and      maintain the security and integrity of the network..

### Skills Learned

- Security Assessment & Analysis.
- Penetration Testing Skills.
- Secure Coding & Remediation.
- Compliance & Risk Management.

### Tools Used
- SonarQube : for carrying out static analysis of our code.
- Burp Suite: use to carry out manual tests and also capture HTTP Post Request Header.
- OWASP ZAP: to conduct automated vulnerability scans on the web application.
- SQLMap: for detecting and performing SQL injection vulnerability present in the Client Detail System.

### About the System
The Client Details System (version 1.0) is a web-based application built using PHP, CSS, Bootstrap, and JavaScript. It serves as a centralized platform for managing client information, providing both user and administrator panels. The user panel facilitates the entry and retrieval of client details, while the administrator panel empowers system management and user administration.

### Threat Considered
Due to the vulnerability found in our assessment of the Client Details System 1.0, we discovered that the “uemail” parameter allows SQL Injection. We exploited this vulnerability and compromised the application, thereby able to access user data (username and password) and the underlying database information CVE-2023-7137.     

### Attack scenario
For this vulnerability assessment, the attack scenarios considered include firstly, an attacker exploiting vulnerabilities in input validation mechanisms of the Client Details System to inject malicious SQL queries into user input fields and potentially gaining unauthorized access to the database and executing arbitrary commands. Secondly, the attacker can also bypass authentication mechanisms to gain unauthorized access to privileged functionalities within the system. Thirdly, attackers can also use automated tools to attempt and guess admin credentials to gain uncontrolled access.

### Steps
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
