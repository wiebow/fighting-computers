---
title: C64 development setup in 8 easy steps
description: Setup a small cross-development environment for the Commodore 64
draft: false
date: 2024-02-03
tags:
  - kick-assembler
  - linux
  - sublime-text
  - guide
---
Here's another one of those "Let's write this down for future reference" posts. After re-installing Mint 18.1 there was need of setting up some essential development tools.  
  
Here is how to set up an Commodore 64 assembly development on a fresh Mint install in eight easy steps:  
  
1 - Download **VICE** from the software manager. The executables (`X64`, `X128`, etc) will be added to `/usr/bin`. As this path is already part of your **PATH** variable you do not need to do anything extra for the executables.  
  
2 - Download the [Windows version](http://vice-emu.sourceforge.net/) of VICE, unpack and copy the `C64`, `C128` and `DRIVES` folders in `/home/<username>/.vice/`
  
3 - Download [Kick Assembler](http://www.theweb.dk/KickAssembler/Main.html#frontpage) and unpack it into `/home/<username>/Development/tools/KickAssembler`.  
  
4 - Add the variable **CLASSPATH** to `/etc/environment`, and point it to the FULL path of the `KickAss.jar` file. Use `sudo` to edit this file (only root can edit this file) and add `CLASSPATH="/home/<accname>/Development/tools/KickAssembler/KickAss.jar"` to this file.

5 - Log off and back on.  
  
6 - Install **Sublime Text** from the software manager, and then [package control](https://packagecontrol.io/installation).  
  
7 - Install the Kick Assembler package for Sublime Text.  
  
8 - Load one of the Kick Assembler demo files, compile and do a test run.  

![[kickassembler.png]]

And there you have it, a fresh development setup. It can be extended with a Git client of course.

One of the problems with working on Linux is that most of the content creation tools are Windows only. Next up is an attempt to go and try to see these can work in Wine. If this turns out not to be the case then maybe it's time to develop some native Linux character, screen and sprite editors!

Thanks for now, have fun coding!
