

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
This tutorial will be an observation of various network traffic to and from Virtual Machines in Azure through Wireshark. We will also be workig with Network Security Groups.


<h1>Pre-requisites:</h1>
This tutorial requires you to have already completed the previous tutorials and is the continuation of the last one where we created Virtual Machhines in Azure. If not completed, please use the link below to redirect to the tutorial before continuing.

- [How to Setup VMs and a Virtual Network in Azure](https://github.com/bvongpradith/creating-azure-vm)<br />
<h1>Software and Tools Used</h1>

- Microsoft Azure
- Remote Desktop
- Command-Line Tools
- Various Network Protocols (SSH, RDP, DNS, ICMP, DHCP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>Overview</h2>

- Step 1: Use Remote Desktop to connect to your Windows 10 Virtual Machine
- Step 2: Within your Windows 10 Virtual Machine, Install the WireShak application
- Step 3: Ping the Ubuntu Virtual Machine to observe ICMP traffic, disable and re-enable ICMP traffic through the Network Security Group
- Step 4: Observe SSH traffic on the Windows Virtual Machine
- Step 5: Observe DNS traffic on the Windows Virtual Machine
- Step 6: Observe RDP traffic on the Windows Virtual Machine
- Step 7: Clean up by deleting the resource groups to ensure we don't incur any charges
<h2>Actions and Observations</h2>

<p>
<img src="https://i.imgur.com/zh6pL9D.png"/>
<img src="https://i.imgur.com/5jr5619.png"/>
<img src="https://i.imgur.com/MaPydFb.png"/>
</p>
<p>
First, enter the Azure portal by going to https://portal.azure.com/. 

After you enter the portal, go to you Windows VM and copy the public IP address. We will be connecting to this VM by Remote Desktop Connection. To find RDC, type it into the Windows search bar it will be the first selection. A pop up will show up where you will put the public IP address of the VM to connect. After pressing connect, you'll need to use the username and password we created to log in and then pressing OK.
</p>
<br />

<p>
<img src="https://i.imgur.com/IvvQ2YL.png"/>
</p>
<p>
Check everything to no in the privacy settings when the VM finally loads up.
</p>
<br />

<p>
<img src="https://i.imgur.com/y5TSvVp.png"/>
</p>
<p>
In the VM, go to the EDGE browser and download Wireshark. You can copy and paste this link (https://www.wireshark.org/download.html). You will need to download the Windows Installer (64-bit)
</p>
<br />

<p>
<img src="https://i.imgur.com/oO0WZCJ.png"/>
</p>
<p>
Open the installer and install everything to the default settings by pressing "Next" all the way to the end.
</p>
<br />

<p>
<img src="https://i.imgur.com/xLbHAtN.png"/>
</p>
<p>
When Wireshark is open, filter by "icmp" packets and hit the blue shark fin to start capturing traffic through the VM.
</p>
<br />

<p>
<img src="https://i.imgur.com/4fxurLf.png"/>
</p>
<p>
Find your Ubuntu VM's private IP and copy it.
</p>
<br />

<p>
<img src="https://i.imgur.com/paiFYqT.png"/>
</p>
<p>
Head back to your VM and open cmd or powershell through the Windows search bar in the bottom left. You will now ping -t the private IP address of the Unbuntu VM to make a continuous ICMP traffic that you can observe through Wireshark. We should see requests from Ubuntu VM's private IP and replies from our Windows private IP.
</p>
<br />

<p>
<img src="https://i.imgur.com/RbC2bZL.png"/>
</p>
<p>
We are going to disable ICMP traffic from the Ubuntu virtual machine's network security group in the Azure portal. To get to the NSG page, type in "NSG" in search bar and click onto the service.
</p>
<br />

<p>
<img src="https://i.imgur.com/2ZVWZBr.png"/>
</p>
<p>
Find the NSG for the Ubuntu VM.
</p>
<br />

<p>
<img src="https://i.imgur.com/KXUwodG.png"/>
</p>
<p>
After you are on the NSG page, click on "Inbound security rules".
</p>
<br />

<p>
<img src="https://i.imgur.com/kqS58Sw.png"/>
</p>
<p>
Add a new rule with ICMP as the protocol and set the action to deny. We must change the priority to a number that is lower than the first rule in the list. The lower number allows the rule to have a higher priority than the other rules that have a higher number. In this tutorial, I set it to 200 as it has a higher priority than 300.
</p>
<br />

<p>
<img src="https://i.imgur.com/qSAu6wS.png"/>
</p>
<p>
After applying the rule, head back into the VM and observe how the ping to the private IP address will time out or fail.
</p>
<br />

<p>
<img src="https://i.imgur.com/ZFaMu0f.png"/>
</p>
<p>
Now we will select allow on the rule and the traffic should now continue through.
</p>
<br />

<p>
<img src="https://i.imgur.com/NK9QreR.png"/>
</p>
<p>
After applying the rule, we should now see traffic continue on the VM again!
</p>
<br />

<p>
<img src="https://i.imgur.com/dJxpVHL.png"/>
</p>
<p>
We have now observed ICMP traffic through RDC between two VMs and will now connect to the Ubuntu VM via SSH or Secure Shell within the command lines of PowerShell. Firstly, you will need to type "SSH (your Ubuntu private IP address). When prompted with 
  
  "The authenticity of host '10.0.0.5 (10.0.0.5)' can't be established.
ECDSA key fingerprint is SHA256:wYAdmAbrJ/JTv69D9C8feZvkyQAHgpvW5ZNdt2AvYfA.
Are you sure you want to continue connecting (yes/no/[fingerprint])?"
 
  Type "Yes"

  After continuing, it will ask for a password. This is the password that we set when we made the VM so type it in. The password will be invisible when typing so just be aware that it is still typing even though it may not look like it.
</p>
<br />

<p>
<img src="https://i.imgur.com/qwZ0w8l.png"/>
</p>
<p>
In wireshark, filter the traffic by "ssh" without saving.
</p>
<br />

<p>
<img src="https://i.imgur.com/lm3cnCf.png"/>
</p>
<p>
In the SSH connection, type in the command line linux shell commands or anything in general to see the SSH traffic. Afterwards, type "exit" to disconnect from SSH
</p>
<br />

<p>
<img src="https://i.imgur.com/wRHQB9w.png"/>
<img src="https://i.imgur.com/NFOD1JZ.png"/>
<img src="https://i.imgur.com/rjfTLR5.png"/>
</p>
<p>
 
We will now observe DHCP traffic through our VM.
  
Back in wireshark, filter by "DHCP" and refresh. In the command line, type in "ipconfig /renew" to give the Windows VM a new IP address. This proccess will use the DHCP protocol and can be observed on wireshark.
  
Afterwards, we can observe DNS traffic by typing in the command line "nslookup google.com" or any other website to see the traffic and changing the filter in Wireshark to "DNS". Lastly, we will be able to observe the constant RDP traffic by either typing it into the filter or "tcp.port == 3389" in Wireshark. This will conclude this tutorial and we will now delete the resource groups to avoid any costs.
</p>
<br />

<p>
<img src="https://i.imgur.com/TZXjAuP.png"/>
</p>
<p>
To delete the resource group, just go back to the resource groups page, click on the resource group, click delete resource group, and type/copy paste the resource group's name to confirm, and then click delete.

