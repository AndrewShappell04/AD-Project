# AD-Project
**Project Description:** This project was designed to simulate a small enterprise network constructed via Active Directory using Oracle VirtualBox. The project consists of four virtual machines: a Windows 10 workstation, a Kali Linux machine, a Windows Server, and an Ubuntu Server. Each machine serves a distinct purpose and is configured to communicate with the others through a single NAT network. The network layout is shown in the image below, along with each machine’s IP address and installed software. As part of this project, the Ubuntu Server is configured as a Splunk Server for centralized log management. Both the Windows 10 machine and the Windows Server have the Splunk Universal Forwarder installed, which transmits log data to the Splunk Server, as represented by the dotted green line.

<img width="800" height="650" alt="image" src="https://github.com/user-attachments/assets/c4aa0019-03e4-4611-bc9f-e5c50772faad" /><br>

**Purpose:** The purpose of this project is to gain hands-on experience setting up and configuring an Active Directory environment with a focus on security and detection. Each of the VMs serve their own role within the enviroment: Windows 10 Machine simulates user activity and authentication, Active Directory Server manages the domain and user accounts, Kali Linux machine creates the security events, and the Splunk Server monitors the domain for malicious or abnormal behavior. Working with these technologies and understanding their roles is the main learning outcome of this project. 

---
**Step 1: Configure the Network**<br>
After installing the virtual machines and allocating the required hardware for each, a NAT network was configured to place all the machines on the same network. To do this, I went to Tools > Network > NAT Network > Create. I named the NAT Network "AD-Project" with the network IP Address 192.168.10.0/24.<br>

The /24 in the CIDR notation indicates that the first 24 bits of the IP address are the network portion, which means the subnet mask is 255.255.255.0. This allows for 256 total IP addresses in the subnet, with 254 usable host addresses (since the first address is reserved as the network address and the last as the broadcast address).<br>

From there, I navigated to each VM > Settings > Network, changed it to "Attached to: NAT", and changed to the network I just created (AD-Project).<br>

---

**Step 2: Set up Splunk Server**<br>
I booted up Ubuntu server and changed the IP address to match my diagram. Everything in linux is either a file or a folder, so I navigated into the network configuration file using the command "sudo nano /etc/netplan/50-cloud-init.yaml". I modified the file by configuring a static IP address of 192.168.10.10, changing the DNS to 8.8.8.8 (Google's DNS server), and changing the default gateway to 192.168.10.1 (My network's default gateway).<br>
<img width="650" height="500" alt="Screenshot 2025-12-23 164534" src="https://github.com/user-attachments/assets/8c08e92d-d413-4cd0-8d59-b52516efc519" /><br>

Following this, I needed to install Splunk on my Ubuntu Server, so I first downloaded the Linux version of Splunk Enterprise on my host machine. Next, I installed the necessary VirtualBox guest utilities on the Ubuntu Server to ensure proper integration between the host and the virtual machine. After that, I created a shared folder in VirtualBox so the Splunk installation files could be accessed from within the server. Once the shared folder was mounted and verified, I navigated to it from the Ubuntu Server and installed Splunk using the package manager. After installation, I started Splunk, completed the initial setup, and configured it to run automatically at boot using my dedicated Splunk user. This ensured that whenever the Ubuntu Server was started and I logged in with my user account, Splunk would automatically start as well.<br>
<table>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/af42a0e2-4fe5-4a94-b3d5-2afa8f36260a"
           alt="Screenshot 2026-01-07 130027"
           width="450">
    </td>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/a12701b7-7560-478a-8a1a-25ce7dd7fc53"
           alt="Screenshot 2025-12-28 133838"
           width="450">
    </td>
  </tr>
</table> <br>

**Purpose of Splunk:** The Splunk Server will be used to collect and analyze security-relevant data from both the Windows 10 machine and the Active Directory server. This data can be used to detect and monitor the devices to ensure that no malicious activity has occurred. Some of the data that might collected include user authentication activity such as successful and failed logon attempts, logouts, account lockouts, and repeated login failures. Monitoring this information helps give insight into when and how users are accessing systems, helps identify unauthorized access attempts or brute-force behavior, and supports auditing of normal versus abnormal login patterns across all devices. 


---

**Step 3: Windows 10 Machine and AD Server Splunk Set Up** <br />
For the context of this project started by changing the Windows 10 machine to be named "target-PC" because it will eventually be exposed by my Kali Linux machine. I then changed the network settings on the machine to match my network, so that I could now access my newly set up Splunk Server.
<table>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/36f8d4ff-8f46-4500-8a53-0ee54dfd67d4"
           alt="Screenshot 2025-12-30 193256"
           width="450">
    </td>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/ec142504-6fd6-4cb8-9ede-4dd91d120062"
           alt="Screenshot 2025-12-28 143050"
           width="450">
    </td>
  </tr>
