---
title: C128 assembly - Part 1
description: An introduction to assembly on the Commodore 128
draft: false
tags:
  - kick-assembler
  - asm6502
  - commodore-128
  - assembly
---
## Introduction

![[Screenshot from 2018-03-21 20-44-31.png]]
*Looks complicated? Read on!*

All the code and resource files in this post are available on my GitHub page, because it's fun to share.

I want to delve more into the Commodore 128. It's such a nice system. Some of the new and advanced features are not easy to program though. So I decided to create a system, a framework if you will, of re-usable parts once I had figured out the basics.

## Macros

I use [Kick Assembler](http://www.theweb.dk/KickAssembler/Main.html#frontpage), Sublime Text and VICE to develop on my PC.  
  
Using more of Kick Assemblers power was on my to-do list and this is a start. I decided to create easy to use *macros* that will generate specific object code, or make it easy to insert reusable subroutines into object code.

### Setup

First thing to do was to modify the default basic startup macro to use `$1c01`, not `$0801`:

```asm 6502
.macro BasicUpstart128(address) {  
    .pc = $1c01 "C128 Basic"  
    .word upstartEnd  // link address  
    .word 10          // line num  
    .byte $9e         // sys  
    .text toIntString(address)  
    .byte 0  
upstartEnd:  
    .word 0           // empty link signals the end of the program  
    .pc = $1c0e "Basic End"  
}  
```

Then, some handy setup macros. For example:

```asm6502
.macro Go80 () {  
	lda MODE  // are we in 80 columns mode?  
	bmi !+  // bit 7 set? then yes  
	jsr SWAPPER // swap mode to 80 columns  
!:  
}
```

Isn't that nice? Entering `Go80()` beats remembering and typing in that code. I can now also use this macro `DoEscapeCode('X')` which will also switch between 80 and 40 column mode. This is the macro code for that:  

```
.macro DoEscapeCode (code) {  
	lda #code  
	jsr JESCAPE  
}  
```

And it allows easy access to the other escape codes as well.

### VDC

These are one-off calls. They accept parameters and will therefore generate specific code for specific situations, mostly during setup, and they can be added into the code when required. Some code is better added as subroutines though, for example: writing to a VDC register:  

```
.macro WriteVDC () {  
	stx VDCADR  
!:  bit VDCADR  
	bpl !-  
	sta VDCDAT  
}  
```

This can be added as a subroutine to object code like so:  

```
WRITE_VDC:  
:WriteVDC()  
rts
```

It can then be called multiple times, like this:  
  
```asm6502
	ldx #26        // modify register 26, colour.  
	lda #010011    // left nybble is BG, right is FG  
	jsr WRITE_VDC  
```
  
This will change the background and character colour on the 80 columns display.

### Memory Management (Unit)

One of the first things to tackle when using the C128 is the use of the MMU. Especially when mixing Basic and machine code.  
  
Check out this memory map overview. We will get back to it later: 

![[Screenshot from 2018-03-17 14-17-34.png]]
*The Commodore 128 memory map. Fun.*

This can be quite confusing, so I created a macro which will make selecting the proper bank configuration easier, as the values as used in the  `BANK` command can be used, and some custom configuration when I do so choose to add them:  

```
.macro SetBank(id) {  
.if(id==0) {
	lda #111111  // no roms, RAM 0  
}  
.if(id==1) {  
	lda #%01111111  // no roms, RAM 1  
}  
.if(id==12) {  
	lda #000110  // int.func. ROM, Kernal, IO, RAM 0  
}  
.if(id==14) {  
	lda #000001  // all roms, char ROM, RAM 0  
}  
.if(id==15) {  
	lda #000000  // all roms, RAM 0. default.  
}  
.if(id==99) {  
	lda #001110  // IO, kernal, RAM0. No basic  
}  
	sta MMUCR  
}
```

So, adding `SetBank(15)` to my source code will enable the default bank configuration.

## First Test

This is the code for a first test I've made. It sets up some default stuff, and it then fills the 80 column display with a character, and changes the colour of the display.  

```asm6502
#import "c128system.asm"  
#import "c128macros.asm"  
  
:BasicUpstart128(MAIN)  // I like to use : to indicate a macro call.  

MAIN:  
		:SetBank(15)
		:Go80()  
  
		ldx #18                       // register 18 = update address hi  
		lda #$00  
		jsr WRITE_VDC  // write 0  
		inx                           // register 19 = update address lo  
		jsr WRITE_VDC  // write 0  
  
		ldy #0                        // byte counter  
		lda #8                        // page counter  
		sta $fa  

// we write to the data register  
// data will be passed to address defined by 18-19  

		ldx #31
		lda #$66  // we write this char to screen memory  
!:  
		jsr WRITE_VDC  
		iny  
		bne !-                   // byte loop  
		dec $fa  
		bne !-                   // page loop  
  
		ldx #26                  // modify register 26, colour.  
		lda #010011              // left nybble is BG, right is FG  
		jsr WRITE_VDC  
		rts  
  
// assemble sub routines.  
  
WRITE_VDC:  
		:WriteVDC()  
		rts  

READ_VDC:  
		:ReadVDC()  
rts  
```  

That's it for now. I will keep trying new things and post about them.  At this time, I am not sure what I want to program on the C128, but I have a few ideas...

Go on to [[Commodore 128 assembly - Part 2]].

