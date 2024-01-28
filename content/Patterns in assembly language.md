---
title: Patterns in assembly language
draft: false
tags:
  - assembly
  - asm6502
  - "#asm45gs02"
---
The following items are translations from higher-level language patterns to low-level  assembly language. This can be used as a guide to maintain code standardisation.

More patterns will be added in the future.

# Logic

## If-then-else

`A = 10 : IF A = 15 THEN ... ELSE ...`

```asm6502
				lda #10
				cmp #15
				bne else
				...         # if code
				jmp ifdone

else:           ...         # else code
ifdone:         ...         # rest of program
```



# Math


