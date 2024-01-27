---
title: Assembly style
draft: false
tags:
  - assembly
  - guide
---

This post explains how I style 6502 assembly language and it will also document the most common patterns.

## Source-code layout

### Lines

* a line is max 80 characters wide.

### Instructions

* instructions are in lowercase.
* instructions are tabbed 16 chars to the right.

### Labels

* labels do not override the instruction space.
* labels are on the same line as the first instruction.

### Comments

* comments explaining a block of code are positioned at the beginning of the line.
* comments explaining an instruction are positioned after the instruction


This results in the following style:

```asm6502
// this is a codeblock explaining
// the following code block in a general way.

				lda #01
				sta $d020

				ldx #$00          // reset string index
!:				lda string,x
				beq done
				jsr $ffd2
				inx
				jmp !-
				
done:			jsr PRIMM
				.text "THIS IS IMMEDIATE PRINT."
				.byte 0
				jmp *

string:			.text "HELLO WORLD"
				.byte 13, 0
```




## Common patterns

The following items are translations from higher-level language patterns to low-level machine code. It's a helpful guide to maintain code similarity.

### If-then-else

```asm6502
				lda #10
				cmp #15
				bne else
				# if code
				jmp ifdone
else:   # else code

ifdone: # rest of program
```





