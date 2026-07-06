# AD-Project
**Project Description:** This project was designed to simulate a small enterprise network constructed via Active Directory using Oracle VirtualBox. The project consists of four virtual machines: a Windows 10 workstation, a Kali Linux machine, a Windows Server, and an Ubuntu Server. Each machine serves a distinct purpose and is configured to communicate with the others through a single NAT network. The network layout is shown in the image below, along with each machine’s IP address and installed software. As part of this project, the Ubuntu Server is configured as a Splunk Server for centralized log management. Both the Windows 10 machine and the Windows Server have the Splunk Universal Forwarder installed, which transmits log data to the Splunk Server, as represented by the dotted green line.

<img width="800" height="650" alt="image" src="https://github.com/user-attachments/assets/c4aa0019-03e4-4611-bc9f-e5c50772faad" /><br>

**Purpose:** The purpose of this project is to gain hands-on experience setting up and configuring an Active Directory environment with a focus on security and detection. Each of the VMs serve their own role within the enviroment: Windows 10 Machine simulates user activity and authentication, Active Directory Server manages the domain and user accounts, Kali Linux machine creates the security events, and the Splunk Server monitors the domain for malicious or abnormal behavior. Working with these technologies and understanding their roles is the main learning outcome of this project. 

This project was inspired by a hands-on lab from the MyDFIR YouTube channel and was independently completed by myself to gain more experience with threat simulation, detection, and log analysis using industry-relevant tools. 










