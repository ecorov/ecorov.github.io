---
layout: post
title: "Tutorial 3: Enable camera module and start video streaming"
date: 2017-07-27 14:32
image: videostream.png
---



**Task**: start video streaming from RPi to web browser. 

**Materials**: 

* Raspberry Pi camera 


**Step 1**: enable camera module

Mount camera module to Raspberry Pi as shown following:

![]( /images/videostream.png)

By default, camera module is off. That’s because turn it on will let relative [GPU firmware](https://www.raspberrypi.org/documentation/configuration/camera.md) keep running. But now we do need it keep running. 

The official solution for enable camera module seems not clear enough, I can’t find the correct option. After searching a while, I found it is in submenu **#5 Interfacing Options**, thanks to [goldilocks](https://raspberrypi.stackexchange.com/questions/68927/why-raspberry-pi-camera-wont-appear-on-rasp-config)

~~~
sudo raspi-config
~~~

Save, and reboot RPi `sudo reboot`. After reboot, we can test the camera module using the following command:

~~~
raspistill -v -o test.jpg
raspivid -o vid.h264
ls -l
~~~

Step 2: Stream video to memory using raspimjpeg

There are two popular video streaming tools available to Raspberry Pi: Pycamera & raspimjpeg. Pycamera is written in Python, so very easy to use under RPi, and easy to cooperate with other image processing tools written in Python. Raspimjpeg is written in C, so it’s faster. One another advantage of raspimjpeg is it can stream video at the same time of recording video or take picture. Pycamera can also do it, but it stream video only to display device (a screen). So I choose raspimjpeg.


~~~
sudo wget -O /opt/vc/bin/raspimjpeg https://github.com/ecorov/ecorov/raw/master/bin/raspimjpeg
sudo chmod 755 /opt/vc/bin/raspimjpeg
sudo ln -s /opt/vc/bin/raspimjpeg /usr/bin/raspimjpeg

sudo wget -O /etc/raspimjpeg https://raw.githubusercontent.com/ecorov/ecorov/master/etc/raspimjpeg
sudo chmod 644 /etc/raspimjpeg

sudo mkdir -p /var/www 
sudo mknod /var/www/FIFO p
sudo chmod 666 /var/www/FIFO

sudo mkdir -p /dev/shm/mjpeg
sudo chmod 777 /dev/shm/mjpeg

sudo su -c 'raspimjpeg > /dev/null &' 
~~~






Step 3: stream a picture to web browser

The video streaming is actually stream  a series of pictures in a high frame ratio. For now, we will stream only one picture, so the video seems stopped, but it’s not. I compared different streaming method and finally choose  mjpg-streamer, it supplies several different streaming method includes stream pictures. Let’s take the picture taken above (test.jpg) as an example. 

1. Install git and close the mjpg-streamer repository 

~~~
sudo apt-get install -y git
sudo git clone https://github.com/ecorov/mjpg-streamer.git
sudo ln -s mjpg-streamer/mjpg-streamer /usr/bin/mjpg-streamer
~~~


The last line in above block create a softlink between mjpg-streamer and /usr/bin/mjpg-streamer where is under the system environment path, so can be found and execute correctly. 

2. Start to stream

~~~
cd /home/pi/mjpg-streamer;
mjpg_streamer -i "input_file.so -d 0.05 -f /dev/shm/mjpeg -n cam.jpg" -o "output_http.so -w ./www -p 8080"&
~~~

The mjpg_streamer will monitor the change of picture test.jpg, if change happened, then it will be streamed to server:8080

We can check the streamed pictures on **RPi-IP:8080/javascript_simple.html**































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
