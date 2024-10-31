<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />



<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Resource Group Creation
- Virtual Machine Creation
- Remote Desktop Protocol (RDP)
- Wireshark Installation 

<h2>Actions and Observations</h2>

<p>

![image](https://github.com/user-attachments/assets/307e3542-db1a-4ecb-b700-f806676442eb)

</p>
<p>
In this project we will be using a Windows 10 virtual machine and a Linux virtual machine. We will begin by creating a resource group in Microsoft Azure and labeling it with a name and selection a region. 
After this has been completed, navigate to the virtual machine dashboard in azure and create a new virtual machine in the same resource group and region. We will be creating two virtual machines, we will start with the 1st one being the Windows 10 virtual machine. The image will be a Windows 10 Pro, version 22h2 - x64 Gen2. 

Next we will create the Linux virtual machine. This will be in the same resource group as the Windows virtual machine. The image will be a Ubuntu Server 22.04 LTS - x64 Gen2. For this lab, the authetnication type has been set as password. The same credential login info for both virtual machines will be the same. 

We will connect into the Windows 10 virtual machine first by using Remote Desktop Protocol (RDP). Obtain the public IP address for the Windows 10 virtual machine > open up RDP > paste the public IP address into RDP with the login information to connect.
</p>
<br />

<p>

![image](https://github.com/user-attachments/assets/b6a41d69-f81c-4ba1-8e55-2994edf8e676)

</p>
<p>
In the Windows virtual machine we will be installing Wireshark. Click on Microsoft Edge and type in https://www.wireshark.org and click Download. We will be using the Windows x64 Installer. Throughout the installation process everything will be kept as the default settings. After downloading Wireshark you can open it up from the Start menu. 
</p>
<br />

<p>

![image](https://github.com/user-attachments/assets/e0397f90-aca0-4096-b603-1cefb9a2b831)

</p>
<p>
We will be ovserving ICMP (Internet Control Message Protocol) which is used to test connectiivty between two devices. In Wireshark we will be able to see all the traffic that is being generated. After opening up Wireshark, click on Ethernet then on the blue shark fin in the upper left corner. This will start the capturing packet process from Ethernet. In the photo example you will be able to see all the network traffic that's occurring on the Windows virtual machine. 
<br />

<p>

![image](https://github.com/user-attachments/assets/b5842986-bb7b-4caa-af93-91cbab1542dd)

</p>
<p>
We will now test for ICMP traffic. We will first filter for ICMP traffic by clicking on "Apply a display filter" in the search bar > type icmp and hit enter. This filters for ICMP traffic only.
<br />

<p>

![image](https://github.com/user-attachments/assets/2064900b-22bf-4770-895f-e965dd255d43)

</p>
<p>
For this next step we will take the Linux private IP address and ping it from within the Windows Virtual machine. THe private IP address can be found by clicking on the Linux virtual machine in Azure in the Overview tab in the Networking section. After retrieving the Linux private IP address, open up Windows Powershell and type in ping <private IP address> (In this example I ping the private ip address [ping 10.0.0.5] ). This will test the connectivity between the two devices. You will observe that when the Linux private IP address has been pinged, traffic populates in Wireshark in the ICMP filter. ICMP is filtered so that it's organized with the results. Wireshark showcases the requets and replies from the Windows virtual machine to the Linux virtual machine.

We will do a ping that loops in PowerShell. Type in PowerShell "ping <private IP address> -t) (Example: ping 10.0.0.5 -t"). This will generate constant pinging which we will use to observe in the next step.
  <br />

<p>

![image](https://github.com/user-attachments/assets/6531c2b9-6cba-4587-a119-3431bbb54fd0)

</p>
<p>
In this step we will open up the Network Security Group in Azure and disable incoming traffic in the Linux virtual machine. The Network Security Group is the Azure version of a firewall. This will be disabled y configuring the network security group. Navigate back to the Azure portal > Virtual Machines > Linux virtual machine. Navigate to the Networking tab > Network settings and click on Network security group: in the rgiht corner. After you have clicked on that click on settings in the left hand corner. Click Inbound security rules and from there we will begin the network security group configuration. Click + Add as this will crete a new rule. In the Destination port range field, input an asterisk * symbol as ICMP doen't use ports. Select IMCPv4 as the protocol > select Deny as the action > set priority range to 290 (290) is the lowest priority rule in the Inbound security rules meaning it will take priority over the larger number security rules, the blocking rule will take precedence). Once you have clicked add, the ICMP traffic from any source will be denied.
<b />

<p>


![image](https://github.com/user-attachments/assets/9584d2cc-a6a3-4a83-9f7d-cf98367c3bf3)

  
</p>
<p>
Now that tue rule has been created, in the Windows virtual machine you will be able to observe that "Request timed out" will populate in PowerShell. Wireshark will show (no response found) meaning that the rule has now went into effect. To cancel this, go back into the Inbound security rules of the Linux virtual machine in azure and click the trash can on the 290 rule to delete it. The rule will be deleted and the pinging will now succeed. 
<b />

<p>

![image](https://github.com/user-attachments/assets/1f75c6ab-19cd-4b60-9983-96fd1e3e2955)

  
</p>
<p>
We will be observing SSH traffic in this portion. SSH is used to make a secure connection from one computer to another utilizing a command line. In Wireshark clear the "Apply a display filter" and tpy in ssh into the search bar to filter SSH traffic. Navigate back to Azure and retrieve the IP address of the Linux virtual machine. Open up PowerShell in the Windows virtual machine and connect to the Linux virtual machine. You will be using your login credentials that were made previously, in this lab the name labuser is being used. Type in ssh labuser@10.0.0.5. (ssh labuser@<private IP address >) the password you type will appear invisible for security reasons. After this has been done you will be able to observe the SSH traffic in Wireshark.

As a side note, to confirm that you are in the Linux virtual machine, you can type in commands like (id), (uname -a) which will give you information about your machine.
Anything you type in PowerShell will generate incoming SSH traffic in Wireshark.

To exit out of the SSH connection in Powershell. Type in exit and the connection will be dropped.
<b />

<p>
We will now be observing DHCP Traffic
</p>
<p>  

  ![image](https://github.com/user-attachments/assets/bb449d50-2f27-415e-ac91-c5493c8bd306)


</p>
<p>
DHCP (Dynamic Host Configuration Protocol) which uses (UDP 67 and 68) is the protocol used to assign an IP address to devices when they first connect to a network. 

To observe this traffic, clear out the filter in Wireshark and type in dhcp. Once you have done this go into PowerShell and type in ipconfig /renew (this command will drop the IP address of the computer and will broadcast for a new IP address). If the discover packet did not show in this filter, clear out the filter and type in (udp.port == 67 || udp.port == 68) and recapture the packets. In the chance nothing populates in Wireshark we will use this method to troubleshoot. 

Open up Notepad and type in 
ipconfig /release
ipconfig /renew
Save this and put it into the Folder > This PC > Windows (C:) > ProgramData > file. Save it as All Files (for this method the filename has been named dhcp.bat). Wait for the connection to drop, the restoral process will occur with the connection being restored.
![image](https://github.com/user-attachments/assets/89968d57-2615-4094-bf4f-d85cb7314a91)

Here you can observe what the traffic looks like in Wireshark after it has been restored.
<b />

<p>
We will be observing DNS (Domain Name Systems) traffic in this portion. DNS converts human words into IP addresses. 

In Wireshark type in dns and start a packet capture. I have used the website Google as an example. In PowerShell type in (nslookup google.com), the computer will reach out to the DNS server and will show you the IP address of google.com. You will observe that in Wireshark, all the DNS traffic is being generated



Example photo of DNS traffic in Wireshark



![image](https://github.com/user-attachments/assets/91690cda-784d-4498-9707-5d8a679823ca) 



<b />




  
</p>


<p>
We will observe RDP (Remote Desktop Protocol) traffic being observed. RDP like SSH connects from one computer to another computer but uses a GUI (Graphical User Interface) instead of a command line like SSH. 

RDP is on port 3389.

In Wireshark we will filter for RDP traffic inputting (tcp.port == 3389) and starting a packet capture. 

You will observe that there is constant traffic being generated due to this lab being run on a virtual machine by using RDP. A constant picture is being streamed from the server to our local machine  
</p>
<p>


Example Photo


![image](https://github.com/user-attachments/assets/4d7a6e4c-3680-4a2d-aeb2-981bc0411815)

</p>
