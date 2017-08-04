---
layout: post
title: "Tutorial 9: Let RPi control a brushless motor"
date: 2017-08-04 10:32
image: RPiBrushlessMotor_bb.png
---


**Material**: 

 * [Brushless motor](https://www.banggood.com/Wholesale-XXD-A2212-KV1000-Brushless-Motor-H363-For-RC-Airplane-Quadcopter-p-57432.html)
 * [ESC ](https://www.banggood.com/Wholesale-XXD-HW30A-30A-Brushless-Motor-ESC-For-Airplane-Quadcopter-p-50621.html?rmmds=detail-left-hotproducts)
 * [LiPo Battery (3s, 11.1V)](http://www.ebay.com/bhp/3s-lipo-battery)



#### Step 1: connect brushless motor, ESC and RPi as shown below:

![](/images/RPiBrushlessMotor_bb.png)

Note: normal ESC general has two thick wires (red and black) and 3 thin wires (black, red and white).  The two thick wires supply electricity to brushless motor and ESC. The black thin wire is ground wire which should connect to the GND pin of RPi. The white wire is the control wire which transfer PWM signal from RPi to ESC. The red thin wire is power line supplied from ESC, it generally is 5V, some ESC can supply 2A while most ESC supply 1A current. **Keep in mind: 5V2A power supply from ESC and support power to RPi board using the 5V pin (top right). **


#### Step 2:  Setup the maximum throttle (2000us) of ESC

**Unconnect one power wire between battery and ESC!!** Then start RPi, open Python console and paste the following code:**


~~~
import time
import RPi.GPIO as G   
G.setmode(G.BCM)

## Define pin connected white wire
pinPWM = 18

## Setup the pin as output channel
G.setup(pinPWM, G.OUT)

## create object for generate 500 Hertz PWM on the pin
BM = G.PWM(pinPWM, 500)

## Start to generate PWM
BM.start(0)

## Generate 2000us PWM
BM.ChangeDutyCycle(100)

~~~

Most ESC accept pwm form 1000us (stop) to 2000 us (full throttle), so if the PWM signal is 500 Hertz, then its duty cycle is 2ms (each pulse). 1000us is 1ms,  which is 50 percent of the duty cycle. 2000us is 2ms,  which is 100 percent of the duty cycle. Function *ChangeDutyCycle* allow **0.1% resolution**, so we have **500 levels** (50.0%-100.0% ) to control the motor. Above code will generate a 2000us (2ms) PWM signal, which is the maximum throttle of the ESC. 

#### Step 3:  Setup the minimum throttle (1000us) of ESC. 

Connect ESC’s power wires to battery. You will hear some sound, like **bee---**, or **bee-bee-**, which means the maximum throttle was setup successfully, and waiting for minimum signal input. Now, back to RPi’s python console, and type the following code quickly (before you hear any sound of the ESC):


~~~
## Generate 1000us PWM
BM.ChangeDutyCycle(100)
~~~

This will generate the minimum pwm signal for ESC. If the minimum throttle was successfully setup, you will hear some sound like: **bee---bee-bee-bee 123**. The **bee---** means minimum throttle was setup successfully, **bee-bee-bee** means that ESC detect the battery is a 3s battery, and **123** means that ESC is ready for input.


Now, we can drive the brushless motor using command, e.g.:

~~~
BM.ChangeDutyCycle(60.5)
~~~


Note: theoretically, the motor should start when the PWM cycle is longer than 1000us , but due to different quality of the motor, some of them will not spin until they receive a higher voltage signal. You can start with `BM.ChangeDutyCycle(50.5)`, and increase with a **step 1.0** to see when your motor start to spin. If your motor never  spin, anothr reason could be your ESC doesn’t work at all. 
