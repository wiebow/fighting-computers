---
title: Assembly style
draft: false
tags:
  - assembly
  - guide
---

This post explains how I style 6502 or 45gs02 assembly language.
In addition, standard patterns translated to assembly can be found in [[Patterns in assembly language]] .

## Layout

### Instructions

* instructions are in lowercase.
* instructions are tabbed 16 chars to the right.

### Labels

* labels do not override the instruction space, max length is 14.
* labels are on the same line as the first instruction.

### Comments

* comments explaining a block of code are positioned at the beginning of the line.
* comments explaining an instruction are positioned after the instruction

>[!Example]
>```asm6502
>// this is a comment explaining
>// the following code block in a general way.
>				lda #01
>				sta $d020
>				ldx #$00          // reset string index
>!:		        lda string,x
>				beq done
>				jsr $ffd2
>				inx
>				jmp !-
>				
>done:		jsr PRIMM
>				.text "THIS IS IMMEDIATE PRINT."
>				.byte 0
>				jmp *
>				
>.string:            .text "HELLO WORLD"
>				.byte 13, 0
>```

## Subroutines

* must explain:
	* **function**: one or two words naming the function
	* **input**: which registers must be set, and what do they contain?
	* **output**: which register is set with what return information?
	* **destroys**: which registers are destroyed when running this routine?
	* **calls**: which other routines are called from this one?
	* **description**: more in-depth explanation of the function.

>[!Example]
>```asm6502
>// ***************************************
>// * FUNCTION: Initialize Interrupts
>// * INPUT: rasterline to trigger interrupt in .A
>// * OUTPUT: none
>// * CALLS: none
>// * DESTROYS: .X, .Y
>// * DESCRIPTION: sets up a raster interrupt at passed line
>```

