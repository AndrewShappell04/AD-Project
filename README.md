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

**Step 3: Windows 10 Machine and AD Server Splunk Set Up**<br>
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
</table>
First, I installed the Splunk Universal Forwarder from the Splunk website, then I installed Sysmon which was much less straightforward. To install Sysmon, I downloaded the software from https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon, and extracted the raw data of the sysmonconfig.xml from https://github.com/olafhartong/sysmon-modular. Finally, I copied the URL of the extracted directory and ran Sysmon in Powershell. <br />
<img width="650" height="500" alt="Screenshot 2025-12-28 145430" src="https://github.com/user-attachments/assets/718f5f9d-4590-46a4-bc0e-122b6ebcef77" /> <br />




