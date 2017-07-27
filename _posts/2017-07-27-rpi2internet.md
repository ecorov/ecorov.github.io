---
layout: post
title: "Connect Raspberry Pi to internet"
date: 2017-07-27 16:32
image: access2rpi.png
---



We need the RPi can access to internet for updating, downloading, etc. I will show three type of connection to internet:

* Access to EAP WiFi with wifi name and password
* Access to PEAP WiFi with wifi name, userID and password
* Access to smartphone’s Hot-spot with hot-spot name and password

All above connections share same setting steps. 

Let’s check RPi’s current network interface using shell command ifconfig

![]( /images/ifconfig0.PNG)


We can see there are three different interface: **eth0**, **lo**, **wlan0**.
 
* **eth0** is for ethernet cable (RJ45) port, 
* **lo** meaning local loopback interface, which often used when testing service locally
* **wlan0** mean the wifi interface, currently, our RPi has only one wifi module, so it ended with *0*. *Wlan0* is the interface we need to modify which currently has no IP address. 

**Step 1**: set the IP assign method to dynamic 

```shell
sudo nano /etc/network/interfaces
```


* Add **auto wlan0** above line allow-hotplug wlan0
* Change **iface wlan0 inet manual** to **iface wlan0 inet dhcp**

**Step 2**： configure wpa_supplicant.conf file 

```shell
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
```

* For connection to EPA WiFi and personal Hot-spot, add following lines to its end.

```shell
network={
      ssid="wifi name"
      psk="password"
  }
```
![]( /images/interfacessetting.PNG)

* For connection to PEPA WiFi, add following lines to its end.

```shell
network={
eap=PEAP
 key_mgmt=WPA-EAP 
 ssid="wifi name"
 identity="user name"
 password="password"
   }
```

**Step 3**: restart networking with new configuration.

```shell
sudo /etc/init.d/networking restart
```

You can test your internet connection using command: 

```shell
ping www.google.com
```

Now, let’s update the Raspbian Jessie to the latest version

```shell
sudo apt-get -y update
```








**Task**: access to Raspberry Pi from computer through an Ethernet cable.

**Hardwares**:

* Raspberry Pi 3 Model B with Micro SD card
* Micro SD card reader
* DC Power with output 5V2A through micro USB cable
* Ethernet cable (RJ45 port)


![]( /images/access2rpi.png )

**Softwares**:

* [RASPBIAN JESSIE LITE](https://downloads.raspberrypi.org/raspbian_lite_latest)
* [Win32 Disk Imager](https://sourceforge.net/projects/win32diskimager/)
* [PuTTY](https://the.earth.li/~sgtatham/putty/latest/w64/putty-64bit-0.70-installer.msi)



**Step 1**:  Download above softwares, then install *Win32DiskImager* and *PuTTY*, unzip *RASPBIAN JESSIE LITE*.
 
 
**Step 2**:  Install *Raspbian Jessie Lite* into Micro SD card using *Win32DiskImager*.

1. Insert the microSD card into its reader, then plug the reader into your computer. You should see it on your computer’s *Devices and drives*.
    
2. Open *Win32DiskImager*, navigate to the unzipped raspbian image *file* and check the *device* is correct. Then click the *write* button to burn image to Micro SD card.

![]( /images/Win32DiskImager.PNG)


**Step 3**: Setup a static IP and enable SSH 

1. Open the Micro SD card (now, its name called **boot**), and edit file **cmdline.txt** by appending ** ip=192.168.8.8** to its end. 
2. Create a new file called **ssh** to enable SSH. Thanks to scruss
3. Insert the Micro SD card into RPi

**Step 4**: setup computer’s IP address manually to under same subnet mask as RPi (above), so we can access RPi from computer. 

Open *Ethernet Properties*, find and open *Internet Protocol Version 4 (TCP/IPv4)*. In the opened window select *Use the following IP address*, then set IP address to: 192.168.8.7, set Subnet mask to 255.255.255.0, and set Default gateway to 192.168.8.1. Save the changes.

![]( /images/ipv4.PNG)

**Step 5**: access to RPi using *PuTTY*

1. Connect computer and RPi using ethernet cable, and power on the RPi. You will see the green led at ethernet port blinks

2. Open *PuTTY* and setup the *Host Name (or IP address)* to the one you added to *cmdline.txt*, eg. 192.168.8.8. By default setting, the *Port* is 22, and connection type is *SSH*.  Then click *Open*.

3. The connection may failed several times if the RPi still not get it ready. Once connected, you will see a pop-up window about the key fingerprint, choose *Yes*. Then *PuTTY* will ask you to type the username and password. By default, the username is **pi**, and the password is **raspberry**. You are supposed to access RPi successfully. 

![]( /images/interfacessetting.PNG)