</table> <br>
First, I installed the Splunk Universal Forwarder from the Splunk website, then I installed Sysmon which was much less straightforward. To install Sysmon, I downloaded the software from https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon, and extracted the raw data of the sysmonconfig.xml from https://github.com/olafhartong/sysmon-modular. Finally, I copied the URL of the extracted directory and ran Sysmon in Powershell. <br />
<img width="650" height="500" alt="Screenshot 2025-12-28 145430" src="https://github.com/user-attachments/assets/718f5f9d-4590-46a4-bc0e-122b6ebcef77" /> <br />

<br>

Sysmon and Splunk work in conjuction with each other to provide information about system activity by collecting, centralizing, and analyzing detailed event data. Sysmon generates in-depth logs related to process creation, network connections, file changes, and registry modifications, while Splunk ingests and correlates this data across multiple systems. By adding another inputs.conf file into Local Disk (C:) > Program Files > SplunkUniversalForwarder > etc > system > local, I can define what Sysmon event logs I want to be collected, how to collect them, and where to send them in Splunk. In the image below, it can be seen that I configured the inputs.conf file to collect multiple Windows Event Logs, including the Application, Security, and System logs, as well as the Microsoft-Windows-Sysmon/Operational log. Each of these inputs is enabled and set to send events to the endpoint index in Splunk. The file was originally a txt file created in Notepad, but was converted into a configuration file before being added to the correct directory. <br>
<img width="650" height="500" alt="Screenshot 2025-12-28 150235" src="https://github.com/user-attachments/assets/bf60609d-30da-4bdc-a41c-1e03f3ee8486" /> <br>

Anytime the inputs.conf file is updated, the Splunk Universal forwarder service must be restarted in the "Services" application on Windows, so I made sure to do that as well. I then navigated to Splunk’s Search and Reporting application and ran a search for index=endpoint, which is the index configured to receive the forwarded event logs. I confirmed that my Windows 10 machine was there, and subsequently repeated the steps for the Active Directory Server. In the image below, it can be seen that there is two hosts, one being "target-PC" (Windows 10 machine), and the other being "ADDC01" (Active Directory Server). <br>
<img width="700" height="650" alt="Screenshot 2026-01-07 191919" src="https://github.com/user-attachments/assets/ff984241-ea82-40cd-b2e5-d1a8f400c225" /> <br>

---

**Step 4: Active Directory Domain Controller** <br>
Similarly to the other VMs, the network settings needed to be configured first to match my network diagram. So, I configured a static IP address of 192.168.10.7, default gateway of 192.168.10.1, and DNS server of 8.8.8.8. After that, I navigated to the Server Manager application that comes installed with Windows Server, and selected Manage > Add Roles and Features. I went through the steps of installing Active Directory Domain Services. I proceeded to promote the AD Server to a Domain Controller. <br> 
<table>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/33621621-e284-44e6-85b4-ce6cb0dc16fc"
           alt="Screenshot 2026-01-08 164353"
           width="450">
    </td>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/cc634825-e2d7-4568-8e98-76dd8f20fac0"
           alt="Screenshot 2026-01-08 164938"
           width="450">
    </td>
  </tr>
</table> <br>

Now that the server was set up and registered as a domain controller, I created a couple of organizational units (OUs) and add users to them. Organizational units are used to break up objects like users, and computers into specific groups based on relation. This allows adminintsrators to apply specific Group Policies (GPOs) to certain sets of users or computers rather than the entire domain. By organizing objects into OUs, administrators can more easily manage permissions, enforce security settings, and apply policies tailored to different roles or departments. In this project, the two OUs I created were IT and HR, with the user Jenny Smith in IT and Terry Smith in HR. 
<table>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/91bb96d4-ad64-424d-9dda-9e0d945e014f"
           alt="Screenshot 2026-01-08 164353"
           width="450">
    </td>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/9e7e6617-f191-4ef9-acb8-7c242e6763a3"
           alt="Screenshot 2026-01-08 164938"
           width="450">
    </td>
  </tr>
</table> <br>

With the two user being created, I now needed to join the domain on my Windows 10 machine. However, when I tried joining the domain, I encountered an error saying my domain couldn't be contacted. To resolve this issue, I changed the DNS server from 8.8.8.8 to 192.168.10.7 which is the IP address of my newly created Active Directory Domain Controller. Once that was done, I was able to connect to my domain (ashap.local). The words "Sign in to: ASHAP" in the right image confirm that. 
<table>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/66384c55-8c92-44a3-843c-595d597808d9"
           alt="Screenshot 2026-01-08 164353"
           width="450">
    </td>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/f9ac3768-9d4d-4de2-afef-3b21776a7af3"
           alt="Screenshot 2026-01-08 164938"
           width="450">
    </td>
  </tr>
</table>

---

**Step 5: Kali Linux and Atomic Red Team** <br>

As with the other virtual machines, the first thing that needs to be done is to configure Kali's network settings. This VM will have a static IP address of 192.168.10.250, default gateway of 192.168.10.1, and DNS server of 8.8.8.8. I made sure to update and upgrade my repositories to ensure the system had the latest packages, security patches, and dependency updates before proceeding as weell. 

**Attack Set Up:**







