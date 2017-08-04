---
layout: post
title: "Tutorial 10: Let’s make a simple water sensor"
date: 2017-08-04 10:32
image: watersensor_bb.png
---


**Material**: 

 * Resistor (1k ohm)
 * Wires


The GPIO pins of RPi can also used to read data, for example, to detect if a switcher has turned on or off, this [video](https://www.youtube.com/watch?v=NAl-ULEattw) explained this feature very clear. Water is conductible, so can be taken as a switcher. 

#### Step 1: connect resistor to RPi as shown below:

![](/images/watersensor_bb.png)

**Note: leave the circuit open**

#### Step 2:  Start RPi, then open Python console  and paste the following code:

~~~

import time
import RPi.GPIO as GPIO   
GPIO.setmode(GPIO.BCM)

## Setup the pin as output channel
GPIO.setup(18, GPIO.IN)

for i in range(100):
	GPIO.input(18)
	time.sleep(1)
 
~~~

It should print **0** continuously cause the circuit is open. 

#### Step 3:  Put the open ends of the two wires into water, but isolated (water will connect them together). 

Let’s check the input of GPIO pin 18 again in Python

~~~
for i in range(100):
	GPIO.input(18)
	time.sleep(1)
 
~~~

It supposed to print **0**  continuously. 


**Note**, other GPIO pins can be taken as 3.3V pin by setting them to **OUT**  and **HIGH** mode,

~~~
GPIO.setup(17, GPIO.OUT)
GPIO.output(17, GPIO.HIGH)
~~~

