---
layout: post
title: "Tutorial 12: wiring diagram of ecoROV"
date: 2017-08-04 10:32
image: wiringDiagram.png
---

![](/images/wiringDiagram-hide.png)

#### Part 1: 3G WiFi router

In [Tutorial 1](/2017/07/26/access-to-rpi-without-screen.html) & [2](/2017/07/27/rpi2internet.html), I explained how to access RPi through Ethernet cable and connect RPi to internet. A ROV can connect to a computer in field, but it’s  convenient and safer to connect to a smartphone.  The 3G WiFi router module is used to connect your smartphone to ROV. It’s very easy to use the 3G WiFi router: **(1) start and connect it with RPi, (2)search WiFi network using your smartphone and join it**. Then you can connect with your ROV using **SSH** (e.g. JuiceSSH) software or **web browser**. 

#### Part 2: Magnetic switch.

The ROV will work in water, and I hope to reduce any possible water leak into it. The reeds and a special DC-motor-driven switch together will take the work. The switch was driven by the motor: if the motor rotates clockwise, the switch will close, and the opposite, the switch will open. The two reeds control both direction power supply, so they can control the switch turn on and turn off. There could be other solution for controlling a switch, but keep in mind that the electricity current from the LiPo battery could be higher than 50A, which is enough to burn a normal switch. 


#### Part 3: water sensor

This module is used to detect wether water has gotten into the ROV, detail information can found in [Tutorial 10](/2017/08/04/water-sensor.html)

#### Part 4: I2C sensors

The water pressure sensor MS5803 and 10 DOF IMU sensor are connected together to RPi through I2C pins. There is only one I2C port (two pins), so these kind of sensor must connect together. [Tutorial 6](/2017/08/03/i2c-gy-91.html) & [7](/2017/08/03/i2c-ms5803.html) contain detail information


#### Part 5: ESC for brushless motor and 4-channel relay 

The ESC is used to control brushless motors. You can find a lot of tutorials on this topic. The 4-channel relay is used to change the rotate direction of brushless motor.  A brushless motor has three wires, its rotate direction can be changed by a special ESC, however, these kinds of ESC general work not well, and most ESC accept PWM signal from 1000us to 2000us, the control resolution of a bidirectional ESC will decrease to half of a normal ESC. Another method of change rotate direction is switch any two wires of the brushless motor. And the 4-channel relay was used to achieve this task (It’s could be first time to use relay to change the rotate direction of a brushless motor). [Tutorial 9](/2017/08/04/brushless-motor.html) has details information 

#### Part 6: Illumination module

Two 5V LEDs were connected to a relay which can controlled by changing the GPIO ouput to HIGH and LOW. The power supply for both LEDs and relay are from the 5V output of EasyDriver. [Tutorial 11](/2017/08/04/relay.html) has more details.


#### Part 7: Step-motor controlled buoyancy. 

This module is used to control a buoyancy using step-motor. The power supply for EasyDriver is from the battery directly. More details found in [Tutorial 8](/2017/08/04/step-motor.html)


#### Power supply to RPi

You may find there is no power supply to RPi through its micro-USB port. I use the 5V2A output power from ESC to power RPi through pin 5V and a GND pin. This is a very convenient design because all modules are controlled by the magnetic switch now. 
