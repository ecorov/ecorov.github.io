---
layout: post
title: "Step 2: Connect Raspberry Pi to internet"
date: 2017-07-27 14:32
image: interfacessetting.png
---



We need the RPi can access to internet for updating, downloading, etc. I will show three type of connection to internet:

* Access to **EAP WiFi** with wifi name and password
* Access to **PEAP WiFi** with wifi name, userID and password
* Access to smartphone’s **Hot-spot** with hot-spot name and password

**All above connections share same setting steps.** 

Let’s check RPi’s current network interface using  command **ifconfig**.

![]( /images/ifconfig0.PNG)


We can see there are three different interface: **eth0**, **lo**, **wlan0**.
 
* **eth0** is for ethernet cable (RJ45) port, 
* **lo** meaning local loopback interface, which often used when testing service locally
* **wlan0** mean the wifi interface, currently, our RPi has only one wifi module, so it ended with *0*. *Wlan0* is the interface we need to modify which currently has no IP address. 

**Step 1**: set the IP assign method to dynamic 

~~~
sudo nano /etc/network/interfaces
~~~


* Add **auto wlan0** above line allow-hotplug wlan0
* Change **iface wlan0 inet manual** to **iface wlan0 inet dhcp**

**Step 2**： configure wpa_supplicant.conf file 

~~~
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
~~~

* For connection to **EPA WiFi** and personal **Hot-spot**, add following lines to its end.

~~~
network={
    ssid="wifi name"
    psk="password"
}
~~~


![]( /images/interfacessetting.PNG)

* For connection to **PEPA WiFi**, add following lines to its end.

~~~
network={
    eap=PEAP
    key_mgmt=WPA-EAP 
    ssid="wifi name"
    identity="user name"
    password="password"
}
~~~

**Step 3**: restart networking with new configuration.

~~~
sudo /etc/init.d/networking restart
~~~

You can test your internet connection using command: 

~~~
ping www.google.com
~~~

Now, let’s update the Raspbian Jessie to the latest version

~~~
sudo apt-get -y update
~~~
