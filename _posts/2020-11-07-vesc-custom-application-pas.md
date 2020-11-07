---
layout: post
author: "Piotr Strzałka"
tags: [vesc, friction-drive, electronic]
---
# VESC - Creating a custom application for PAS sensor

-- POST UNDER DEVELOPMENT --  
VESC is a really powerfull platform in terms of software comparing to other solutions that you can find on the market. Configuration that you can do through dedicated PC and smartphone app is quite extensive, but for me the real power lies is capability of writing own application on that platform. I used that power to write custom Pedal Assist Sensor component used in my electric bike drive train project.

And belive me, as for now there is no better alternative. Once I had decided to make a research for other platform, I have tried to use ST branded Motor Control Workbench with dedicated development boards and after several afternoons I gave up. It was just so annoying to deal with some not fully testes closed source code generator when things happend just randomly.

## 1. Pedal Assist Sensor - what is all about

Pedal Assist Sensor (PAS) - is a device that can be mount on the crank of bicycle which gives you an information about current movement of pedals. (**[HERE](https://ebikes.ca/learn/pedal-assist.html)** you can find broader description of sensors.
)

I got bunch of Varied width sensors which requires one wire to connect to uC. The signal that can be readed looks like below:

![PAS graph](/assets/uml/pas-signal-graph.png)


## 2. Ok - but where to put my application "main function"?

This question has been answered in Benjamin Vedder post **[Writing custom application](http://vedder.se/2015/08/vesc-writing-custom-applications/)** . There is a comprehensive description where to put your logic into the code.

## 3. Integration with RTOS and other system functions

**[ChibiOS](https://www.chibios.org/dokuwiki/doku.php?id=chibios:documentation:start)** is really neat Real Time Operating System working with it is a pleasure thanks to good documentation and wide support community.  

To implement our PAS functionality within ChibiOS and VESC "framework" I took approach as follows:

- Transitions Hi->Low and Low->Hi can be detected by one of the pins using hardware interrupts.
- 
 From RTOS I will need a task because calculation of High/Low state will need some context to do it in (we cannot do it in interrupt from ) 

For my purpose I have created a task (here is called **thread**) and reserved space of 2048 Bytes for its working area (in different words **stack**).
``` c
...
static THD_FUNCTION(pas_sensor_thread, arg);
static THD_WORKING_AREA(pas_sensor_thread_wa, 2048);

void app_pas_sensor_init(void)
{
    ...
    ...

    chThdCreateStatic(pas_sensor_thread_wa, sizeof(pas_sensor_thread_wa),
        NORMALPRIO, pas_sensor_thread, NULL);
}

static THD_FUNCTION(pas_sensor_thread, arg)
{
    ...
}
```




## 4. Application example - Pedal Assist Sensor detector

## 5. Write your own