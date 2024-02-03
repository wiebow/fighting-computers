---
title: Tetris in 6502 - Part 12
description: Adding music and sound to the game
draft: false
tags:
  - commodore-64
  - kick-assembler
  - asm6502
  - tetris
date: 2024-02-03
---
Hi there and welcome to a new part of this series. We are going to add sound!!

![[97.PNG]]


> [!NOTE]
> As usual, I remind you of the repository location for this game. You can get all the code and source from [this location on GitHub](https://github.com/wiebow/tetris.c64).

## Adding a SID file to the program source

Creating music and sound effects is an art I do not master, so it would've taken me a lot of time to create. I was really happy when I was offered assistance. [Hedning](http://csdb.dk/scener/?id=20879) from Genesis Project PMed me on CSDB and offered his assistance on getting me sounds for the game. Shortly thereafter I received a .SID file, made by Vanje Utne. You may know her from *DeviantArt*, and all kinds of demo's on the C64. [Here is her page](http://vanjau.deviantart.com/).  Many thanks to both of you!!
  
A `.SID` file is a standardised way of distributing sound files, programs and definitions. Because of the standardisation they can be used in players, emulators and on real Commodore 64's. The *HSVC collection* consists of only SID files, so in theory I could have taken any song in that library and use it for this game. But I chose to have at least one original bit in this remake :)

First, we need to add the music file to our source code. Loading a .SID file into Kick Assembler is easy, I added this to the file `main.asm`: 

```
.var music = LoadSid("audio.sid")  
```

The variable music now points to the data, and we can use it as an object, like in an object oriented language. Loading the SID file does not mean we can use it straight away. For this, we need to place its' data into memory. We use another Kick Assembler macro to do that:  

```
.pc = music.location  
.fill music.size, music.getData(i)  
```
  
We get the music location from the object, point the assembler to that location, and then we write that data. NOW we can use the SID file from our program.  
  
Usually, a raster IRQ is set up to run the music independently from the program. The IRQ is called once a frame, the play subroutine is called, and then the main program is continued. As we are already calling the game code once a frame (remember that the main loop is waiting for a specific raster position) we don't need to setup an interrupt to run the music. Let's not complicate things when we don't have to. In the main loop, we add this at the end:  

```
loopend:
		jsr music.play  
		jmp loopstart  
```

This will call the play routine each frame, at the end of the main loop. Now all we need to do is add the music track set definitions. I created a new file, called `sound.asm` and I put this in it:  
  
```
// music definitions in sound file

.const SND_MOVE_BLOCK = 0  
.const SND_ROTATE_BLOCK = 1  
.const SND_DROP_BLOCK = 2  
.const SND_LINE = 3  
.const SND_TETRIS = 4  
.const SND_PAUSE_ON = 5  
.const SND_PAUSE_OFF = 6  
.const SND_OPTION = 7  
.const SND_MUSIC_TITLE = 9  
.const SND_MUSIC_GAMEOVER = 8  
```

We can play the required sound (or music) by calling the following subroutine:  
  
```asm6502
// set accumulator before calling this  
playsound:  
        ldx #0  
        ldy #0  
        jsr music.init  
```

How to use this: Load the accumulator with any of the defined values, and call this subroutine. The main loop call to music.play will take care of the rest.  

## Music
  
We start the music when a mode starts. For the title screen, the call...  
  
```
		lda #SND_MUSIC_TITLE
		jsr playsound  
```

...is added to `attract.asm`, into the subroutine StartAttractMode. Makes sense: start the music when this mode starts.  I really like working with modes.  

## Sound Effects
  
Sound effects need to be played when something happens, but where to add the calls? After some playing around, I settled for the file `play.asm`. Here is an example: After using a control, the correct sound is played: 
  
```
doControls:  
		cpx #LEFT  
		bne !skipControl+  
		jsr BlockLeft  
  
		lda #SND_MOVE_BLOCK  
		jsr playsound  
		jmp doLogic
```

Above you see the first addition, but that file now plays sound for each action. Here is another example from the pause section of the same file. The correct sound is played for pausing or un-pausing the game:  
  
```asm6502
TogglePause:
		lda pauseFlag  // get the current pause flag  
		eor #000001  // flip between 0 and 1  
		sta pauseFlag  // store it  
  
		cmp #$01  // pause mode?  
		beq !skip+  // yes  
  
		lda #SND_PAUSE_OFF  
		jsr playsound  
  
		jmp RestorePlayArea          // no, restore the screen  
!skip:  
		// game is paused. so clear the screen  
  
		lda #$01  // set the erase flag  
		sta playAreaErase  // so area gets cleared as well  
		jsr SavePlayArea  // save and clear the play area  
  
		lda #SND_PAUSE_ON  
		jsr playsound  
  
		jmp PrintPaused  
```

I'm not going to add all the sound instructions I've added to the program but the principle should be clear now, right? :) 

## Concluding

Adding the sound and music isn't that hard, especially when you have someone with experience creating the sounds for you, haha!
  
There are still some small things that need fixing, like overlapping sound effects when hitting controls too fast, but overall I'm quite happy.  
  
There is also something strange going on with timing when I use the joystick controls. This needs investigation, especially since I've moved my development system over to Linux Mint. I am running Linux Vice now and I need to be sure that the problem is not coming from changing Vice versions, or a configuration setting in the emulator...  
  
We'll fix those things later on in another post. I wanted to get this post up because it has been a long time ago since the last part in the series.
  
So, until next time: happy coding, and check out the repository for the code changes and details.

The next part is [[Tetris in 6502 - Part 13]].
