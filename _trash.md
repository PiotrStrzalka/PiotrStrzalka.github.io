


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

# Encoder solution.

Suprisingly there are no widely available Pedal Assist Sensors with two hall sensors embedded able to detect precisely movement in both directions. So I decided to simulate it by adding hall sensor from another PAS. Result looks like below, diagram and photo of my test harness.

picture


Rotation in both directions are show on next picture, it is photo taken on oscilloscope which shows rotation signal and rotation change signal. Overall it looks like typical encoder signal, maybe slopes distances are less regular due to inprecise position of sensors and I think detection in both directions is not fully symmetrical. To get more info about encoders you can check e.g. **[Encoder](https://howtomechatronics.com/tutorials/arduino/rotary-encoder-works-use-arduino/)** 


### Encoder support in STM32F4
Fortunately support for encoder is embedded in STM32F4 timers and even more support is given by VESC system itself, so setting up this whole thing takes only a minute.

I connected "encoder" outputs to pins to STM32 Pins B6 and B7, external pullup is already on board so there is no need for setting up internal pullups.
My implementation can be found in file **[app_pas_encoder.c](https://github.com/PiotrStrzalka/bldc/blob/friction_drive/applications/app_pas_encoder.c)**

Initialization takes only a couple of lines:
``` c
#define ENCODER_RELOAD_VALUE        1000

void app_pas_encoder_init(void)
{
...
    encoder_init_abi(ENCODER_RELOAD_VALUE);
...
}
```

After that value interesting for us can be read from timer CNT register
``` c
uint32_t encoder_position = HW_ENC_TIM->CNT;
```

That value contains direction detecion, but it doesn't detect counter overload which can disrupt our calculations, it needs to be taken into account when countning position delta. Function **calculate_encoder_delta** takes care about it.
``` c
static int calculate_encoder_delta(uint32_t previous_value, uint32_t actual_value)
{
    int delta = actual_value - previous_value;

    if(delta < -(ENCODER_RELOAD_VALUE/2))
    {
        delta = delta + (int)ENCODER_RELOAD_VALUE;
    }
    else if (delta > ENCODER_RELOAD_VALUE/2)
    {
        delta = delta - ENCODER_RELOAD_VALUE;
    }
    return delta;
}
```

Main cyclist pedal movement is determined in function **calculate_action_from_position**. That function is called every 10ms from task and containes state machine to preserve previous state. It also makes some simple averaging and sends signal to listeners (by use of events) about full turn back detection (further it will be used for arming and disarming the controller). I think there is no point to write more about it, small diagram and code in c should explain everything clearly.
