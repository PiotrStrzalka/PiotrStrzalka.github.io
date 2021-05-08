---
layout: post
author: "Piotr Strzałka"
tags: [c++, cmake, linux, docker]
---
# Png generator - C++ developer interview task
{:.no_toc}
Some time ago I came up with idea about going to some interviews to check what are current requirements to get a job. After several interviews I got the job, but one interview caught my attention more. I got task to do and show future employer, at first I wasn't to happy to spend my leisure on this, but them I got cought by curiosity.
At first glance, the task wasn't hard, it was all about creating two programs communication with each other, sending data both ways using some IPC (excluding sockets), generating png files, interacting with user etc. I thougt it was be easy, I have done more challenging task in work. But then I realize my issue, at work most of the time the build system is provided by the organisation and well established, moreover topics are frequently repeatable, but not here. I have needed to came up with some build system, libraries, architecture decisions etc. and ... this takes time, a lot of. I of course underestimate the time to do the task, and failed with completing it in right form, but recently [I decided to go back and finish the task fullfiling all the requirements.](https://github.com/PiotrStrzalka/png_generator)

## Table of Contents
{:.no_toc}
* This will become a table of contents (this text will be scrapped).
{:toc}

# Program requirements
The full requirements I got from the company can be seen in [Readme file](https://github.com/PiotrStrzalka/png_generator#readme) .
In short terms it was all about creation of two programs from which one will work as a pipeline to the second, and the second will generate png pictures. Parameters of the pictures can be entered from as input file or CLI command.

# Architectural decisions

## Programming language 
I decided to use C++ as a programming language since I could only choose from C and C++ and with the latter there is more libraries to choose from. For unit test I have used googletest framework.

## Buildsystem
I do not know how CMake gain so much popularity, it is soooo intricate. But still I choose because it is widely used.  
## IPC
I had hard time to decide about IPC path, since socket (obvious choice) is forbidden by req. Finally I have choose unnamed pipes which it turned out for me later I can connect to stdin and stdout of second program, that simplified many things.

## Libraries
For the Command Line Interface I found nice library on [GitHub](https://github.com/daniele77/cli)
Same for png generation there is a library called [pngwriter](https://github.com/pngwriter/pngwriter)

I also used a little of docker magic for ensuring that there is not too much dependency on build host (I can control easily how many additional libraries are needed to build and run program, that was also the requirement).

## Uml overview
<span class="picture-missing">#TODO DESIGN GRAPH</span>


# Interesting snippets
As the [source code](https://github.com/PiotrStrzalka/png_generator) is public available, I would like to mention only about couple of interesting things that I found during development.

## Spawning a new process
## Forwarding streams

## Attaching pipes to stdin and stdout

# Finally
Making that kind of free work seems to be unprofitable, but finally I got profit from it. I learned new things and I had fun doing that :) (maybe the code is not as polished as it should be, but for side project I didn't want to spoil the fun).






