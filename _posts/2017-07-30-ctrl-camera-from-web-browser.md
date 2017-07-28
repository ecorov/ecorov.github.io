---
layout: post
title: "Tutorial 5: Control camera from web browser."
date: 2017-07-29 13:32
image: webCtrl.png
---


In Tutorial 3, we have create a web service for streaming video using mjpg-streamer, it use port **8080**, this service is always needed because we always want to see the video streaming. Now, we need to create another web service for receive and execute command from web browser to ecoROV. I choose **[lighttpd](http://www.lighttpd.net/)** because it’s *light* and can integrated with the Python library **[flup](https://pypi.python.org/pypi/flup)** to talk with Python through [FastCGI](http://redmine.lighttpd.net/projects/lighttpd/wiki/Docs_ModFastCGI). This idea was originally used (as I know) by [Dav](http://davstott.me.uk/index.php/2013/03/17/raspberry-pi-controlling-gpio-from-the-web/).


**Step 1**: build a simple web server using **lighttpd**

Install **lighttpd** and start the service

~~~
sudo apt-get install -y lighttpd
sudo /etc/init.d/lighttpd start
~~~

The installation creates a HTML file called **/var/www/html/index.lighttpd.html** as the homepage, we can visit it by typing the IP address of RPi in web browser. 


![](images/lighttpd.png)

We can create a new HTML file called */var/www/html/index.html* to make up our own homepage. For example: 

~~~
<html>
  <head>
    <title>ECOROV</title>
  </head>
  <body>
    <iframe id="streaming"  src="http://192.168.8.8:8080/javascript_simple.html" ></iframe>
  </body>
</html>
~~~

Now, refresh the homepage, you should see the RPi’s streaming video. 

![](images/simplelighttpdhomepage.png)


**Step 2**: change default homepage path of lighttpd **/var/www**

This is mainly due the pictures and videos are stored under */var/www/media*, set lighttpd’s homepage to here, so we can visit the pictures and video from web browser. 

~~~
sudo mv /var/www/html/index.html /var/www/index.html 
sudo rm -R /var/www/html
~~~

For sure, we need to tell *lighttpd* that we have changed the homepage directory by modifying its configure file: **/etc/lighttpd/lighttpd.conf**

~~~
sudo nano /etc/lighttpd/lighttpd.conf
~~~

In line “server.document-root”, change to “/var/www”. We need to restart the lighttpd service and check if it works as before in your web browser. 


~~~
sudo service lighttpd restart
~~~


**Step 3**: Let web browser talk with python

**Install *python-flup* **

~~~
sudo apt-get install -y python-flup
~~~

**Configure */etc/lighttpd/lighttpd.conf* to tell lighttpd include *FastCGI* function**.


~~~
Sudo nano /etc/lighttpd/lighttpd.conf
~~~

This is the content of “/etc/lighttpd/lighttpd.conf”: 

~~~
server.modules = (
        "mod_setenv",
        "mod_access",
        "mod_alias",
        "mod_compress",
        "mod_redirect",
        "mod_fastcgi",
#       "mod_rewrite",
)

server.document-root        = "/var/www"
server.upload-dirs          = ( "/var/cache/lighttpd/uploads" )
server.errorlog             = "/var/log/lighttpd/error.log"
server.pid-file             = "/var/run/lighttpd.pid"
server.username             = "www-data"
server.groupname            = "www-data"
server.port                 = 80
setenv.add-response-header  = ( "Access-Control-Allow-Origin" => "*" )


index-file.names            = ( "index.php", "index.html", "index.lighttpd.html" )
url.access-deny             = ( "~", ".inc" )
static-file.exclude-extensions = ( ".php", ".pl", ".fcgi" )

compress.cache-dir          = "/var/cache/lighttpd/compress/"
compress.filetype           = ( "application/javascript", "text/css", "text/html", "text/plain" )

# default listening port for IPv6 falls back to the IPv4 port
include_shell "/usr/share/lighttpd/use-ipv6.pl " + server.port
include_shell "/usr/share/lighttpd/create-mime.assign.pl"
include_shell "/usr/share/lighttpd/include-conf-enabled.pl"

fastcgi.server = (
  ".py" => (
    "python-fcgi" => (
      "socket" => "/tmp/fastcgi.python.socket",
      "bin-path" => "/var/www/py/ecorov.py",
      "check-local" => "disable",
      "max-procs" => 1
    )
  )
)


~~~

The changes includes added "mod_setenv" &  "mod_fastcgi" in *server.modules*, and specified the “bin-path” to "/var/www/py/ecorov.py" in fastcgi.server section, which **contains all codes for controlling the ecoROV**.  The following is an example for the **ecorov.py** file:

~~~
#!/usr/bin/python

# load libraries
from flup.server.fcgi import WSGIServer 
import sys, urlparse

## Define camera control functions
## Taker a picture
def im():
    with open("/var/www/FIFO", "w") as f:
        f.write("im")
        f.close()

## Start record a video
def ca1():
    with open("/var/www/FIFO", "w") as f:
        f.write("ca 1")
        f.close()

## Stop recording a video
def ca0():
    with open("/var/www/FIFO", "w") as f:
        f.write("ca 0")
        f.close()        

## Turn off camera
def ru0():
    with open("/var/www/FIFO", "w") as f:
        f.write("ru 0")
        f.close()

## Turn on camera
def ru1():
    with open("/var/www/FIFO", "w") as f:
        f.write("ru 1")
        f.close()
  

def app(environ, start_response):
  # start our http response 
  start_response("200 OK", [("Content-Type", "text/html")])
  # look for inputs on the URL
  i = urlparse.parse_qs(environ["QUERY_STRING"])
  yield ('&nbsp;') # flup expects a string to be returned from this function
  # if there's a url variable named 'q'
  if "q" in i:
    if i["q"][0] == "im": 
      im()   # take a picture
    elif i["q"][0] == "cm1":
      cm1() # Start to record a video
    elif i["q"][0] == "cm0":
      cm0() # Stop recording a video
    elif i["q"][0] == "ru0":
      ru0() # Turn off camera
    elif i["q"][0] == "ru1":
      ru1() # Turn on camera
      
#by default, Flup works out how to bind to the web server for us, so just call it with our app() function and let it get on with it
WSGIServer(app).run()

~~~

Use the following command s to create it.
~~~
sudo mkdir /var/www/py
sudo nano /var/www/py/ecorov.py

## Important
sudo chmod 755 /var/www/py/ecorov.py

~~~

**Note! ** The first line specify to use **pythonROV** which we haven’t created. 

>By default, the /usr/bin/python executable runs as the user that calls it. That’s likely to be either >the ‘pi’ user if you’re interactively using it, or the ‘www-data’ user if it’s being run by the web >server. An easy to configure way of breaching this security is to user linux’s setuid feature. This >is a potentially dangerous technique, so needs a bit of careful thought, but it is easy to set up.


~~~
sudo cp /usr/bin/python2.7 /usr/bin/pythonROV
sudo chmod u+s /usr/bin/pythonROV
sudo ls -l /usr/bin/pythonROV
~~~




Now let’s restart the **lighttpd** service again. 

~~~
sudo service lighttpd restart
~~~

Type the url **192.168.8.8/py/ecorov.py?q=ru0** in your web browser, you will see a black web page. Then open a new tab and type **192.168.8.8** , you should see that the video streaming has stopped, and you can also found the red LED light on camera board has turned off. The url 192.168.8.8/py/ecorov.py?q=ru0 actually has asked the camera to stop, and we can use 192.168.8.8/py/ecorov.py?q=ru1 to restart the camera again. 


**Step 4**: Design a user interface to control camera.

Using url to control the camera is boring. We prefer to click a button to take a picture or start a video capture. This can be easily done by adding some code in the homepage file: **/var/www/index.html**. For example: 


~~~

~~~


We need to download the *jquery* used in above file first. 

~~~
sudo mkdir /var/www/js
sudo wget -O /var/www/js/jquery-1.10.2.js https://raw.githubusercontent.com/ecorov/ecorov/master/www/js/jquery-1.10.2.js
~~~



