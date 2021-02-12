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

<img src="/assets/uml/fdrive-system-view.png" alt="pas-signal-graph" style="width: 500px" class="center"/>
## Battery

## Controller
That part took huge amount of time:


| Controller                        | Prons         | Cons  | Price |
| -------------                     |:-------------:| :-----:| ------:|
| **Generic BLDC<br>controller**    | easy access<br> low price<br> works with sensorless | $1600 |       |
| **STM32ESC1**                     | centered      |   $12 |       |
| **SC06**                          | are neat      |    $1 |       |
| **VESC**                          | are neat      |    $1 |       |

## Motor

## Mount

## Pedal Assist Sensor


# Project future
