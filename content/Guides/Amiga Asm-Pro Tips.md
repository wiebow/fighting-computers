---
title: Amiga Asm-Pro Tips
description: A guide to configure Asm-Pro on the Amiga
draft: false
tags:
  - amiga
  - asm68000
  - assembly
  - guide
---
## Introduction

After reading about the MC68000 I decided to type in small listings from books and magazines to get a feel for the assembler and the new language I want to learn. I am a dummy when it comes to **Asm-Pro**. These are the tips I can give you after playing with it for a few hours.  

## Preferences
  
I recommend you set these preference options:

  ![[asm2.png]]

Enabling **Label :** means that a label must be defined by putting a `:` behind it. This will prevent lines like

`CLsize = CLend - CLstart`

being interpreted as a label definition. Also, declaring a value like that will now be recognized as an error. You cannot have spaces in declarations, so the correct line should be:  

`CLsize = CLend-CLstart`
  
where `CLend` and `CLstart` are labels.
  
Setting **UCase = LCase** will put an end to case sensitive behaviour with labels and declarations. Maybe I will turn this back off later on, but for now it is preventing me from having too many errors while compiling :)  
  
**; Comment** forces you to use a `;` before adding a comment. This is again to get rid of possible weird behaviour due to user errors (read: typos).  

## Labels and Even
  
When you need to align declarations to an even word in memory, like when declaring copper lists, you use the keyword even. Do NOT put this keyword after the label declaring your data as in this example:

![[asm3.png]]
  
This will NOT work. Do it like this:

![[asm4.png]]

This may be old news to you, but I spent 45 minutes trying to solve this little issue, as the assembler does not report an error, but this copper list will not work though.  

## Debugging
  
Top tip: a quick way to assemble and enter the debugger in one go is to use the command `ad`.

With the monitor you can look at the Amiga's memory. The monitor is started with the command `h <label>`. A handy thing to do is to use `h.w <label>` and then the monitor displays the memory in words, instead of bytes. As program code is always comprised of words, this makes it easier to 'read' the monitor output:

![[asm5.png]]

If you can call that easy. I hope I will not have to use it to debug stuff...  The dis-assembler allows you to look at the compiled program in a more friendly way. Use `d <label>` to check out the program:
 ![[asm6.png]]

## Assembling

One handy assemble option is **optimize**. You can use the command `ao` and the assembler will look at your branches: if a branch offset fits in a byte (meaning the branch is less than 128 memory locations, it will change your instruction to a `.b` variant, so `bne` will become `bne.b` and so on. This will save two bytes and two cycles, per branch. Yay!  

OK, that's it for now. I am currently following the [Scoopex tutorials on YouTube](https://www.youtube.com/channel/UC1lfCoAuwbQ22H-KoImEygg). They are a great resource, and after reading some 680000 books they are a great introduction to Amiga hardware programming. Fascinating. Highly recommended!