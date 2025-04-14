# Monitoring and Managing VM Traffic in Azure with NSGs and Wireshark






<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>

</h1>


In this tutorial, I will observe and inspect network traffic between Azure Virtual Machines and experiment with Network Security Groups (NSGs). In the tutorial I will be filtering different network protocols such as ICMP, SSH, DHCP, DNS and RDP and I will be observing the traffic. I will explain how to set up a basic firewall in Azure to block ICMP traffic and use various network command-line utilities and tools.


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Network Protocols used (SSH, RDP, DNS, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 Pro (22H2)
- Ubuntu Server 20.04


<h2> Basic Steps</h2>

- I will create one resource group and two Virtual Machines. I will use one Windows VM and one Linux VM
- I will be using Remote Desktop to connect to them
- I will use Wireshark to observe the network traffic

  <h2> The Tutorial </h2>

The diagram below basically illustrates the setup and objective of this tutorial.

![image](https://github.com/user-attachments/assets/a5a2f4b8-1914-45ef-a29f-d629194a63b4)


- First I will create the resource group and then I will create 2 virtual machines.
  
- I will create the VMs (Virtual Machines) in Microsoft Azure and it is important to make sure that both VMs are in the same virtual network/subnet and resource group.
  
- Open portal.azure.com in your browser > Create a resource group > You can name it whatever you like > I will be naming mine "Network-Activities-RG" > Review + Create > Create

![image](https://github.com/user-attachments/assets/479bf6d4-2f81-447a-89df-b7182941e824)

- Next I will create a Virtual Machine > portal.azure.com > Virtual Machines > Create > Make sure to choose the resource group you just created, so I will choose "Network-Activities-RG > Under image, choose: Windows 10 Pro, version 22h2 -x64 Gen2 > Under Administrator Account > fill out Username + Password > Make sure to check the Licensing box
  
- Under the Networking category, you can either allow Azure to auto-create a Virtual Network or create your own. I will let Azure auto create one, so I will just click > Review + Create > Create 


![image](https://github.com/user-attachments/assets/d81781bd-9dd1-41e7-9615-f2c9aa7649b6)

- Next we will need to create another VM, this will be the Linux VM.

- The exact same steps as before apply here. I will name this Virtual Machine Linux-VM2 > Make sure to choose the same Resource group, Region and Virtual Network as the Windows VM.

- If you let Azure auto create a Vnet for the Windows VM, verify that the Linux VM is also in that Vnet in the Networking Category > In my case Azure auto created the Vnet Windows-VM1-vnet for my Windows VM, so this is the one I will choose for my Linux VM aswell. (It will be the default Vnet if you have no other Vnets) > Choose Ubuntu Server 22 under Image > Under "Authentication Type: choose password > fill out username + password > Check licensing > Review + Create > Create

![image](https://github.com/user-attachments/assets/97005626-e28d-4be6-b98f-9fec75eacb46)



- Now I will connect to the Windows Virtual Machine via Remote Desktop.

- To do that I will get the public IP adress of the Windows VM, which can be found in Microsoft Azure > Virtual Machines > Next to the Virtual Machine, you will see the public IP address and paste it into Remote Desktop

![image](https://github.com/user-attachments/assets/db322e33-9432-49a4-a1f5-bd1891ce9d71)

- Open Remote Desktop in Windows.

- If you are using Mac you can download Remote Desktop in the Mac App Store. All you have to do is paste the Public IP of the Windows VM.

- If you want to, you can name the VM in Remote Desktop to get a better overview of the different VMs. For example "windows-vm" or "linux-vm". An example of what Remote Desktop might look like is shown below

![image](https://github.com/user-attachments/assets/98eedc60-a17d-4154-9a8d-d9acfc5d1954)

- Now we will install a protocol analyzer to observe network traffic. In this tutorial I will be using Wireshark.

- Log in to the Windows VM via Remote Desktop > open a browser in the VM > https://www.wireshark.org/ > Download > follow the install configuration, you can simply use all the default configurations

![image](https://github.com/user-attachments/assets/0f6d199c-f940-4cc2-b372-0eaff02ef3d0)

- Open WireShark > Ethernet should be highlighted > Click on the shark icon in the top left corner labeled 'Start Capturing Packets.
 
- You can now see all the network traffic happening on the backend.

![image](https://github.com/user-attachments/assets/019f0f9a-aea4-400c-828e-f1c10be56da7)

- I will now ping the Linux VM from within the Windows VM and observe the traffic via Wireshark.

- First I will find the Linux VMs private IP address > Go to Microsoft Azure > Virtual Machines > click on your Linux VM > Look for the private IP address > Now open your Windows virtual machine > Open Wireshark and filter to icmp traffic > In Wireshark, type 'icmp' in the filter bar at the top. > Open Powershell as an admin > ping the Linux VMs private IP address, command: ping "IP address"
  
![image](https://github.com/user-attachments/assets/35825717-f93f-42f8-9133-2859ae87b4de)

- I will now setup a firewall within the Linux-VM.

-  This will block the inbound ping requests from the Windows VM > Go to portal.azure.com > Virtual Machines > Your Linux VM > Networking > Network settings > On the right side click on > Network Security Group: Linux-vm-nsg
  
![image](https://github.com/user-attachments/assets/86d6f88f-cead-4ff9-9c1d-7f6898ac77e0)

- In "Linux-vm-nsg > Settings > Inbound security rules > add > Put "Destination port ranges" to * > Protocol to "ICMPv4" > Action: Deny > Priority: 290 > Add

![image](https://github.com/user-attachments/assets/e795d45f-28c4-4df3-8e13-0e853315a8b3)

- If we go back into the Windows VM, and use the ping command we will get a "Request timed out" and in Wireshark we will get no reply from the Linux VM.

- The firewall is now blocking the ping request from the Windows VM. It will specifically block all inbound ping. 

![image](https://github.com/user-attachments/assets/06b1933f-d9d1-411d-8939-a973497a099d)

- Observing SSH traffic (Secure Shell, port 22 TCP)

- Log in to your Windows VM > Open Wireshark and filter for "ssh" traffic > Open Powershell as an admin > run the command ssh + Linux VM usersname + its private IP address for example: ssh jokeren@10.2.0.5 > yes > enter password > When you are entering your password nothing will show, that is for security reasons 
  it will still register what you are typing.

![image](https://github.com/user-attachments/assets/70aaa978-3a8a-42e5-8697-67a3a46593b2)

- We are now connected to the Linux VM via SSH.

- You can type some random stuff in powershell and inspect it in Wireshark. You will see that the data is encrypted so we cannot see what the actual packets contain. This is because SSH is a secure connection and encrypts the data

![image](https://github.com/user-attachments/assets/3c0679ce-b47a-41b5-ae08-08d60aa83f7a)

- Exit the connection > Powershell command > "exit" 

![image](https://github.com/user-attachments/assets/8aae2feb-7931-4a1b-942d-e70e281cfbae)

- Observing DHCP Traffic (Dynamic Host Configuration Protocol, port 67 and 68 UDP)

- The full DHCP process can be boiled down to -> Discover, Offer, Request, Acknowledge. The following steps are not necessary - you can simply type DHCP in the filter bar and observe, however for the sake of this tutorial I will showcase the full DHCP process.

- To force the full process we need to make a bat file. Open notepad > Type ipconfig /release, ipconfig /renew 

![image](https://github.com/user-attachments/assets/63a59006-e0f2-41d1-8fa8-0c935bf6f3d4)

- Now save it on the directory C:\ProgramData > name it dhcp.bat and save as all files 

![image](https://github.com/user-attachments/assets/181e87a8-7119-4149-9daa-523d571633f6)

- Open powershell as an administrator > type: cd c:\programdata > enter > type: ls > enter > type: .\dhcp.bat > enter

- This command should almost instantantiously use the release and renew command simultaneously, so that we do not lose the connection to the VM and in Wireshark we can observe the full DHCP traffic

![image](https://github.com/user-attachments/assets/2f8ffda8-067a-400e-a8c7-9b560b59d6f4)

- Observing DNS (Domain Name System, port 53 TCP and UDP)

- This is very simple > In Wireshark filter for "dns" > Open Powershell > type: nslookup any website > example: nslookup facebook.com or nslookup youtube.com

![image](https://github.com/user-attachments/assets/49c8b652-b55a-4aae-aa4d-457fcd67195d)

- Observing RDP: (Remote Desktop Protocol, port 3389 TCP)

- In Wireshark filter for rdp > There will be a lot of traffic because we are connected through remote desktop

![image](https://github.com/user-attachments/assets/b3f3509f-057b-4ec4-9424-55b103d75411)

This concludes the tutorial. You have now observed traffic from common protocols and experimented with blocking traffic using Network Security Groups. It’s a great way to get a feel for how networking works in Azure and how tools like Wireshark can help you see what’s going on behind the scenes. I hope you learned something new! 

