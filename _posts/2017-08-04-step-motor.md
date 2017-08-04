---
layout: post
title: "Tutorial 8: Let RPi control a step-motor"
date: 2017-08-04 10:32
image: RPiStepMotor_bb.png
---


**Material**: 

 * [Step motor (2-phase-4-wire s)](https://www.dhgate.com/store/product/35-stepper-motor-2-phase-4-wire-1-8-degrees/382142958.html)
 * [EasyDriver (step motor driver) ](http://www.schmalzhaus.com/EasyDriver/)
 * [LiPo Battery (3s, 11.1V)](http://www.ebay.com/bhp/3s-lipo-battery)

#### Step 1: connect step motor, EasyDriver and RPi as shown below:

![](/images/RPiStepMotor_bb.png)

Note: three GPIO pins connected to EasyDriver. 

 - Yellow wire connect **DIR** (Direction) to GPIO 17
 - White wire connect **STEP** (Step) to GPIO 18
 - Blue wire connect **SLP** (Sleep) to GPIO 27

#### Step 2:  Start RPi, then open Python console and paste the following code:

~~~
import time
import RPi.GPIO as G   
G.setmode(G.BCM)

## Define pins for Step motor
pinStp = 21
pinDir = 20
pinSlp = 26

## Set up all pins as output channels.
G.setup(pinStp, G.OUT)
G.setup(pinDir, G.OUT)
G.setup(pinSlp, G.OUT)

## set EasyDriver to sleep mode
G.output(pinSlp, False)

## Function for step motor
def stepMotor(step):
    G.output(pinSlp, True)
    time.sleep(0.1)
    # Direction
    if step < 0:
        G.output(pinDir, False)
    else:
        G.output(pinDir, True)
    # step
    for i in range(1, int(abs(step) * 1.8 *100)):
        G.output(pinStp, True)
        G.output(pinStp, False)
        time.sleep(0.0001)
    G.output(pinSlp, False)
    return
~~~

#### Step 3:  Let's drive the step motor

~~~
## clockwise 
stepMotor(10)

## anticlockwise
stepMotor(-10)
~~~

The parameter **step** of function **stepMotor** determines the direction (positive & negative) and steps the motor will rotate. After every rotation, the function will let EasyDriver into **sleep mode** to save power. 

#### If you encountered the motor failed to rotate or rotate in wrong manner, very possible is the wires order connected step motor and EasyDriver is not correct. Change the order of wires will fix this problem. 



