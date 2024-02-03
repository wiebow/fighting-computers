---
title: Tetris in 6502 - Part 6
description: Adding scores and counting to the game.
draft: false
tags:
  - commodore-64
  - kick-assembler
  - asm6502
  - tetris
date: 2024-01-30
---
## Score time!
  
Welcome back to this series of programming the C64 to behave like a Gameboy! This post was funny to write and to code because working with numbers is so easy on today's computers and I had to do a little research on how to do all this number stuff properly in assembler.
  

> [!NOTE]
> As a reminder: all source is on my [Github page](https://github.com/wiebow).  

  
We use a 24 bit number to hold the score value. This will enable us to have a maximum score of 999999, each byte holding two numbers from 0 to 9. For the math, we use the decimal mode of the 6502. It's not used often but for arithmetic it's pretty neat.  
  
So here we have the definition of the numbers:

![[48.PNG]]
  
The 6502 uses [Little Endian](http://en.wikipedia.org/wiki/Endianness), which means that the least significant byte is stored first. So in the above, the first byte of score represents the value 0-99. The second byte 100-999, and the third byte 1000-9999.  Addition are the bytes we use to add points to the score.  
  
Let's create a function to add the value in addition to the score:  

![[47.PNG]]

The value to add has to be put in the addition bytes before calling this routine. `SED` sets decimal mode. We then clear the carry bit and add the first addition byte to the first score byte. A overrun will set the carry bit, which will cause the carry to be added to the next `ADC` command, and so on for the third byte. We clear decimal mode at the end to stop the rest of the program to keep working in this mode.  
  
Now we need to print the score as well. Here is the subroutine:  

![[49.PNG]]

We use the KERNAL routine at `$ffd2` (chrout) to print the numbers onto the screen, after setting the X,Y position of the cursor using the other KERNAL routine at `$fff0` (plot). This is a different approach than storing data into the screen memory as we have been doing for all the screen related code so far.  
As the number is stored with the least significant byte first and we want to start printing with the most significant value, we move back through the bytes instead of up.  
  
Each byte holds 2 digits of the score value, each in 4 bits. To filter these out so we get to the value, we shift the bits to the right. We now have a value from 0 to 9. We add `#$30` to make a screen code from this and we print it. Then we do the same, but for the 4 bits which are already in place. We only need to clear the top 4 bits here.  

Printing the score might result in it being in a different color. We need to tell the C64 to print using light green. Here is the code I added to the setup part of the main program:  

```asm6502
  lda #153  
  jsr chrout  
```  

That wasn't too hard. 153 is the chr$ code for light green. See page 380 of the _Commodore 64 Programmer's Reference Guide_. :)

## Scoring mechanism

We are going to use the Gameboy Tetris way of keeping score. There is a nice wiki page on that subject [here](http://tetris.wikia.com/wiki/Scoring). We need a way of calculating the score. The play level has to be taken into account and the number of lines made.  
  
There's an array for that!  

![[50.PNG]]

If you look at it from the top down, you see that the score for making one line is 40. The score for two lines is 100, and so on. The top bytes are the least significant bytes, the middle are the middle bytes, and the bottom row are the most significant bytes.  
  

> [!NOTE]
> We explicitly state that 40 is the hex number `$40`, so this is what is being assembled. If we just use 40, this will get assembled as `$28`, which is not what we need for decimal mode!

Then, when a player make a line (or more) the following code is run:  

![[51.PNG]]

First, we look at the amount of lines made, and we fill the addition bytes with the values from the array. After that, the score is added, once per player level.  
  
PS: I added `currentLevel` as a byte definition in the main file.  
  
As an addition, we add a little bit of code to the input part of the source code to reward the player for using the down button:  

![[52.PNG]]

Yes, one big point for moving blocks down faster than the delay. :)  
  
Let's try this. I've put the player level at 1 here. So making a one liner will give me two times the value for a single line, which is 80:  

![[53.PNG]]

OK, after making the line, we should have 170 points:  

![[54.PNG]]

Result! This really adds meaning to the game. Wow.  

## Wrapping Up

Storing, adding to and printing scores in assembly language is not as easy as it is in high level languages. But remember, inside your computer, number work is being done in roughly the same way as we did here. This is a nice way to remind us of how far we've come, people :)  

See you later at the next part, where we will introduce some small bits to flesh out the game-play: player levels and showing the next Tetris block in advance. We also will take a look at the control scheme. All this in [[Tetris in 6502 - Part 7]]
