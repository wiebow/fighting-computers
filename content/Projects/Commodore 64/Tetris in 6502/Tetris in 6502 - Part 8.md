---
title: Tetris in 6502 - Part 8
description: randomness and input!
draft: false
tags:
  - commodore-64
  - kick-assembler
  - asm6502
  - tetris
---
Here we go with a new part in this series!  
  
We will update some parts of the game that should be done in a different way.  
  
Some things stand out:  

1. As the game will need audio, using the SID chip to generate random numbers is not a valid approach, so a new way of creating numbers is needed.
2. The input is not how it should be. We want to be able to hold a key and have the block react with some delay between the action.

> [!NOTE]
> A reminder here to tell you that the source code for this game can be found on [Git Hub](https://github.com/wiebow/tetris.c64)! You can download it or "[crack"](http://csdb.dk/release/index.php?id=142638) it, I don't care!

## Random Numbers

[This article shows how](http://meatfighter.com/nintendotetrisai/#Picking_Tetriminos) the Nintendo Tetris game generates the random numbers for block selection. If it's good enough for Nintendo then it's good enough for us. Here is a nice article on [Wikipedia](https://en.wikipedia.org/wiki/Linear_feedback_shift_register) about the algorithm and possible variations.

The code easily translates into something usable for our game:

![[67.PNG]]

Adding  SetUpRandom to the start-up part of the game, and calling GetRandom from the block selection code makes this easy to implement.

After implementing it I found the pattern to be a bit meh, but reading the article about Nintendo's Tetris reveals that the update part is called each display frame, regardless if a new block is needed or not! After moving UpdateRandom to the main loop and removing it from the new block code I found the pattern to be much more interesting. Succes!

The SID part of the set-up code we can remove. We are ready to add music and sound effects to the game now without disturbing the game loop. This we will do later on.

## Keyboard Input

Needing to re-press the same key time and time again to move a block is off putting. So we need to change the input routine to provide constant feedback to the game while not doing so too fast.

Using the keyboard kernel routine GETIN (`$FFE4`) has the side effect that we need to press the keys again and again. So lets do this differently. We have not disabled interrupts, so the keyboard is scanned 50 times a second. One of the routines called is the one at `$EA87`. This places the keycode of the held key into location `$CB`. Interesting! So by reading this location we know which key is held, automatically.  We need to create a delay in our input code so the reaction is not too fast.  
  
I changed the starting code in `input.asm` to this:

![[68.PNG]]
  
We need key codes instead of character string values, and we use the value of address `$CB` instead of using subroutine `$FFE4`. Let's change the read and delay code:  


![[69.PNG]]

First we check if the held key is the same as the previous key. If this is the case, we use the delay. The delay is 7 updates. If the held key is different than the previous key then we use no delay because we want fast control reactions.  We must always reset the key delay though because the next call needs that delay if the same key is held again. Makes sense? I hope so!
  
The rest of this code is as it was, except for the bit when we press the DOWN key. We want blocks to fall faster when we hold down, so we need to add this little change:  

![[70.PNG]]
  
So holding down results in a shorter delay. Also, dropping a block when holding down makes the next block appear almost immediately, just like when a block drops when not pressing down.  
  
Modifying the controls has made the game a lot better. The key to good game-play is a tight control system!

That's it for this post.  You can check the complete code on the GitHub link mentioned above. The next post will be about adding sound and maybe some more tweaks. For me, programming a game is about iteration: tweak and tweak until it feels and looks right.  

See you in [[Tetris in 6502 - Part 9]].
