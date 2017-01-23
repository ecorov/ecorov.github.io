---
layout: page
title: About
permalink: /about/
---

###ECOROV is a ROV designed for eclogical research purpose. 

If you like fishing, you know how depressive it is after fishing for hours and there still is no fish got hooked. Then you may want to know: is there fish? If there is, why they don't bite? A ROV with video recording function can give some insight about above question, however, they generally very expensive. 


In 2014, I met with Raspberry Pi, then I have the idea to build a ROV myself. I used a lot of time to teach myself the knowledge about hardware and which module/materials I should buy. After wasting a lot motors, ESCs, etc, finially the ECOROV came to true. 

The ECOROV is designed with the following characters: cheap, easy-control, compact, and the most important is low disturbance. The current version costs around $500, controlled by mobile phone, with length around 35cm and using buoyancy to control sink and float. A new version with more exciting features is under developing.




**Contact info**:

* Name: **Huidong Tian**

* Email: **tienhuitung@gmail.com**

* [Souce code on Github](https://github.com/ecorov)

<div id="timeline"></div>
<script src="http://ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js"></script>
<script src="http://hotoo.me/life/assets/markline/0.6.0/markline.js"></script>

<script>
var Markline = require('markline');
var $ = require("jquery");

$.get("timeline.md", function(markdown){
  var line = new Markline("#timeline", markdown);
  line.render();
});
</script>


