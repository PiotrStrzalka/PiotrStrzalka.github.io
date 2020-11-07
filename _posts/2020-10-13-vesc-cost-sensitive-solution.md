---
layout: post
author: "Piotr Strzałka"
tags: vesc electronic
---
# VESC - cost sensitive solution

Playing with electronic speed controllers can be really enjoyable and frustrating at the same time. I have always had a hard time using custom ESC’s when everything seems to work well and in the next second some tangled wire, or ESD charge kills an important chip or even one crucial I/O pin spoiling all the fun.

Most of the time I am working with a **VESC controller** thanks to the fact that it is open source (in hardware and software) and very popular. Assembled, it costs around 70-100$ so burning it once or twice can discourage further work.

I was facing that problem for a long time, finally it seems that I found a way out and I want to share it with you. The idea is to assemble controllers on your own. Whole process includes ordering pcbs and parts (also a couple which are the most fault prone) and assembling them by hand (it is easier that it seems). **After that we can get 3 pieces of controller with spare parts in the price of one assembled to go VESC.** If you are interested, I invite you to read further.

<br>

<img src="/assets/images/VESC-close.jpg" alt="drawing"/>
<hr/>
## STEP 1 Gathering components:

### PCBS
Currently the best option to order them is to use JLCPCB manufacturer, (**[HERE](https://support.jlcpcb.com/article/21-how-do-i-place-an-order)** is a comprehensive description of how to order boards). IMHO the basic set of pcbs for playing with VESC should contain at least:
- **[VESC main board](https://github.com/vedderb/bldc-hardware)** (we will use version 4.12) (5pcs - 7$)
- **[VESC capacitor board](https://github.com/vedderb/CapPCB)** (10pcs -2$)
- **[AntiSparkSwitch](https://github.com/vedderb/SparkSwitch)** (10pcs -2$)

- **TOTAL:** Boards 11$ + Shipping 5$: 16$

<img src="/assets/images/VESC-boards.jpg"  width="600" class="center"/>

### COMPONENTS
Bill of materials for VESC can be found on **[Benjamin Vedder github](https://github.com/vedderb/bldc-hardware/blob/master/design/BLDC4.12_BOM.ods)**. Here is my version of it which includes parts for 2 VESC boards, 2 capacitor boards and 2 antispark switch. It also contains spare parts which most probably will be burned while playing with custom applications. (I am currently experimenting with replacing mosfets  with cheaper alternatives).

### EXTRA
To play with VESC properly it is a good idea to order also NRF52832 bluetooth module, you can get one of these on eBay or Aliexpress. It allows connecting mobile app to device, controlling, checking state of device, making bridge connection to PC apps and many many more.

PICTURE


Potentiometer, Remote controller or anything else that can be used as a control device.  
Programmer: some ST-link or J-Link (most of ST development boards have ST-Link embedded into)

<hr/>
## STEP 2 Assembly:

For many this seems the toughest part, but it is not necessarily true. To assemble you need to have: Soldering iron, Soldering wire, Flux, Tweezers, Copper braid, Hot air gun or a bit of thermal paste.


I have prepared top and bottom component placement prints for your convenience, you can print it and should easily assemble the whole board with it. Of course you can also install **[Kicad](https://kicad-pcb.org/)** software and look at original schematic and  pcb design (also for AntiSparkSwitch).

<!-- ![My helpful screenshot](/assets/images/vesc-top-bottom.jpg) -->
<img src="/assets/images/vesc-top-bottom.jpg" alt="drawing" width="600" class="center"/>

If you do not have hot air to solder thermal pad of mosfet driver (DRV8302 - U3) you can smear it with thermal paste and solder only legs on sides, it should work sufficiently for most use cases.

<hr/>
## STEP 3 Programming:

Thanks to the courtesy of Benjamin we can download ready to go binaries which we can flash our STM with. Go to **[GIT repository](https://github.com/vedderb/bldc)** and download the latest compiled version of VESC firmware (**[Precise link](https://github.com/vedderb/bldc/blob/master/build_all/410_o_411_o_412/VESC_default.bin)**.  
First of all bootloader needs to be flashed, a compiled version of it can be downloaded from **[HERE](/assets/other/BLDC_4_Bootloader.hex)**
The process of programming of STM32 processor is quite easy and can be found i.e HERE.

PROGRAMMING CONNECTOR OBRAZEK

<br>
<br>

<hr/>
## CONCLUSION
In that way we got 3 VESC controllers and a bit of knowledge at a very competitive price. It is not a solution which suits everyone, but I found it the best and hope you are too. 
If you have any comments about this post or you would like to have something clarified more deeply please write below, or contact me directly (contact info!!!!). In the future I am going to publish more posts about VESC controller, stay tuned.
