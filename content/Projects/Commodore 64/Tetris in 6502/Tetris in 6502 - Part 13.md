---
title: Tetris in 6502 - Part 13
description: Hi-scores loaded and saved!
draft: false
tags:
  - commodore-64
  - kick-assembler
  - asm6502
  - tetris
date: 2024-02-03
---
Hi there and welcome to a new part of this series. We are going to look at hi-scores!  
  

> [!NOTE]
> As usual, the code can be [found on github](https://github.com/wiebow/tetris.c64).  Warning! The code can be a bit unstable at the moment because the final bits are tweaked to get rid of the minor annoyances that still remain, and I am tweaking all kinds of stuff at this moment.  

![[Selection_001.png]]

Here is a screenshot of the old Tetris code, as seen in VisAss in VICE, and the code being ported to kick assembler.
  
Lots of code follows! :)  I decided to not add new all code to this post as it is already a very long one. The complete files can be found on GitHub as I said. I have posted the most interesting bits here and it already is quite a lot.  

## Hi-score Table

As Tetris is a score attack game it is essential that progress is saved. A hi-score table is mandatory for this type of game. It's even better if it is saved and re-loaded as well. So let's get to it.  
  
The table itself is very simple. It is a data area, and each entry has a score length of 3 bytes (as we use decimal mode for scoring, we can have a score up to 999999) and then we have a name length of 7. Why? Seven bytes fit in the window that holds the score display. Here is the table definition:  
  
```
.const TABLE_ENTRIES = 3  
.const TABLE_NAMELENGTH = 7  
.const TABLE_SCORELENGTH = 3  

.const ENTRY_LENGTH = TABLE_NAMELENGTH + TABLE_SCORELENGTH

hiscore_table_start:  
.fill ENTRY_LENGTH*TABLE_ENTRIES, 0  

hiscore_table_end:  
.fill ENTRY_LENGTH, 0  
```
  
We add an extra entry at the back of the table to ensure we have a buffer when moving data. This might not be required, but at least it guarantees we do not overwrite any code or data we put behind it.  As you might have guessed, this moving entries code might require a rewrite to ensure only valid data is moved. We'll get to that later on, I don't like loose ends. Loose ends in assembly code tend to have consequences!! :)

![[Screenshot from 2017-01-22 14-38-15.png]]
  
Initializing the table goes as follows: We go through the data area, and we add a default entry for as often as defined by `TABLE_ENTRIES`: 
  
```asm6502
RESET_HISCORE_TABLE:  
		lda #TABLE_ENTRIES  
		sta current_entry  
		ldx #0  
		start_entry_reset:  
		ldy #0  
!loop:
		lda default_table_entry,y  
		sta hiscore_table_start,x  
		inx  
		iny  
		cpy #ENTRY_LENGTH  
		bne !loop-  
		dec current_entry  
		bne start_entry_reset  
		rts  

default_table_entry:  
.byte $00,$12,$06
.text "WDWBEST"  
  
// counter for RESET_HISCORES_TABLE and PRINT_HISCORE_TABLE  
current_entry:  
.byte 0  
```
  
Printing the table requires quite a few steps. We are re-using the level select screen when printing the hiscore table, so when that is done, we add the table information over the data in that screen.  We only need to print the scores and the names, as the level select screen already has an empty table in it with an index.  
  
We set the `.x` and `.y` registers to the correct screen location and call this routine:  
  
```asm6502
PRINT_HISCORE_TABLE:  
// save coordinates
		stx hiscore_table_position  
		sty hiscore_table_position+1  
		clc
		jsr PLOT
  
		lda #1  
		sta current_entry  
  
// reset table data offset  

		ldy #0  
  
print_table_entry:  
		ldx #TABLE_SCORELENGTH // this amount of bytes in score  
!loop:  
		lda hiscore_table_start, y  
		pha  // store value  
		lsr  // shift right 4 times  
		lsr  
		lsr  
		lsr  
		clc  
		adc #$30  // add $30 to get a screen code  
		jsr PRINT  // print it  
		pla  // retrieve original value  
		and #001111  // get rid of leftmost bits  
		clc  
		adc #$30  
		jsr PRINT  
		iny  
		dex  // dec number counter  
		bne !loop-  
  
		lda #$20  
		jsr PRINT  
  
// print the name  
  
		ldx #TABLE_NAMELENGTH  
!loop:  
		lda hiscore_table_start,y  
		jsr PRINT  
		iny  
		dex  
		bne !loop-  
		lda current_entry  
		cmp #TABLE_ENTRIES  
		beq !exit+  
  
		inc current_entry  
  
// save memory pointer offset  
		tya  
		pha  
  
// go one line down  
		inc hiscore_table_position  
  
// position cursor  
		ldx hiscore_table_position  
		ldy hiscore_table_position+1  
		clc  
		jsr PLOT  
  
  // restore memory pointer offset  
		pla  
		tay  
		jmp print_table_entry  
!exit:  
		rts  
  
// x and y positions for PRINT_HISCORE_TABLE  
hiscore_table_position:  
.byte 0,0  
```

