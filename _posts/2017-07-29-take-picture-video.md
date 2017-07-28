---
layout: post
title: "Tutorial 4: Let RPi take picture, record video while streaming"
date: 2017-07-28 13:32
image: im.png
---

[RPi-Cam-Web-Interface](http://elinux.org/RPi-Cam-Web-Interface) use PHP & Apache to steam videos, take pictures and record videos. However, I am not familiar with them. There is another solution to have the same features: write camera command into the **FIFO** which is actually *RPi-Cam-Web-Interface* do. But for simplicity, I use Python. Another reason to use Python is all other controls of the hardwares of ecoROV were also written in Python. 

Let’s start Python first by type `python` in RPi’s terminal. 

**Example 1: take picture**.

Let’s define a function of taking a picture

~~~
def im():
    with open("/var/www/FIFO", "w") as f:
        f.write("im")
        f.close()

~~~

Then we execute that function `im()`. There should be one picture with thumb appear in directory **/var/www/media**. You can open another terminal to check using command:

~~~
ls -l /var/www/media
~~~

**Example 2: record video**.
Define functions

~~~
## Start record a video
def ca1():
    with open("/var/www/FIFO", "w") as f:
        f.write("ca 1")
        f.close()

## Stop record a video
def ca0():
    with open("/var/www/FIFO", "w") as f:
        f.write("ca 0")
        f.close()
~~~

Start to record, and stop after a while.

~~~
ca1()
## After some time, e.g. 30 seconds.
ca0()

~~~

Now, you can see a **.h264** video file and its thumb appear in **/var/www/media**.

**Example 3: turn off and on camera**.

~~~
def ru0():
    with open("/var/www/FIFO", "w") as f:
        f.write("ru 0")
        f.close()

def ru1():
    with open("/var/www/FIFO", "w") as f:
        f.write("ru 1")
        f.close()

~~~

Turn off and turn on camera.

~~~
ru0()
## wait for several seconds
ru1()
~~~

The [RaspiMJPEG](https://github.com/ecorov/ecorov/blob/master/bin/RaspiMJPEG-manual.txt) has a full list of all cammands can be used. However, some of them are not work on this project due to I didn’t install *RPi-Cam-Web-Interface* as its official guide. 

*RPi-Cam-Web-Interface* has also feature of motion detection. I try to includes that feature, but failed, I think that can also be done in Python, for example, monitor the size change of **cam.jpg**, set up a threshold value to trigger video recording, while the changes are smaller than threshold value for a period, then stop video recording. 





