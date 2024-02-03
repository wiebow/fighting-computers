---
title: Tetris in 6502 - Part 7
description: Scores, statistics and quality of life additions!
draft: false
tags:
  - commodore-64
  - kick-assembler
  - asm6502
  - tetris
date: 2024-02-02
---
Hi there! This post is all about fleshing out the Tetris experience so the game feels more complete. We are going to add the line count, play level and the showing of the next block to fall.  

> [!NOTE] Title
> As always, all code can be found on [GitHub](https://github.com/wiebow).  
  
## Line Score

We need to see how many lines we're made so far in the game in total. This is not too hard.  The amount of lines made must be added to the value we see on the screen in the 'lines' section. For this we reserve some memory locations:  

```
linesTotal:  
    .byte 0,0  
```

We need 2 bytes, as the amount of lines made can go over 100, and I think that a maximum of 999 lines are enough. We don't need an additional byte to store the value to add to these bytes, we use the `linesMade` byte for this.
  
Then, using the experience with score counting from the previous post we add the lines to this value each time lines are made:

![[56.PNG]]
  
The second addition is always 0, as the maximum number of lines to make in a single drop is 4. And then we print it:  

![[57.PNG]]

We call this code in the main loop, after the lines have been removed from the screen, but before the `linesMade` value is reset to 0.  Moving on, we do the same for the game level.  

## Play Level

The difficulty of the game increases each ten lines made. So, we add a declaration for the number of lines made since the previous level increase. We add the made lines to this counter and if it is higher than ten we increase the level. Here we go. First, we declare some bytes to hold the screen data for us:  

```
gameLevel:  
.byte 0,0
```

We also declare the following consts:  

![[62.PNG]]

`linesPerLevel` is the amount of lines we need to make to go up a level. For testing purposes, this is now set to two, but in the final version this must be set to 10.  
Also, we declare `delayChange` which tells us how much faster the game will become as we go up a level.
  
We define a function to go up a level:  

![[63.PNG]]

First, we advance the level counter, and then we do the same thing as with the score and the lines counter: we add one to it, so we can print it.
  
The lines to make threshold is removed from the lines made this level counter to check for the next level advance.
  
We also make the blocks fall faster by changing the delay. We ensure that the delay is lower than 0, because then the game will slow down again. A minimum delay of 3 is crazy fast! :) 
  
And then we add a function to print the current game level:

![[64.PNG]]

We know this code, it is the same as the code for printing the current level.
  

> [!NOTE]
> NOTE: these two bytes (and the ones used for printing the player level) are only used to PRINT the value on the screen. This is different than the score memory bytes which are used to hold the score AND print it.  

But when do we go up a level? This happens when we make more lines in the current level than the value defined with `linesPerLevel`.  The main loop is changed to check this:  

![[65.PNG]]

This code is added right after removing the lines from the screen, and adding a new block. The `linesMade` are added to the counter which holds the total lines made this level. If it goes over the threshold we go up a level. If not, then we just create the next block.  
  
Which takes us to.......  

## Next Block

One of the fun parts of Tetris is that you can see which block will fall next. It adds an element of planning to an otherwise random game. To enable this feature, we have the extend the NewBlock function to support this.
  
Also, when the game is started, we generate a block ID in advance, so when the first block is being printed, the next ID is already known. I added this to the StartGame subroutine:  


![[59.PNG]]
  
Moving on to the NewBlock function for the needed changes. First, the ID of the block to create NOW (as set in the `nextBlockID` value) is saved to the stack, and we also erase the block already on the screen in the 'next block' field. First we set the screen pointer to that location:  

![[58.PNG]]
 
Then we choose the next block to fall AFTER the one we want to create now, and print it in the screen part reserved for this. We also save this value so we can use it the next time this routine is called.  

![[60.PNG]]

We can use PrintBlock without setting the screen pointers because they are already pointing to the correct location.  
  
Phew. Now we can finally create the block we called this function for, which is what we set out to do in the first place :) We retrieve the ID of the block to create, and place the block on the screen. First, we set the screen pointer to the location where we want to put the new block. We already know this code, but here it is anyway:  

![[61.PNG]]

The only change is the `PLA` instruction, to retrieve the ID of the block. We pushed it onto the stack for easy retrieval earlier. 
  
Let's fire it up and see what is happening:  

![[66.PNG]]

Nice! The game is speeding up as we go up in level (I made two lines, yay!), the next block to drop is shown and the lines counter is moving up as we make more and more lines. One thing though, the level counter is printed one row too low, so let's change the PrintLevel function to plot to 26,8.
  
One more thing: I added some statements to reset the display bytes for level and lines to 0 in the StartGame function as well. 
  
And we're done for now.

## Summary

We are now getting down to the part of game development that is difficult for a lot of developers (well, the coders among us anyway): the game play is complete and now it's all about the small touches to polish things up, doing the non programmer stuff like audio, adding more control schemes, and after that comes the dreaded front-end business of the game.  I am not sure that this series will complete through all those phases. After all, this was about doing something purely for the joy of improving old code, and it has become a larger series than I anticipated already.  
  
The game does need sound now though, to liven things up. If I can get the sound parts that are left on my floppy disks working then I will add those and write a post about it. If I cannot get the audio to work then I must stop because I just do not know enough about that part of C64 development.  
  
There will be at least one more post about code optimisation and re-factoring, though.  
  
Until then: Happy coding! See you in [[Tetris in 6502 - Part 8]]