## Game Over And New Hiscore?

Looking back at the code from 23 years ago, I quite liked the approach. Instead of using sorting methods, like bubble sort, an entry comparison is done on all the bytes in each table entry.  
  
Each byte is checked against the new score and the difference is logged in a corresponding flag. This flag is set to `$ff` if the new score was lower, `$00` if it was the same and `$01` if it was higher. Depending on the byte checked a new hi-score is detected or rejected. If a new score is detected a new entry is inserted at the current location, else the next entry is tested:  
  
```asm6502
PROCESS_NEW_SCORE:  
// start with no hiscore  
		lda #0  
		sta new_hiscore  
// reset entries counter  
		lda #1  
		sta current_entry  
  // reset table data offset  
		ldy #0  
  
  
entry_compare:  
// reset byte compare flags for this entry  
		lda #0  
		sta compare_flags  
		sta compare_flags+1  
		sta compare_flags+2  
//reset the compare flag counter  
		ldx #0  
  
byte_compare_loop:  
// check each byte in the new_score with current entry  
// set compare flag accordingly  
		lda new_score,x  
		cmp hiscore_table_start,y  
		beq byte_compared // score is same. skip  
		bpl byte_is_higher // score is higher  
		dec compare_flags,x // score is lower  
		jmp byte_compared  
		byte_is_higher:  
		inc compare_flags,x  
byte_compared:  
		iny  // inc data counter  
		inx  // inc byte counter  
		cpx #TABLE_SCORELENGTH  
		bne byte_compare_loop  
  
// lets see if new_score was higher than the table entry  
// this is fixed to a score length of 3 bytes !!  
		lda compare_flags  
		beq !skip+  // this byte was the same, check next  
		bpl found_hiscore  // higher! :)  
		jmp no_hiscore  // lower :(  
!skip:  
		lda compare_flags+1  
		beq !skip+  // same, check 3rd byte  
		bpl found_hiscore   // :)  
		jmp no_hiscore      // :(  
!skip:  
		lda compare_flags+2  
		bmi no_hiscore  // last byte is lower. so no new hi  
		jmp found_hiscore  // all 3 digits the same or last higher. new hiscore!  
no_hiscore:  
// new_score was lower than this entry.  
// check the rest of the entries if not yet all done.  
		inc current_entry  
		ldx current_entry  
		cpx #TABLE_ENTRIES+1  
		beq all_entries_compared  // all entries compared  
// so no hiscore at all! exit  
  
// goto start of next table entry  
		jsr GET_ENTRY_OFFSET    // this uses X register.  
		ldy entry_offset        // get offset to beginnig of next entry  
		jmp entry_compare // do the next entry  
  
all_entries_compared:  
		rts  
  
  
found_hiscore:  
// hiscore found and its position is in current_entry  
		ldx current_entry  
		jsr GET_ENTRY_OFFSET  
		jsr INSERT_HISCORE_ENTRY  
  
// add the score to the entry  
		ldy entry_offset  
		lda new_score  
		sta hiscore_table_start,y  
		lda new_score+1  
		sta hiscore_table_start+1,y  
		lda new_score+2  
		sta hiscore_table_start+2,y  
  
// clear the name. add dots  
		ldx #TABLE_NAMELENGTH  
		lda #$2e  
!loop:  
		sta hiscore_table_start+3,y  
		iny  
		dex  
		bne !loop-  
  
// mark that a new hiscore has been detected at this entry.  
		lda current_entry  
		sta new_hiscore  
		rts  
  
```
  
Some data manipulation is required to insert a new entry in the list:  
  
