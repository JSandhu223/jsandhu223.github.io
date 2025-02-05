---
title: Inspecting Network Traffic with Wireshark
date: 2024-09-16 15:31:00 -0600
categories: [Wireshark, Microsoft Azure]
tags: [cloud, azure, wireshark]     # TAG names should always be lowercase
---

<!-- <p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p> -->

<h2>Overview</h2>
In this lab, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />

<!--
<h2>Video Demonstration</h2>

- ### [YouTube: Azure Virtual Machines, Wireshark, and Network Security Groups](https://www.youtube.com)
-->

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 Pro (22H2)
- Ubuntu Server 24.04

<h2>High-Level Steps</h2>

1. Create a Windows 10 Pro and Ubuntu Server virtual machine under the same virtual network
2. Remote connect into the Windows virtual machine to install Wireshark
3. Execute various Windows networking commands and inspect packets in Wireshark
4. Edit NSG rules

<h2>Actions and Observations</h2>

<h3>Step 1 - Virtual Machine Setup</h3>

With Azure, we can deploy a virtual machine to the cloud and connect to it via Remote Desktop Protocol. Let's deploy a Windows 10 Pro VM under a resource group, which we'll call `azure-networking`. For our case, 2 CPUs and 16 GB of RAM will be enough.


<p float="left">
  <img src="/assets/img/azurenetworkprotocols/Step1_WindowsVMCreation.png" height="80%" width="80%"/>
  <!-- <img src="/assets/img/azurenetworkprotocols/Step1_WindowsVMCreation2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/> -->
</p>

When creating the Ubuntu Server VM, we will use a password based authenication rather than an SSH key. The Ubuntu Server VM is not as resource heavy as the Windows VM, so we can opt to use one of the cheaper hardware sizes. To ensure connectivity between the two VMs, we can put this VM under the same virtual network.

<p>
  <img src="/assets/img/azurenetworkprotocols/Step1_UbuntuServerVMCreation.png" height="80%" width="80%"/>
  <img src="/assets/img/azurenetworkprotocols/Step1_UbuntuServerVMCreation_NetworkSettings.png" height="80%" width="80%"/>
</p>

<h3>Step 2 - Wireshark Installation</h3>

