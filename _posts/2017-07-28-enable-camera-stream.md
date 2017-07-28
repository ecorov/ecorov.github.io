---
layout: post
title: "Tutorial 3: Enable camera module and start video streaming"
date: 2017-07-27 14:32
image: videostream.png
---



**Task**: start video streaming from RPi to web browser. 

**Materials**: 

* [Raspberry Pi camera](https://www.raspberrypi.org/products/camera-module-v2/) 


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

**Step 2**: Stream video to memory using **raspimjpeg**

There are two popular video streaming tools available to Raspberry Pi: [picamera](https://picamera.readthedocs.io/en/release-1.13/) & [RPi-Cam-Web-Interface](http://elinux.org/RPi-Cam-Web-Interface). Picamera is written in Python, so very easy to use under RPi, and easy to cooperate with other image processing tools written in Python. RPi-Cam-Web-Interface is written in C, so it’s faster. One another advantage is it can stream video at the same time of recording video or taking picture. *Picamera* can also do it, but it stream video only to display device (a screen). So I choose *RPi-Cam-Web-Interface* (raspimjpeg).


~~~
# Download raspimjpeg and make it available for system path.
sudo wget -O /opt/vc/bin/raspimjpeg https://github.com/ecorov/ecorov/raw/master/bin/raspimjpeg
sudo chmod 755 /opt/vc/bin/raspimjpeg
sudo ln -s /opt/vc/bin/raspimjpeg /usr/bin/raspimjpeg

# Download the configure file for raspimjpeg
sudo wget -O /etc/raspimjpeg https://raw.githubusercontent.com/ecorov/ecorov/master/etc/raspimjpeg
sudo chmod 644 /etc/raspimjpeg

# raspimjpeg need a FIFO: First In First Out
sudo mkdir -p /var/www 
sudo mknod /var/www/FIFO p
sudo chmod 666 /var/www/FIFO

# Creat a folder in memory (so more efficient) for store the recorded images.
sudo mkdir -p /dev/shm/mjpeg
sudo chmod 777 /dev/shm/mjpeg

# start recording 
sudo su -c 'raspimjpeg > /dev/null &' 

# To stop raspimjpeg, using command "sudo killall raspimjpeg"
~~~

You should see the *led light* on camera now turn to **red**. 

**Step 3**: stream a picture to web browser

The video streaming is actually stream  a series of pictures in a high frame ratio. I compared different streaming methods and finally choose  **mjpg-streamer**, it supplies several different streaming methods includes stream pictures. And it is actually very stable and effective, the time delay could be less than 200ms. 

* **Install git and close the mjpg-streamer repository** 

~~~
# Install git (for clone repository), 
# cmkae (C language compilation tool ) and 
# libjpeg8-dev (library used by mjpg-streamer)
sudo apt-get install -y git cmake libjpeg8-dev

# Download and compile mjpg-streamer
sudo git clone https://github.com/ecorov/mjpg-streamer.git
cd mjpg-streamer; 

# The following commands may take several minutes
sudo make
sudo make install
sudo ln -s mjpg-streamer/mjpg-streamer /usr/bin/mjpg-streamer
~~~


The last line in above block create a softlink between mjpg-streamer and */usr/bin/mjpg-streamer* where is under the system environment path, so can be found and execute correctly. 

* **Start to stream**

~~~
cd /home/pi/mjpg-streamer;
mjpg_streamer -i "input_file.so -d 0.05 -f /dev/shm/mjpeg -n cam.jpg" -o "output_http.so -w ./www -p 8080"&
~~~

The *mjpg_streamer* will monitor the change of picture under folder */dev/shm/mjpeg*, if change happened, then it will be streamed to server:8080

We can check the streamed pictures on **RPi-IP:8080/javascript_simple.html**

**Step 4**: enable video stream every time start the Raspberry Pi.

~~~
sudo wget -O /etc/rc.local https://raw.githubusercontent.com/ecorov/ecorov/master/etc/rc.local
sudo chmod 755 /etc/rc.local
~~~

Now reboot RPi, and you should see the red led light on camera, and the streaming video at **RPi-IP:8080/javascript_simple.html**