```asm6502
INSERT_HISCORE_ENTRY:  
// first we need to move the data down.  
// point memory offset to end of table.  
		ldy #hiscore_table_end - hiscore_table_start  
!loop:  
// move data until we're on the wanted offset.  
		lda hiscore_table_start - ENTRY_LENGTH,y  
		sta hiscore_table_start,y  
		dey  
		cpy entry_offset  
		bpl !loop-   // keep going  
  
clear_entry:  
		ldy entry_offset  
		lda #$01    // insert some values  
		ldx #0  
!loop:  
		sta hiscore_table_start,y  
		iny  
		inx  
		cpx #ENTRY_LENGTH  
		bne !loop-  
!exit:  
		rts  
```
  
When this is all done we print the level select screen and we overwrite the level select text with a happy or sad message to indicate a new hiscore or a loser attempt:  
  
```asam6502
PrintHappyMessage:  
		ldy #0  
		ldx #17  
!loop:  
		lda hiscoremessage1,y  
		sta $0400+52,y  
		lda hiscoremessage2,y  
		sta $0400+52+40,y  
		iny  
		dex  
		bne !loop-  
		rts  
  
PrintSadMessage:  
		ldy #0  
		ldx #17  
!loop:  
		lda noHiscoremessage1,y  
		sta $0400+52,y  
		lda noHiscoremessage2,y  
		sta $0400+52+40,y  
		iny  
		dex  
		bne !loop-  
		rts  
  
hiscoremessage1:  
.text " a new hiscore!! "  

hiscoremessage2:  
.text " enter your name "  

noHiscoremessage1:  
.text "   too bad  :(   "  

noHiscoremessage2:  
.text "    game over    "  
```
  
Code to enter the name is also added, in the file `controlled_input.asm`. It uses the kernal routine at `$FFE4` (GETIN) to scan for keyboard entry and only the accepted characters are added to the name buffer:  
  
```asm6502
valid_character:  
		ldy input_len  
		cpy #MAX_INPUT_CHARS  
		beq !exit+ //input  
		sta input_buffer,y  
		jsr PRINT  
		jsr PRINT_CURSOR  
		inc input_len  
  
		lda #0  
		rts  
input_done:  
		lda #1  
		!exit:  
		rts  
````
  
The label `input_done` is jumped to when the user presses the return key. A flag is set so the main program can exit this game mode, or keep waiting for characters. The complete code for the input routes can be found in `controlled_input.asm`.  
  
Pfew!!!!  

## Load and Save

Saving and loading data on the C64 is relatively easy.  It is memory based, so you need to know which memory parts need to be written to the open device. The open device is #8 (the disk drive)
  
First, we need to open the device and set the file name to load:  
  
```asm6502
.label SETLFS = $ffba  
.label SETNAM = $ffbd  
.label LOAD = $ffd5  
  
LOAD_FILE:  
// set logical device  
		lda #0  
		ldx #8  
		ldy #0  
		jsr SETLFS  
  
// get length of file name  
		lda #filename_end - str_filename  
		ldx #<str_filename  
		ldy #>str_filename  
		jsr SETNAM  
  
str_filename:  
.text "filename here"  

filename_end:  
.byte 0  
```

Then we point to the memory address we want to load the data before calling the load KERNAL routine:  
  
```asm6502
// set memory destination and load  
		lda #0  
		ldx load_destination  
		ldy load_destination+1  
		jsr LOAD  
		rts  
  
load_destination:  
.byte 0,0  
```

`load_destination` must point to `hiscore_table_start`.
  
Don't forget to close the file afterwards. We need to do this as we might save several times during one play session.
  
Saving is done as loading, except we also have to set the end memory address. This is easy, as we labeled it in the source: `data_start` should point to `hiscore_table_start` and `data_end` should point to `hiscore_table_end`.
  
```
// set pointers to the memory block to save  
		lda #<data_start  
		sta startsave  
		lda #>data_start  
		sta startsave+1  
  
// save up until to the end address  
		lda #<startsave  
		ldx #<data_end  
		ldy #>data_end  
		jsr SAVE  
		rts  
```
  
### Conclusion

Adding a hi-score to a game is not trivial. It requires quite a bit of code, certainly more than I expected when I started this addition. But it gives the game an important feature and it is appreciated by everyone who plays the game, we can be sure of that.
  
Next time we will start adding the final touches to the game. We will be adding more robust sound features, some colour options and tweaks to try and finalise the game.  
  

> [!NOTE]
> Remember: to view the full code --this post contains only excerpts-- visit my github page linked at the top of this post. 

Happy coding and see you later, in [[Tetris in 6502 - Part 14]].
