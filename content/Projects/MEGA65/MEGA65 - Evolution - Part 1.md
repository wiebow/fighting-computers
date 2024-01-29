---
title: MEGA65 evolution - Part 1
description: An explanation of the evolution of Commodore CPU's
draft: false
tags:
  - commodore-64
  - commodore-128
  - mega-65
---
## Introduction

What is going on? Well, 8-bit land is in quite an uproar: the [MEGA65](https://mega65.org/) has started to arrive on people's doorsteps. The MEGA65 is a continuation of the 8-bit computer products of Commodore, the once-king of the 80's home computer revolution. It's an open-source community driven project that's been slowly developing for many years now.

![[mega65.jpg]]
*The MEGA65 shown running on a DELL monitor. READY.*

I have followed the progress on and off through the years, but now the MEGA65 is finally available! I have ordered a MEGA65 but it will take some time before it arrives, as my order is part of "batch 3", which is due to be delivered sometime in 2024.

But in the mean time, we can explore all things around the MEGA65. I started reading the manual and a few things stood out for me:

1. There is some essential information missing in the manual, especially concerning *C65* features that are in the system and sometimes referred to. This is because there is copyright on the original C65 texts and documentations.
2. It is assumed that the reader knows about these things. Most people do not, as the C65 was never released :)
3. There are also *C128* features in the C65 (and therefore the MEGA65) which might not be familiar to a lot of people, as those features were not widely used (90% of users started the C128 holding the Commodore key)

There is SO MUCH stuff that is mentioned and referenced in the manual, that the overview is lost (at least for me). Which got me thinking: why is the MEGA65 what it is? What came before it and what was introduced along the way to shape the MEGA65 as we now (get to) know it? Because this would help understand the MEGA65 and put things in context.

My research started and since I am not unique I figured that more people would benefit from this information, so blog posts incoming! I wanted to make one blog post but it quickly became apparent that it would be a very complex and long post, so I decided to split things up.

The post series I have planned will look at CPU, Memory, Video, Audio and I/O changes throughout the main Commodore products that helped shape the MEGA65: The C64, C128, C65 (unreleased), and (funny enough!) the Amiga. Each product introduced features that were kept and sometimes modified for the MEGA65.

Note: Some of the features and numbers in this series can change in the future, as the MEGA65 is in constant development. I will update these posts if something like that happens.  There is a lot of information in these posts: if I have slipped up somewhere along the line, please let me know and I'll fix it! Thanks!

Let's look at item #1 on the list: The *CPU*!

## CPU

First, a table to create a global overview of the changes throughout the years. Yellow means no change from the previous generation, green means improvement:

![[CPU_evolution.png]]
*CPU options evolving from left to right over the years.*

One thing that stands out it that through all the iterations and changes, the address and data bus remain at the same level, obviously. Also, almost **NO FEATURES ARE REMOVED** in a next iteration. If they are, I will mark them in red in coming posts.

As memory amounts change, the CPU got new features to handle that as well, so I mention it here as well. So let's go through it.

### C64  

![C64 hardware](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e9/Commodore-64-Computer-FL.jpg/300px-Commodore-64-Computer-FL.jpg)  
*The classic breadbin*

Our baseline!! The C64 introduced the 6510, which is a slightly modified 6502. It introduced the ROM masking techniques to interact with the various custom chips and ROMs in the C64: VIC II, Basic, KERNAL, etc. which enabled all these chips to co-exist in a 64K address space.

The 6510 was technically capable of running at a higher Mhz clock, but bus sharing with the VIC II forced it to run at about 1Mhz.

### C128

#### ![Commodore-128.jpg](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b1/Commodore-128.jpg/280px-Commodore-128.jpg)
*The follow-up, the C128. The 128D was a much prettier machine, imho*

