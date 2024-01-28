---
title: Amiga 1200 PCMCIA compact flash
description: How to start using a CF card and ADF files with the Amiga 1200.
draft: false
tags:
  - amiga
  - guide
---
## Introduction
  
I obtained an Amiga 1200 with no internal harddisk. It came with a few floppies containing demos and audio files. The set also contained Amiga OS 3.0 disks (Workbench, Storage, Extra's, Fonts and Locale).  As I intend to do some work (read: retro programming) on this machine I had to find a way to obtain and install more software.  
The internet is full of *Amiga Disk File* (.ADF) files but how to put these onto an Amiga floppy? PC floppy drives cannot write the .ADF files. It turns out there is a way: the tool to use is an Amiga command-line tool called `adf2disk`.

But how do we transfer the tool and the .ADF files onto the Amiga in the first place? The Amiga 1200 has a PCMCIA card slot which can be used with a PCMCIA to CF (Compact Flash) adaptor. After looking around on the internet I found and ordered this set of [adaptor](http://www.cameratotaal.com/pcmcia-compact-flash-kaart-adapter-voor-laptop-p-1025.html) and [card](http://www.cameratotaal.com/pcmcia-compact-flash-kaart-adapter-voor-laptop-p-1025.html). I went for the 4GB card because 4GB is the maximum disk size an Amiga can access using the default file system [FFS](http://en.wikipedia.org/wiki/Amiga_Fast_File_System).  
  
The Amiga does not recognise the CF card by default, some files will have to be installed onto the Workbench disk before it can be accessed. We can download these files, put them on a DOS floppy and copy them to the Workbench floppy disk. Before we use a DOS floppy we need to activate *CrossDOS*.  

This took me some time to figure out, but this is the complete procedure to follow:  

1. Enable CrossDOS on the Amiga;
2. On a PC, format a floppy with the FAT file system, in 720KB mode;
3. Download the files needed to recognise the CF card, and put these on the floppy;
4. Install the files from the floppy onto the Workbench floppy using CrossDOS;
5. Reboot, access the CF card and test;
6. Download .ADF files and the `adf2disk` utility and put these on the CF card;
7. Write .ADF file to Amiga floppy;
8. Enjoy.

## Let's go!

Enabling CrossDOS is easy. I used this [guide](http://www.l8r.net/technical/t-crossdos.shtml). I copied the files from the Storage disk to my RAM disk and then to the Workbench disk because I had only one drive.

Then, move over to a PC. The stock Amiga floppy drive can read DD (Double Density) disks. You can use a HD diskette for this next step, but you must put a tape over the hole on the left side of the floppy so the drive sees it as a DD diskette. Also, I found out that newer USB floppy drives do not support the formatting of disks at 720KB capacity so use a standard, built-in PC floppy drive for this.

Format the diskette at 720KB capacity instead of 1.44MB using the following DOS command: `format a: /t:80 /n:9`.
  
Then, download and unpack the following archives:

- [fat95.lha](http://aminet.net/search?query=fat95)
- [cfd.lha](http://aminet.net/search?query=cfd.lha)

[LHA](http://en.wikipedia.org/wiki/LHA_(file_format)) files can be unpacked on Windows using [7-zip](http://www.7-zip.org/).

Make sure to use the latest version (1.27 at the time of writing) of the cfd archive. Using an earlier version can cause major performance problems. This at least happened with the adaptor and card combination I use.

Put the unpacked files onto the floppy and move back to your Amiga. You might want to make a copy of the Workbench disk before performing the next step if you do not want to risk losing or messing up this important disk. Boot Workbench, and open the workbench disk (DF0:) and select 'show all files' from the workbench menu bar. This is important as it will then show you the important 'L' directory which you will need later.

Copy the following files from the DOS floppy (It should appear as PC0:) to the following folders on your workbench disk:

* `compactflash.device` into `Devs/`
* `CF0` and `CF0.info` into `Devs/DOSDrivers/`
* `fat95` into `L/`

As I had only one drive, I first copied the files from the PC0: drive into my RAM disk and then re-inserted the workbench disk.

>[!Note]
>The long names on the DOS floppy might be truncated. For instance, you might have to rename the `compactflash.device` file because of this.

You can now reboot the Amiga. Workbench should start and you should automatically see the CF0: device if the CF card is formatted with FAT32.

Now, back to the PC again. I assume you have a multi-card reader or a similar device so you can connect the CF card into your PC. Again, go to Aminet and download [adf2disk.lha](http://aminet.net/search?query=adf2disk). Unpack and copy the contents to the root of the CF card. You now have to add the .ADF files you want to transfer onto the CF card as well and then go back to your Amiga:

- Insert the card into the Amiga;
- Open a CLI and go to the CF card by typing `cd cf0:`
- Put an empty floppy into DF0:
- Use the command-line tool to write images: `adf2disk <filename>.adf`

Enjoy!
