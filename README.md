# How to set up a Home VPN Server Using Raspberry Pi with Pivpn

It is possible to get the benifits of Network Wide adblocking of Pihole outside of home network. In this tutorial, We will be achieving this by configuring a VPN wireguard tunnel using PiVPN.

***
**Important Information**

This tutorial assumes that you have a Raspberry Pi setup with Rasberry Pi OS and Pi-hole cofigured on it.
***

About
-----

Visit the [PiVPN](https://pivpn.io) site for more information.
This is a set of shell scripts initially developed by **@0-kaladin** that serve to easily turn your Raspberry Pi (TM) into a VPN server using two free, open-source protocols:
  * [WireGuard](https://www.wireguard.com/)
  * [OpenVPN](https://openvpn.net)

**Installation**
-----------------

SSH into Raspberry Pi and run anyone of the following command.

**To install from Stable branch**
```Shell
curl -L https://install.pivpn.io | bash
```

**To install from Test/Development branch**

```shell
curl -L https://test.pivpn.io | TESTING= bash
```

**How it works**

The script will first update your APT repositories, upgrade packages, and install WireGuard (default) or OpenVPN. We will be going with wireguard since it is newer, more secured and easier on battery of clients.

**Installation Wizard**

1. The first thing that script will configure is a static IP address. Since we have already configured a static IP address for Pi-Hole. We will select Yes when the installer prompts for DHCP reservation to keep our static IP.
2. Check the IPs when the wizard displays current network settings.
  - IP address should be static IP of your RaspberryPi.
  - Gateway should be IP address of your router.
3. You will be warned that you can potentially run into IP conflicts when using this method. The way around that is to use DHCP reservation in router. However, most routers should be smart enough to stop this from being a problem.
4. you will be prompted to specify a local user to store the WireGuard configuration files. Since I haven't configured any additional users in my pi, I'll select default user.
5. Here, we will be prompted to select VPN we want to install. Select WireGuard and press the ENTER key to continue.
6. This screen will allow you to change the port the WireGuard uses on your Raspberry Pi. Defaule wireguard port is `51820`. You can keep it the same or change it to any other port except default/reserved ports for other applications. 

**Make a note of port you'll be entering here since we'll be NAT Forwarding(Opening) the same port from our router to the internet**

Press the ENTER key to confirm the specified port.

7. We will be prompted to specify the DNS provider that we want to use for our VPN clients. The installer will automatically detect Pihole installed and will prompt to use it as DNS. Select Yes to confirm.
8. The wizard will now prompt whether we want to use a Public IP or a DNS Name. Using your public IP address is the easiest option. However, this should only be used if you have a static IP address. Your public IP will be fetched by the script. Press the ENTER key to confirm.

**Make sure to contact ISP and request for a static IP prior setting this VPN up**

VPN won't work in most cases where static IP isn't assigned to you by ISP. ISPs DHCP server dynamically assigns IP address to you and that dynamically assigned internal IP is never directly exposed to the internet. Which means, you can never connect to your internal network from internet.

9. The PiVPN script will now generate the server key that WireGuard requires.
Press the ENTER key again.
10. This screen will give you a quick rundown about unattended-upgrades and why you should enable them.
11. You can now enable the unattended-upgrades by selecting the Yes option.
12. You have now successfully installed the WireGuard VPN software to your Raspberry Pi.
13. You will be asked whether you want to restart your Raspberry Pi before continuing.
Confirm by selecting Yes option.

**Router Configuration**

In order to successfully connect to our configured Pivpn from the internet we'll need to open the wireguard port we setup above to the internet.

1. Login to the Router's admin page. Typically it is 
`192.168.0.1` or `192.168.1.1`

2. Goto advanced settings and search for NAT forwarding/Port Forwarding option.
3. Input details as below.
   - Name: WireguardVPN
   - Device IP: <Static IP of your raspberrypi/IP of your pihole>
   - External Port: 51820
   - Internal Port: 51820
   - Protocol: UDP
4. Select Save/Enable.
5. Reboot Router.

**Adding Clients**
1.  To begin creating a new profile for WireGuard, we need to run the following command.
`pivpn add`
2. You will be prompted to type a name for client. Type a name of your choice and press Enter.
3. A profile will be created and saved in default path /home/pi/configs.
4. Type 'pivpn -qr'
5. Input serial number of your added client.
6. A QR code will be generated on your screen.
7. If you're using Android/IOS client, Download [Wireguard](https://www.wireguard.com/install/) application from PlayStore/App Store.
8. Select '+' icon and select scan.
9. Scan the displayed QR code, input name of your choice.
10. VPN is successfully configured on your client.
11. To check connected clients on your Pivpn, type `pivpn -c`

**Troubleshooting**

1. If you're unable to connect, type `pivpn -d`

 Check whether your pivpnHOST: <Your public IP>
 
 Goto https://whatismyipaddress.com/ to validate your PublicIP.

 and pivpnDNS1: <IP address of your Pihole>

 If above values aren't correct, input below command to access conf file and edit values.
 
 `sudo nano /etc/pivpn/wireguard/setupVars.conf`

 To save, press `Ctrl+X --> Y --> Enter`
 
2. If you're able to access local devices from VPN but have no internet access. Make sure that you have 'Listen to All Interfaces, All Origins' enabled in your PiHole DNS settings.

 Happy Adblocking :)