The C128 8502 CPU improved on this design by adding more speed, but only when the VIC II was not in use (so with bus sharing active). As the CPU still could only address 64K and the C128 had 128K of RAM, there was a new mechanism introduced: *RAM Banking*.  A *Memory Management Unit* (MMU) chip, separate from the CPU, was introduced to make this possible. It allowed the CPU to look at a physically different set (bank) of 64K. It was all-or-nothing: it could only use one RAM bank at any given time. There was an exception: small regions at the top and bottom addresses were always coming from BANK 0, no matter which RAM bank was active. So, essential stuff was located here.

The 8502 also introduced the movable stack and zero page.

### C65  

![A Commodore 65 prototype](https://upload.wikimedia.org/wikipedia/commons/thumb/1/18/C65alleine_%28no_bg%29_%28balance%29.jpg/300px-C65alleine_%28no_bg%29_%28balance%29.jpg)  
*The never to be released C65*

Then came the not-released C65 with a 4510 CPU, and it added quite a few improvements. The 4510 was an evolution from the 65CE02, which allowed it to run at higher speeds, use less current and stay cooler.  

More speed, of course, and usable in all video modes as opposed to the C128. Note: the C65 also inherited lots of features (like the 80 column video mode) from the C128 VDC video chip, but we'll get into that in a later post.

The system address space was enlarged to 1MB, and the C128 MMU was improved (and renamed to *Memory Mapper*), and moved to the CPU making the 4510 a *System In a Package* (SIP). This trend will continue with the MEGA65 CPU, later on. The memory mapper can map PARTS of memory to other physical locations, making it more flexible (and potentially more complex) than the banking mechanism introduced with the C128. The banking mechanism is also still in place in the C65.

There is also a DMA (Direct Memory Access) controller, called *DMAgic*, introduced. More information about these methods will follow in the _Memory_ part of this series, we limit this post to the CPU features.

Another CPU improvement was the introduction of an additional index register: the `.Z` register. This allowed for more efficient programming. Additional opcodes where introduced to enable the use of this new register.

The 4510 also introduced the 16bit stack, allowing it to grow to larger sizes than a single memory page.

### MEGA65

![[mega65_close.jpg|400]]
*Close-up of the MEGA65.*

The MEGA65 continues the SIP trend and moves more components into the CPU. More on that in other posts which will cover those specific component territories. The chip is now called a **45GS02**.  

Of course, more speed. Going to 40.5Mhz will be huge!  

System address space is increased to 28 bit, making 256MB coverage possible. For this to work with an 8 bit processor we can use several new methods:

1. Flat-Memory Access is added by using a 32bit pointer located on the zero page.
2. Another major addition is the Q register which is a virtual register that combines A,X,Y and Z to a 32bit register.
3. The *map* method from the 4510 is extended so it can reach over the 1MB limit of the C65.  

The DMA controller (DMAgic), introduced in the C65, was now moved into the SIP for the MEGA65, and improved. More information about these methods will follow in the _Memory_ part of this series, we limit this post to the CPU features.  

Remember, there also remains the banking mechanism to access memory in other memory banks. What the CPU actually sees is the result of all these methods combined and of priority.

Hardware 32 bit multiplier and divider registers are added to speed up these essential math operations.  

Also introduced in the 45GS02 are different operating modes: Supervisor and Hypervisor. These are used to support the different modes the MEGA65 itself can operate in. These modes take the machine out of the normal user environment to run custom tools and commands. Hypervisor mode can be started using different methods from the user mode.

## Wrap up

Pfew. Some really nice improvements throughout the years on these CPU's. A lot is added and almost nothing has been removed which makes the MEGA65 a very complete machine. This also adds to the complexity for new users and when I started reading about the MEGA65 it made my head spin because of all the references to the older features, some of which are not even (officially) documented.  This will be the trend through all the posts in this series.

Also, not every feature coming from these older CPU's is updated or completed to use in the MEGA65 mode. Time will tell which are the preferred ways of making use of these features.

The next post will dive into the evolution of the use of memory and its methods of access.  Read it here: [[MEGA65 - Evolution - Part 2]]. Thanks for reading, and please let me know of any errors, or suggestions for improvements!