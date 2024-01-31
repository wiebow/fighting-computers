---
title: Tetris in 6502 - Part 3
description: Adding a playfield and custom characters
draft: false
tags:
  - commodore-64
  - kick-assembler
  - asm6502
  - tetris
  - graphics
---
Hi there and welcome to the third part, let's get going!  
  
We're now able to move and rotate the player block. So now we need an environment to play around in.  This means creating a game screen. But before we can do this we need to have some new graphics to go with it!

## Custom Graphics
  
As I've made Tetris previously, and still have all the code, sound and graphics belonging to it I imported this into [CharPad](https://subchristsoftware.itch.io/charpad-c64-free):

![[16.PNG]]
This is a great little tool for working with character sets and game maps. Exporting the character set and adding it to my program was easy:

![[18.PNG]]

I exported raw data. It was not as raw as I expected as there is some metadata (header) information in the file. This takes 24 bytes, so we skip that. We store it at `$3800`. Using the character set goes as follows:

![[19.PNG]]

First we make sure to use memory bank 0 for our video bank. This is the system default, but let's make sure, shall we? Then we set the memory control register bits to use the character set we loaded into memory at `$3800`.

Here's the result:

  ![[20.PNG]]

That kinda looks Tetris already! OK, now we've got the graphics the way we want them, it's time to create the play-field and get that onto the screen as well.  

## Creating the game screen

First, we create the playing field in CharPad. It has a nice map editor, so this is what I came up with:  

![[17.PNG]]

I used the 1992 game running in Vice as a reference, and exported the map data with the following settings:

![[21.PNG]]

So, only the map is exported, per character, and as raw data. You can see the map is 420 bytes. We import it in the same manner as the character data. We add a label to the position where the data is imported as we need to know the start location in memory for our future screen print subroutine:  

![[22.PNG]]

I added a `0` to the end of the data, as a marker for our print route. So let's create the print subroutine. We want to center the play screen on the 40 column display, and as the play screen is 21 characters in width, we cannot do this! The *horror*. A graphics designer nightmare. I blame Nintendo actually :)  So let's compromise by starting at position 10. Here is what I came up with:  

![[24.PNG]]

This code will read data, and write it into the screen memory. It checks for the value of `0` to see if it is done.  As we will need more screens I added the option to set X and Y registers to the start of the data to be read.

To print this, we need an empty screen and preferably some nice colours, and as I liked the CharPad default colour, I used those in a small sub routine:

![[25.PNG]]

The comment mentions the sprite pointers. These are located at `$07f8-$07ff`, and as this loop is clearing those as well I thought it deserved a mention. We may or may not use sprites, so let's keep this in mind.
  
Let's add a little setup code:

![[26.PNG|175]]

When we start this up we get this:  

 ![[23.PNG]]
  
Excellent!! Of course, I can move left and right and destroy the screen layout by moving over it. So next up is adding collision detection for our blocks.  After that, letting the blocks fall and line checking and we have a game already.  I can't wait!  

## Concluding

Adding external data to our program was not too hard. There can be small gotcha's when using exported data, like the meta data for exported character sets, but overall it's not bad.

The next part is here at: [[Tetris in 6502 - Part 4]]
