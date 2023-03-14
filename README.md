

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
- Step 2: Within your Windows 10 Virtual Machine, Install Wireshark
- Step 3: Ping the Ubuntu VM to observe ICMP traffic, disable and re-enable ICMP traffic through the NSG
- Step 4: Observe SSH traffic by connecting through the command line on the Windows VM
- Step 5: Observe DNS traffic on the Windows VM
- Step 6: Observe RDP traffic on the Windows VM
- Step 7: Clean up by deleting the resource groups to ensure we don't incur any charges or waste credits

<h2>Actions and Observations</h2>

<p>
<img src="https://i.imgur.com/UyHslgF.jpg"/>
</p>
<p>
First go to your Azure portal if it is not already open https://portal.azure.com/. 

Then get the public IP of the Windows VM and copy it, you can find that on the Virtual Machines section on the portal and clicking on the VM in question. Next, go to the Windows start menu and search "rdp" and open the RDP app, then paste the IP of the Windows VM in the first box and hit connect. Then enter the username and password that we set when we created the VM. Click OK.
</p>
<br />

<p>
<img src="https://i.imgur.com/iqT04F6.png"/>
</p>
<p>
When the VM loads up, uncheck all privacy settings. (It doesn't really matter, but it may be faster to set up this way)
</p>
<br />

<p>
<img src="https://i.imgur.com/a9O4aHG.png"/>
</p>
<p>
In the VM, open up edge and go to this URL https://www.wireshark.org/download.html (just copy/paste). Next download the Wireshark x64 installer.
</p>
<br />

<p>
<img src="https://i.imgur.com/9xIE2p1.jpg"/>
</p>
<p>
Now open the installer and install Wireshark with the defaults.
</p>
<br />

<p>
<img src="https://i.imgur.com/YEygB7e.png"/>
</p>
<p>
Once Wireshark opens, filter by "icmp" packets and hit the blue shark fin icon in the top left to start capturing traffic.
</p>
<br />

<p>
<img src="https://i.imgur.com/xa4Sj67.png"/>
</p>
<p>
Find your Ubuntu VM's private IP and copy it.
</p>
<br />

<p>
<img src="https://i.imgur.com/Ajzoggh.png"/>
</p>
<p>
Now go back to the VM open the command line and ping -t the private IP address of the Ubuntu VM to make continuous ICMP traffic that will be captured by Wireshark. We should see requests from the Ubuntu VM's private IP, and replies from our Windows private IP as well.
</p>
<br />

<p>
<img src="https://i.imgur.com/FVyFq8w.png"/>
</p>
<p>
Now we are going to disable ICMP traffic from the Ubuntu VM's NSG (network security group) in the Azure portal. To do this, type "NSG" in the Azure portal searchbar and click into Network security groups.
</p>
<br />

<p>
<img src="https://i.imgur.com/T0yT1Ni.png"/>
</p>
<p>
Find the NSG for the Ubuntu VM.
</p>
<br />

<p>
<img src="https://i.imgur.com/iAfxZZo.png"/>
</p>
<p>
Once you found the NSG for the Ubuntu VM, click on inbound security rules.
</p>
<br />

<p>
<img src="https://i.imgur.com/Ut5fruj.png"/>
</p>
<p>
Add a new rule with ICMP as the selected protocol, set the action to deny, and set the priority to any number that is lower than the first rule in the list (the lower the number, the higher the priority that the rule has) I set it to 299 as it has higher priority than 300. Now add the rule and wait for it to apply.
</p>
<br />

<p>
<img src="https://i.imgur.com/U6QJ1li.png"/>
</p>
<p>
Now going back into the VM, in the command line hit Ctrl + C to stop the continuous ping that we did with the ping -t command. Now hit the refresh button in Wireshark and continue without saving. Now try to ping the private IP of the Ubuntu VM again, now notice that our ping requests are now being timed out as we are not getting replies from our Ubuntu VM.
</p>
<br />

<p>
<img src="https://i.imgur.com/Z91rAc3.png"/>
</p>
<p>
Now lets delete the NSG rule we made on the ubuntu VM
</p>
<br />

<p>
<img src="https://i.imgur.com/m2pstmT.png"/>
</p>
<p>
Now try to ping the Ubuntu VM again using the ping command. We now should see that the Ubuntu VM is now sending replies again!
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
