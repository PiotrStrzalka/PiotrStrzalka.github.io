---
layout: post
author: "Piotr Strzałka"
tags: [PROJECT f-drive, electronic, 3d-printing]
---
# F-drive e-bike conversion system
{:.no_toc}
The idea to create friction drive system has been born in my head a couple of years ago. I wanted to check how it is to ride an electric bike, but back then I did not have access to any. Upgrading my bike to be fully electric was not an option since I wanted to keep it as untouched as possible.

Then I found a friction drive solution and after some doubts about efficiency I started to create my own version of it. And here we are a couple of years later.. with almost finished system.


{% include image.html url="/assets/images/f-drive-whole.png" description="Fdrive mounting position" class="center" %}
<!-- <span class="pict ure-missing"> SOME PICTURE MISSING </span> -->

## Table of Contents
{:.no_toc}
* This will become a table of contents (this text will be scrapped).
{:toc}

# How it works
The principle of a friction drive relies on pressing down roll to the tire to pass the drive. To simplify the system a little instead of a roller (which supposedly is powered by some tension belt) we can use a brushless outrunner motor where the rotator can act as a roller.


In this friction drive implementation to connect and hold motor attached to the tire is used torque produced by rotor. To make this solution works flawlessly a lot of tries and iterations were required (both in mounting arm design and software adjustments), but I am more than happy with final result.

{% include image.html url="/assets/images/f-drive-connected.png" description="Motor connected to the tire." class="center"%}

{% include image.html url="/assets/images/f-drive-disconnected.png" description="Motor disconnected from tire." class="center" %}

Comparing to traditional e-bike systems disadvantages of fdrive are obvious: less efficient use of energy, faster tire wear and a problem with wet conditions. But before reject the idea advantages also need to be listed: 
- **little modification in bike**
- **fast and simple mount and dismount**
- **no motor resistance during normal biking** (motor then is completely disconnected from tire)

<!-- <img src="/assets/images/f-drive-whole.png" alt="f-drive-whole" class="center"/> -->

<!-- <img src="/assets/images/f-drive-connected.png" alt="f-drive-connected" class="center"/> -->

<!-- <img src="/assets/images/f-drive-disconnected.png" alt="f-drive-disconnected" class="center"/> -->





# System overview
System has been splitted into three sections. On consists elements that have to be mounted on bike frame, and two (battery, motor with controller) that are easily removable.
The goal was to enabled option when couple of bikes can have Embedded part mounted on and user can switch removable parts between them (I someone have i.e. road bicycle and cross bicycle and want to buy only one set).

Details and connection scheme can be seen on diagram:

{% include image.html url="/assets/uml/fdrive-system-view.png" description="System components connection diagram" class="center" %}

<!-- <img src="/assets/uml/fdrive-system-view.png" alt="pas-signal-graph" style="width: 500px" class="center"/> -->
## Battery

I have made battery by connecting 50 individual 18650 cells in 10S5P configuration. Each cell has capacity of 2750mAh. So the total capacity of battery will be:

```
Capacity = (3,7V * 2,75Ah) * 50 = 508,75 Wh 
```
Taking into account that I live in Europe where law allows to use max 250W and speed up to 25km/h that should give about 2h work on full throttle. Lets say that efficiency is about 80% so estimated range will be:


```
Range ~= ((508,75Wh / 250W) * 25km/h ) * 0,8 = 40,7km
```
But in practice range is much better due to fact that in Europe cyclist cannot use electric motor exclusively, it can only act as a support. 


Battery got also BMS system to balance charger level of each section. Whole battery is packed to one of universal battery cases.

<span class="picture-missing"> SOME PICTURE MISSING </span>


## Controller
That part took huge amount of time, I got information on some forum that generic BLDC controller are ok, but I have never been happy with them. Some generates too much heat, some too much noise, others were lacking of documentation to configura them properly. Chinese S06P was close to be the right one but I haven't possibilty to modify firmware for special friction drive and my pas sensor needs.

I also gave a try to ST motor controller evaluation boards but the SDK was to immature at that time. Then I saw on the shelf dusted VESC controller that I used for e-skateboard project long ago and it fits my needs perfectly.

I have made small table to compare controller candidates.


| Controller                        | Prons         | Cons  | Price |
| -------------                     |:-------------:| :-----:| ------:|
| **Generic BLDC<br>controller**    | easy access,<br> low price,<br> cheap, small | Too loud, Too much heat, closed source |   ~15$    |
| **STM32ESC1**                     | Open sourced,<br> FOC possible,<br> small|  poorly maintained SDK an PC tools |   ~35$    |
| **SC06**                          | rigid enclosure,<br> nice cooperation with outrunner      |    closed source,<br> lack of proper documentation,<br> big enclosure |   ~30$   |
| **VESC**                          | Open sourced, big community,<br> good PC tools, great FOC performance      | ready to go with rigid enclosure can be expensive |  ADDRESS TO OTHER POST  ~25$   |

<span class="picture-missing"> SOME PICTURE MISSING </span>
## Motor

## Mount

## Pedal Assist Sensor

# Test fixture


# Project future
