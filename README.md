

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
After applying the rule, we should now see traffic continue again!
</p>
<br />

<p>
<img src="https://i.imgur.com/vlptP2x.png"/>
</p>
<p>
Now that we have observed ICMP traffic, we will now connect to our Ubuntu VM via SSH (secure shell) within the command line inside of the Windows VM.
  
As the image above shows, in the command line type "SSH (whatever the private IP of your Ubuntu VM is)". 
  
When prompted the following

"The authenticity of host '10.0.0.5 (10.0.0.5)' can't be established.
ECDSA key fingerprint is SHA256:73iEIznECaIszgz83pKTfng9jk2d16JT2ZozJtn3a68.
Are you sure you want to continue connecting (yes/no/[fingerprint])?"

Type "yes"
  
After connecting it will then prompt you for the password of the Ubuntu VM (we set this when we made it), type the password (note: the password won't be visible as you type it in, it may look like you aren't typing it in but you are).
</p>
<br />

<p>
<img src="https://i.imgur.com/oEBPtxB.png"/>
</p>
<p>
Now in Wireshark filter for ssh traffic and refresh, again continue without saving.
</p>
<br />

<p>
<img src="https://i.imgur.com/xebnIjE.png"/>
</p>
<p>
In the remote SSH connection within the command line type some linux shell commands like man, ls, pwd, etc and observe the SSH traffic.
  
Type "exit" to terminate the SSH connection.
</p>
<br />

<p>
<img src="https://i.imgur.com/WOVeWYF.png"/>
</p>
<p>
Now let's observe DHCP traffic.
  
In wireshark filter by dchp and refresh. In the command line type "ipconfig /renew" to issue a new IP address to the Windows VM, this will use the DHCP protocol and the traffic will be observable in wireshark.
  
Now I think we get the gist of it now. On your own you can try to observe DNS traffic by typing in the command line "nslookup google.com", or filter for RDP traffic in wireshark by typing the in the filter "tcp.port == 3389" and see the constant RDP traffic as we are currently inside of a remote desktop connection.
  
Well congrats, you have made it to the end of this lab! Now for the very last step lets clean up our Azure resources so we don't incur any costs.
</p>
<br />

<p>
<img src="https://i.imgur.com/ZttsXKR.png"/>
</p>
<p>
To delete the resource group, just go back to the resource groups page, click on the resource group, click delete resource group, and type/copy paste the resource group's name to confirm, and then click delete.
