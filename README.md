# OpenWRT_For_VMware
An effective method for proxying multiple virtual machines in a virtual local area network on the Windows platform
<br>
<br>
# Disclaimer
**Please comply with local laws and regulations when using this tutorial. You are solely responsible for any consequences arising from the use of this project! This project and its authors assume no responsibility for any consequences you may cause.**
<br>
<br>
# Introduction
This tutorial is applicable to the following scenarios:
1. Other VMware virtual machines require a more secure proxy network.
2. To avoid the tedious steps of enabling separate agent tools in each virtual machine.
<br>

This tutorial aims to create an [OpenWRT](https://openwrt.org/) virtual machine and then use it as a local area network gateway.
<br>

**Topology graph** is below:<br>
<img src="https://github.com/user-attachments/assets/f7f0fde6-b3e3-45f1-9c2a-974dd4fbde9e" alt="This is a topology graph" title="Topology graph" />
<br>
<br>

# Steps to create a virtual machine(openWRT)
1. Use a tool to convert the [image](https://drive.google.com/drive/folders/1MIzj4Hn9hdUZ3K8oksl2Efqs5inrBUQ7) (.img) to VMDK format. I'm using [StarWind V2V Converter](https://www.starwindsoftware.com/tmplink/starwindconverter.exe) software here.<br>
Detailed steps:<br>
Install and open the software via the download link.<br>
Then, Select Local file (Select the location of the image to convert) --> Browse to the location of the img format file (Source image) --> Local file (Select the location of the destination image) --> VMDK (Select destination image format) --> VMware Workstation growable image (Select option for VMDK image format) --> Customize the VMDK image name (Set destination file name) --> Select convert.  

2. Create a new virtual machine.<br>
Open VMware Workstation, select Custom (advanced) --> Default (Choose the Virtual Machine Hardware Compatibility) --> I will install the operating system later --> Linux (Select a Guest Operating System), Other Linux 6.x kernel 64-bit (Version) --> Name the VM. Select the location in which the VM files will be located. --> I recommend choosing 1 processor, 1 core (Processor Configuration) --> I recommend choosing 1 GB (Memory for the VM) --> Use host-only networking (Network Type) --> Default (Select I/O Controller Types) --> Default (Select a Disk Type) --> Use an existing virtual disk (Select a disk) --> Select the image previously converted to VMDK --> Disk format can be left unchanged --> Click Finish.

3. Don't rush to open the virtual machine; We should continue editing the virtual machine configuration.<br>
Edit VM settings --> Select Add Network Adapter --> Specify Network Adapter 2 as NAT mode. --> Specify Network Adapter(1) as VMnet1 (host-only mode).

**The order of network adapter and network adapter 2 is very important here. By default, network adapter (eth0) will be assigned to the LAN port, 和 network adapter 2 (eth1) will be assigned to the WAN port.**  

VMnet1 exists by default. Open **VMware** - **Edit** - **Virtual Network Editor**, set the VMnet1 network to Host-only mode and uncheck "Use local DHCP service to assign IP addresses to virtual machines".  

In addition, it is necessary to ensure that the subnet address of VMnet8(NAT subnet) is different from the subnet address of VMnet1(Host-only subnet).<br>
The final result will look similar to the image below:<br>
<img width="707" height="663" alt="image" title="network configuration" src="https://github.com/user-attachments/assets/c47ab9d7-7b03-4b14-ac9e-af9fa070565f" />   

4. Open the virtual machine and modify the network configuration file.  

`> vim /etc/config/network`  

    config interface 'lan'
    option type 'bridge'
    option ifname 'eth0'
    option proto 'static'
    option ipaddr '192.168.5.1' # Change this IP to the VMnet1 network segment, such as 192.168.5.1 This is the WEB backend address. 
    option netmask '255.255.255.0'
    option ip6assign '60'

After making the changes and saving, restart the network.  
`> /etc/init.d/network restart`  

5. After that, we can access OpenWrt using a browser. The address is the one we modified in the network configuration file. The default username is `root`, 和 the password is `blank`  

6. In the web backend, refer to the [official OpenClash documentation](https://github.com/vernesong/OpenClash/wiki) to configure the OpenClash plugin.  

7. Please refer to the topology diagram above. Configure **the network adapter of the other virtual machine that needs to use the proxy** to **VMnet1 (host-only mode)**.  Enjoy!
---
# Disclaimer
**Please comply with local laws and regulations when using this tutorial. You are solely responsible for any consequences arising from the use of this project! This project and its authors assume no responsibility for any consequences you may cause.**