Now that the VMs are created, we can use **Remote Desktop Connection** to connect to our Windows 10 Pro VM. Once the Windows setup is complete, download [Wireshark](https://www.wireshark.org/download.html). We will download the `Windows x64 Installer` and stick with the default settings. Note that the Wireshark installer will also install `Npcap`. Now, open Wireshark and you will be greeted with the following screen:

<img src="/assets/img/azurenetworkprotocols/WiresharkMainScreen.png" height="80%" width="80%"/>

<h3>Step 3 - Packet Trace</h3>

With Wireshark we can inspect all incoming and outgoing packets from our computer. We will inspect the following traffic:
- ICMP
- SSH
- DHCP
- DNS
- RDP

All these protocols (minus ICMP) have associated port numbers:

| Protocol   | Port Number | Transport Layer Protocol |
| -------- | ------- | ------- |
| SSH  | 22    | TCP |
| DHCP | 67/68     | UDP |
| DNS    | 53    | UDP |
| RDP    | 3389    | TCP |

Notice that we can filter our packets. As I am using an Ethernet connection, I will select that for caputuring traffic.

<h4>ICMP Trace</h4>

ICMP is the Internet Control Message Protocol and it is responsible for sending diagnostic information and error reporting between the sender and receiver of a message. Let's first filter by ICMP traffic by typing `icmp` into the filter text field at the top and hit apply.

<img src="/assets/img/azurenetworkprotocols/WiresharkICMPFilter.png" height="80%" width="80%"/>

Then we can start the packet trace by clicking the button on the top left.

<img src="/assets/img/azurenetworkprotocols/WiresharkICMPFilter2.png" height="80%" width="80%"/>

We should then be greeted with the following screen:

<img src="/assets/img/azurenetworkprotocols/WiresharkICMPBlank.png" height="80%" width="80%"/>

At the moment, there are no packets to display as there isn't any ICMP traffic being sent or received by our machine. Luckily, the Windows OS has built-in commands that utilize the ICMP protocol for sending and receiving information. One of these commands is the `ping` command, which is used to test connectivity to another machine. Since we created an Ubuntu client under the same virtual network as our Windows client, we can ping its private IP address (in my case this is `10.0.0.5`). In command prompt, execute the following command:

`ping 10.0.0.5`

Of course, your IP address configuration may be different so be sure to find your Ubuntu client under `Virtual Machines` in the Azure portal. Now, if you see something like this from your ping result, it means the Ubuntu client was succesfully reached!

<img src="/assets/img/azurenetworkprotocols/PingCommand.png" height="80%" width="80%"/>

If we take a look at the Wireshark packet trace, we will notice some packet information.

<img src="/assets/img/azurenetworkprotocols/PingPacketTrace.png" height="80%" width="80%"/>

<h4>SSH Trace</h4>

SSH (Secure Shell) is a protocol that allows one machine to connect to another machine (typically a Linux machine) and interact with it through a command line interface. We can connect to our Ubuntu VM through SSH as follows:

`ssh <USERNAME>@<IP>`

Here, the username is what you used to create the Ubuntu VM and the IP address is its private IP. In my case, I connect to it via the command `ssh jay@10.0.0.5`. Then we authenticate with the password used during VM creation. After this is done, you will be presented with a linux command line, indicating we have connected to the Ubuntu server. In Wireshark we will see something like this:

<img src="/assets/img/azurenetworkprotocols/SSHPacketTrace.png" height="80%" width="80%"/>

The first couple lines indicate the packets exchanged during authentication. The authentication process uses elliptic curve Diffie-Hellman, a very cryptographically secure public key exchange algorithm that prevents unauthorized access from attackers.

Now, anytime you type in a character, delete a character, or execute a command, packet data is sent to the server and back to you. This makes sense as SSH provides real-time terminal access to the other machine, which means that constant uptime is required if you want to interact with that machine and see changes.

<h4>DHCP Trace</h4>

DHCP is the Dynamic Host Configuration Protocol and it is responsible for assigning IP addresses to network devices. One commonly used command associated with DHCP is the `ipconfig` command. This command lists out the network configuration details of your machine.

<img src="/assets/img/azurenetworkprotocols/ipconfig_output.png" height="80%" width="80%"/>

Of course, if you want more detailed information such as the DHCP servers, DNS servers, or the MAC (physical) address of your network interface card, you can execute `ipconfig /all`:

<img src="/assets/img/azurenetworkprotocols/ipconfig_all_output.png" height="80%" width="80%"/>

Every once in a while, a user may experience Internet connectivity issues due to DHCP failing to assign an IP address to their machine. Luckily, there is a command to request a new IP address from the DHCP server:

`ipconfig /renew`

What we expect is two packets in the Wireshark trace. A DHCP request (sent by us) and a DHCP response (sent by the DHCP server),

<img src="/assets/img/azurenetworkprotocols/DHCPTrace.png" height="80%" width="80%"/>

<h4>DNS Trace</h4>

DNS (Domain Name System) is a protocol that converts fully qualified domain names (i.e. website names) into computer readable IP addresses. This is how web browsers are able to understand website names like `www.google.com`. A simple Windows command that utilizes DNS is `nslookup`. This command returns the IP address of a domain, or vice versa, depending on what you supply as the argument.

<img src="/assets/img/azurenetworkprotocols/nslookup_output.png" height="80%" width="80%"/>
<img src="/assets/img/azurenetworkprotocols/DNSTrace.png" height="80%" width="80%"/>

<h4>RDP Trace</h4>

RDP is the Remote Desktop Protocol which we used to remote connect to our Windows client. If you try to filter the packet trace with the keyword `rdp`, you'll notice that there isn't much traffic being captured. This is because this filter only captures *decrypted* RDP traffic. If we want to see **ALL** RDP traffic, we can apply the following filter instead:

`tcp.port == 3389`

Recall that RDP uses TCP as its transport layer protocol on port 3389. TCP is used because a remote connection needs a consistent, in-order delivery of data to be able to interact with the remote machine. After applying this filter, you should notice a flood of traffic every second. This is because every action (i.e. a key click, a mouse movement) is sent to the remote machine and transmitted back.

<h3>NSG Rules</h3>

We can think of NSG rules as the firewall of our Azure virtual machine. Here we can create rules that allow or deny traffic based on an IP address, a range of IP addresses, or protocol. To get to this page on the Azure portal, first navigate to the Windows client VM under the `Virtual Machines` section and go to `Network settings`

<img src="/assets/img/azurenetworkprotocols/AzureNSG.png" height="80%" width="80%"/>

Note that the number associated with each rule is a priority; a smaller number indicates higher priority. Let's create a rule that denies all inbound ICMP traffic. This will prevent us from pinging any other machine/server.

<img src="/assets/img/azurenetworkprotocols/AzureNSG_AddInboundRule.png" height="80%" width="80%"/>
<img src="/assets/img/azurenetworkprotocols/AzureNSG_DenyICMP.png" height="50%" width="50%"/>

Now if we try to ping, say, our linux client, we should get an indication of the ping failing.
