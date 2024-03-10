---
title: Review 1541 Ultimate II
description: A review of the 1541 II Cartridge for the Commodore 64
draft: false
date: 2024-03-10
tags:
  - commodore-64
  - hardware
  - reviews
  - 1541-ultimate-2
---

> [!NOTE]
> This review was originally posted in 2017.

Recently I bought the new version of the 1541 Ultimate II cartridge. It is the best investment you can make when you enjoy your Commodore 64 and want to play around with games, programming, music playback and hacking.  
  
The cartridge came with the 3.1 firmware installed, which is not available for download on the [official site](http://www.1541ultimate.net/content/index.php). Also included are a serial cable and a branded USB stick. Nice. 

![[1.jpg]]
  
The case is a bit bigger than the first U2, which is needed to house more USB ports, audio connectors, a network port and micro USB power connector.  The case is a really nice quality, with a big logo on top and smooth finish.

![[2.jpg]]
  
The blue USB3 port is not a USB3 port in the traditional sense, but used for the Tape connector.  

![[3.jpg]]

## Features

A lot of features have stayed the same as the Ultimate 2. For an overview of the capabilities of the earlier version of the cartridge, see my series of posts, starting [here](https://devdef.blogspot.nl/search/label/1541%20Ultimate%20II).  

## Cartridges

I was really happy to see the [KCS Power Cartridge](http://ar.c64.org/wiki/Power_Cartridge) in the list of supported images. I (and many Dutch people with me) used this cartridge, because of the monitor and the floppy disk turbo load features.  I never used the freeze capabilities, as I found it cumbersome.  
  
Another new cartridge is the [GeoRAM](https://en.wikipedia.org/wiki/GeoRAM). It was a memory expansion, usable with Geos. It is slower than the REU, and its use is limited. I'll probably never use it.  

## Amiga Mod Audio Playback

This is really fun, and it surprised me.  
  
A new feature is the playback of Amiga mod files. All you need to do is load up the .mod file to your USB stick, and select "play MOD" from the context menu. You must connect the audio out connector to a amplifier though.  But it's good fun to play classic mod files on the C64. You can press the function keys to enable/disable voices.  
  
![[4.jpg]]
  
Now I need to find some classics! Any pointers?  

## Network Features
  
It is now possible to operate the U2+ through the network. The network port is configured, by default, to use DHCP. When you connect a RJ45 cable to the cartridge, you will see the IP address you need to connect to in the root of the file system (the network is displayed the same as a USB stick).  
  
Use a telnet client to do this, and it will look like this:   

![[5.png]]

It's handy to be able to browse the cartridge file systems for a new file to play while the SID or MOD file keeps playing as you browse.  

![[6.png]]

What I did find is that the `ESC` key (close a configuration screen for example) does the same as pressing `RUN-STOP` on the C64, but the screen does not reflect this. Pressing an arrow key afterwards does refresh the telnet screen.  

![[8.png]]
  
I have not tried to FTP files to the cartridge using the network as this is REALLY slow.  
Gideon mentions that the FPGA in the U2+ is not (yet, hopefully) running on full speed, and is even slower than the U2. I did not get any further than this:  

![[7.png]]

## Stand Alone Mode
  
As the cartridge can be powered using a micro USB cable, it is now possible to run it without a C64.  Also connect it to the network and you can Telnet to it, but there is not much else you can do in stand alone mode as audio playback does not work (You need a C64 to run the module or SID playback programs)  

## Quality of Life stuff

It is now possible to select a folder on the file system and set it as a HOME directory. The cartridge can go to this folder on start up, and/or you can go to it by pressing the Home key. Really handy if you are tired of scrolling through the HSVC folders to your favourite musician :)  
  
It is also possible to set the device ID that is used by DMA load. This can REALLY help with multi-load games.  

## Conclusion

Lots of new additions made this cartridge even better than the already impressive original 1541 Ultimate 2.
  
The hardware features promise more exciting possibilities further down the road. I really hope RR-net functionality is coming, because BBSes baby! And also using the turbo assembler cartridge with it.
  
If you like your C64, and you use it on a regular basis, then this cartridge is a GREAT add-on to enjoy it even more. Highly recommended!!
