---
layout: post
author: "Piotr Strzałka"
tags: vesc electronic
---
# VESC - cost sensitive solution

Playing with electronic speed controllers can be really enjoyable and frustrating at the same time. I have always had a hard time using custom ESC’s when everything seems to work well and in the next second some tangled wire, or ESD charge kills an important chip or even one crucial I/O pin spoiling all the fun.

Most of the time I am working with a VESC controller thanks to the fact that it is open source (in hardware and software) and very popular. Assembled, it costs around 70-100$ so burning it once or twice can discourage further work.

I was facing that problem for a long time, finally it seems that I found a way out and I want to share it with you. The idea is to assemble controllers on your own. Whole process includes ordering pcbs and parts (also a couple which are the most fault prone) and assembling them by hand (it is easier that it seems). After that we can get 3 pieces of controller with spare parts in the price of one assembled to go VESC. If you are interested, I invite you to read further.


## STEP 1 Gathering components:

### PCBS
Currently the best option to order them is to use JLCPCB manufacturer, ( **href** here is a comprehensive description of how to order boards). IMHO the basic set of pcbs for playing with VESC should contain at least:
**href** VESC main board (we will use version 4.12 which production files are available on github) (5pcs - 7$)
**href** VESC capacitor board (10pcs -2$)
**href** AntiSparkSwitch (10pcs -2$)
TOTAL: Boards 11$ + Shipping 5$: 16$

### COMPONENTS
Bill of materials for VESC can be found on Benjamin Vedder github. **href** Here is my version of it which includes parts for 2 VESC boards, 2 capacitor boards and 2 antispark switch. It also contains spare parts which most probably will be burned while playing with custom applications. (I am currently experimenting with replacing mosfets  with cheaper alternatives).

### EXTRA
To play with VESC properly it is a good idea to order also NRF bluetooth module **href** here is a example of that module. It allows connecting mobile app to device, controlling, checking state of device, making bridge connection to PC apps and many many more.
Potentiometer, Remote controller or anything else that can be used as a control device.  
Programmer: some ST-link or J-Link (most of ST development boards have ST-Link embedded into)


## STEP 2 Assembly:

For many this seems the toughest part, but it is not necessarily true. To assemble you need to have: Soldering iron, Soldering wire, Flux, Tweezers, Copper braid, Hot air gun or a bit of thermal paste.


I have prepared top and bottom component placement prints for your convenience, you can print it and should easily assemble the whole board with it. Of course you can also install **href** Kicad software and look at original schematic and  pcb design (also for AntiSparkSwitch).

<!-- ![My helpful screenshot](/assets/images/vesc-top-bottom.jpg) -->
<img src="/assets/images/vesc-top-bottom.jpg" alt="drawing" width="400" class="center"/>

If you do not have hot air to solder thermal pad of mosfet driver (DRV8302 - U3) you can smear it with thermal paste and solder only legs on sides, it should work sufficiently for most use cases.


## STEP 3 Programming:

Thanks to courtesy of Benjamin we can download ready to go binaries which we can flash our STM with. Go to **href** GIT repository and download the latest compiled version of VESC firmware (**href** Precise link). Process of programming of STM32 processor is quite easy and can be found i.e **href** HERE.

PROGRAMMING CONNECTOR OBRAZEK

<br>
<br>

## CONCLUSION
In that way we got 3 VESC controllers and a bit of knowledge at a very competetive price. It is not a solution which suits everyone, but I found it the best and hope you are too. 
If you have any comments about this post or you would like to have something clarified more deeply please write below, or contact me directly (contact info!!!!). In future I am going to publish more posts about VESC controller, stay tuned.
