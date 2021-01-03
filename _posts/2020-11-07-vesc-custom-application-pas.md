---
layout: post
author: "Piotr Strzałka"
tags: [vesc, friction-drive, electronic]
---
# VESC - Creating a custom application for PAS sensor
{:.no_toc}

-- POST UNDER DEVELOPMENT --  (INITIAL IDEA OF TRACKING HIGH and LOW LEVEL ratio seems to not work to flawlessly. Another approach which involes encored like solution is required.)
VESC is a really powerfull platform in terms of software comparing to other solutions that you can find on the market. Configuration that you can do through dedicated PC and smartphone apps is quite extensive, but for me the real power lies is capability of writing own application on that platform. I used that power to write custom Pedal Assist Sensor component used in my electric bike drive train project.

And trust me, as for now there is no better alternative. Once I had decided to make research for a different platform, I have tried to use ST branded Motor Control Workbench with dedicated development boards and after several afternoons I gave up. It was just so annoying to deal with some not fully tested closed source code generator when things happen just randomly.

## Table of Contents
{:.no_toc}
* This will become a table of contents (this text will be scrapped).
{:toc}
# Pedal Assist Sensor - what is all about

Pedal Assist Sensor (PAS) - is a device that can be mount on the crank of a bicycle which gives you information about the current movement of pedals. (**[HERE](https://ebikes.ca/learn/pedal-assist.html)** you can find a broader description of sensors.
)

I got a bunch of Varied width sensors which requires one wire to connect to uC. The signal that can be read looks like below:

![PAS graph](/assets/uml/pas-signal-graph.png)


# Ok - but where to put my application "main function"?

This question has been answered in Benjamin Vedder post **[Writing custom application](http://vedder.se/2015/08/vesc-writing-custom-applications/)** . There is a comprehensive description where to put your logic into the code.

# Integration with RTOS and other system functions

**[ChibiOS](https://www.chibios.org/dokuwiki/doku.php?id=chibios:documentation:start)** is really neat Real Time Operating System and working with it is a pleasure thanks to good documentation and wide support community.  

To implement our PAS functionality within ChibiOS and VESC "framework" I took approach as follows:

- #### Transitions **Hi->Low** and **Low->Hi** can be detected by one of the pins using hardware interrupts.
What is worth to mention is that we need to set internal pull-up for pin that we are going to use for  reading sensor data (or maybe even better to add some 10k pull-up resistor, without it sensor does not work, I catched myself on making that mistake) 
- #### Example of interrupt configuration

 Configuration involves pin definition, setting Line, Mode, Trigger etc. and enabling the interrupt itself
``` c
// PAS sensor pin and interrupt definitions
#define HW_PAS_SENSOR_PORT	        GPIOA
#define HW_PAS_SENSOR_PIN 	        6   
#define HW_PAS_SENSOR_PORT_INT	    EXTI_PortSourceGPIOA
#define HW_PAS_SENSOR_PIN_INT 	    EXTI_PinSource6
#define HW_PAS_SENSOR_EXTI_CH       EXTI9_5_IRQn
#define HW_PAS_SENSOR_EXTI_LINE     EXTI_Line6
#define HW_PAS_SENSOR_EXTI_ISR_VEC  EXTI9_5_IRQHandler

void app_pas_sensor_init(void)
{
    ...
    palSetPadMode(HW_PAS_SENSOR_PORT, HW_PAS_SENSOR_PIN, PAL_MODE_INPUT_PULLUP);
    
    SYSCFG_EXTILineConfig(HW_PAS_SENSOR_PORT_INT, HW_PAS_SENSOR_PIN_INT);
    EXTI_InitTypeDef   EXTI_InitStructure;
    // Configure EXTI Line
    EXTI_InitStructure.EXTI_Line = HW_PAS_SENSOR_EXTI_LINE;
    EXTI_InitStructure.EXTI_Mode = EXTI_Mode_Interrupt;
    EXTI_InitStructure.EXTI_Trigger = EXTI_Trigger_Rising_Falling;
    EXTI_InitStructure.EXTI_LineCmd = ENABLE;
    EXTI_Init(&EXTI_InitStructure);

    nvicEnableVector(HW_PAS_SENSOR_EXTI_CH, 2);
    ...
}

// PAS sensor input Interrupt Handler
CH_IRQ_HANDLER(HW_PAS_SENSOR_EXTI_ISR_VEC){
    ...
    }
}

```
- #### Within this interrupt current **pin state** and **timestamp** is saved and send to task via message queue (here is called mailbox).

Configuration of mailbox system seems tricky at first glance, but after all it is one buffer with two pointer tables, one which shows free spots, and second occupied ones.

``` c
typedef struct {
    uint32_t timestamp;
    uint8_t pin_state;
} pas_edge;

static pas_edge buffers[NUM_BUFFERS]; 
static msg_t pas_events_queue[NUM_BUFFERS];
static mailbox_t pas_events;
static msg_t pas_events_queue_free[NUM_BUFFERS];
static mailbox_t pas_events_free;

void app_pas_sensor_init(void)
{
    chMBObjectInit(&pas_events, pas_events_queue, NUM_BUFFERS);
    chMBObjectInit(&pas_events_free, pas_events_queue_free, NUM_BUFFERS);

    for(uint32_t i = 0; i < NUM_BUFFERS; i++){
        (void) chMBPostI(&pas_events_free, (msg_t)&buffers[i]);
    }
    ...
}
...

// interrupt handling
CH_IRQ_HANDLER(HW_PAS_SENSOR_EXTI_ISR_VEC){
    if(EXTI_GetITStatus(HW_PAS_SENSOR_EXTI_LINE) != RESET){
        void *pbuf;
        chSysLockFromISR();
        uint8_t pinLevel = palReadPad(HW_PAS_SENSOR_PORT, HW_PAS_SENSOR_PIN);
        EXTI_ClearITPendingBit(HW_PAS_SENSOR_EXTI_LINE);
        if(chMBFetchI(&pas_events_free, (msg_t *) &pbuf) == MSG_OK){
            ((pas_edge *) pbuf)->pin_state = pinLevel;
            ((pas_edge *) pbuf)->timestamp = (uint32_t) chVTGetSystemTimeX();
            (void)chMBPostI(&pas_events, (msg_t) pbuf);
        }
        chSysUnlockFromISR();
    }
}
```



- #### Calculation of High/Low state will need some context to do it in (we cannot do it in interrupt, holding interrupt procedure can disturb other more important interrupts and calculations), so another task is required.



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

- #### After that I made calculations of signal frequency, Td/Tu ratio, I also made a basic signal interpretation. After that I draw a plot in VESC application but the results were bad, I couldn't distinguish between movement forward and backward when movement was too slow or changed frequently, and the resolution was too bad to detect movement fast enough for my needs.

So I decided to add one hall sensor to circuit and make this detection to work like encoder.

# Encoder soultion.