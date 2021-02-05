---
layout: post
author: "Piotr Strzałka"
tags: [vesc, PROJECT f-drive, electronic, software-architecture]
---
# VESC - fdrive application
{:.no_toc}
Long time ago I have started creating a e-bike conversion kit, based on friction drive principle. I found VESC to be suitable to act as a motor controller. Mechanics of this solution is decribed in another post CLICK<todo>, check this out before going further in text. 
Component described in here is also connected with **[PedalAssistSensor](_posts/2020-11-07-vesc-custom-application-pas.md)**, (it uses signals from PAS component).

-- POST UNDER DEVELOPMENT -- 
## Table of Contents
{:.no_toc}
* This will become a table of contents (this text will be scrapped).
{:toc}

# Architectural view

During my professional work I found diagrams very concise communication path. Even though this project is develop only by me in my spare time, creating a couple diagram speed up work and gives more joy of it.

## Main f-drive state machine


Following state diagram shows strategy I chose to effectively attach motor to the tire.

![PAS graph](/assets/uml/fdrive-app-sm.png)

In general The are two main state Drive_Armed and Drive_NotArmed switched by PAS TurnBack Signal. According to that short sound signal "bip" is generated by motor coils to give user feedback about system state.

Composite state Drive_Armed defines strategy of attaching motor to the tire, as not go to the details, only to say that PAS component is continously polled about crank rotation state and if it is forward it tries to keep motor attached.

## Component position in VESC system

The right place to fit new app_fdrive component is within Application level.

<img src="/assets/uml/vesc-component-view.png" alt="vesc-system-f-drive"  class="center"/>


Component need to interact with Service layer, It's shown by following component diagram.
