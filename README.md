# AD-Project
**Project Description:** This project was designed to simulate a small enterprise network constructed via Active Directory using Oracle VirtualBox. The project consists of four virtual machines: a Windows 10 workstation, a Kali Linux machine, a Windows Server, and an Ubuntu Server. Each machine serves a distinct purpose and is configured to communicate with the others through a single NAT network. The network layout is shown in the image below, along with each machine’s IP address and installed software. As part of this project, the Ubuntu Server is configured as a Splunk Server for centralized log management. Both the Windows 10 machine and the Windows Server have the Splunk Universal Forwarder installed, which transmits log data to the Splunk Server, as represented by the dotted green line.

<img width="800" height="650" alt="image" src="https://github.com/user-attachments/assets/c4aa0019-03e4-4611-bc9f-e5c50772faad" /><br>

**Purpose:** The purpose of this project is to gain hands-on experience setting up and configuring an Active Directory environment with a focus on security and detection. Each of the VMs serve their own role within the enviroment: Windows 10 Machine simulates user activity and authentication, Active Directory Server manages the domain and user accounts, Kali Linux machine creates the security events, and the Splunk Server monitors the domain for malicious or abnormal behavior. Working with these technologies and understanding their roles is the main learning outcome of this project. 

---
**Step 1 Configure the Network**<br>
After installing the virtual machines and allocating the required hardware for each, a NAT network was configured to place all the machines on the same network. To do this, I went to Tools > Network > NAT Network > Create. I named the NAT Network "AD-Project" with the network IP Address 192.168.10.0/24.<br>

The /24 in the CIDR notation indicates that the first 24 bits of the IP address are the network portion, which means the subnet mask is 255.255.255.0. This allows for 256 total IP addresses in the subnet, with 254 usable host addresses (since the first address is reserved as the network address and the last as the broadcast address).<br>

From there, I navigated to each VM > Settings > Network, changed it to "Attached to: NAT", and changed to the network I just created (AD-Project).<br>

---

**Step 2 Set up Splunk Server**<br>
I booted up Ubuntu server and changed the IP address to match my diagram. Everything in linux is either a file or a folder, so I navigated into the network configuration file using the command "sudo nano /etc/netplan/50-cloud-init.yaml". I modified the file by changing the IP address to 192.168.10.10, changing the DNS to 8.8.8.8 (Google's DNS server), and changing the default gateway to 192.168.10.1 (My network's default gateway).<br>
<img width="650" height="500" alt="Screenshot 2025-12-23 164534" src="https://github.com/user-attachments/assets/8c08e92d-d413-4cd0-8d59-b52516efc519" /><br>

To Install Splunk, on my host machine I went to the Splunk website and installed the Linux version of Splunk Enterprise. 
