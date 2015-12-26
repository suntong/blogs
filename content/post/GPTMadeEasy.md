---
title: "Creating GPT partitions easily on the command line"
date: "2015-12-26T00:04:56-05:00"
categories: ["Tech"]
tags: ["Debian","Ubuntu","Linux","partition","GPT","go","easygen","CLI"]
---

<!--
[category Tech][tags Debian,Ubuntu,Linux,partition,GPT,go,easygen,CLI]
-->

## Incentives

I've long been enjoying using `sfdisk` to manipulate my disk partitions. I always use it to,

- [change the boot device](http://xpt.sourceforge.net/techdocs/nix/disk/general/disk01-PartitionTools/index.html#cmd_sfdisk)
- [save/restore disk partition setting and fix errors](http://xpt.sourceforge.net/techdocs/nix/disk/general/disk01-PartitionTools/index.html#save_restore_disk_partition_setting_and_fix_errors_using_sfdisk)
- or even, [create disk partitions](https://suntong.github.io/blogs/2015/12/25/use-sfdisk-to-partition-disks)

When my disks are over 2T in size, I can't use MBR and therefore can't use `sfdisk` anymore. I have to use GPT instead. My first GPT partitions was created using GUI tools, but I really hate GUI tools. So this time when I need to partition GPT again, I look for the command line alternative instead. 

<!--more-->

<a name="problem"/>
## The problem
[ ](https://suntong.github.io/blogs/)

There is a command line tool, `sgdisk`, to create GPT partitions, but its command line interface is inhuman, if not insane. Here is the example given by the `sgdisk` author, from [Creating Scripts for Partition Manipulation](http://www.rodsbooks.com/gdisk/sgdisk-walkthrough.html):

```bash
#!/bin/bash
sgdisk -og $1
sgdisk -n 1:2048:4095 -c 1:"BIOS Boot Partition" -t 1:ef02 $1
sgdisk -n 2:4096:413695 -c 2:"EFI System Partition" -t 2:ef00 $1
sgdisk -n 3:413696:823295 -c 3:"Linux /boot" -t 3:8300 $1
ENDSECTOR=`sgdisk -E $1`
sgdisk -n 4:823296:$ENDSECTOR -c 4:"Linux LVM" -t 4:8e00 $1
sgdisk -p $1
```

As the comparison, to [create disk partitions using `sfdisk`](https://suntong.github.io/blogs/2015/12/25/use-sfdisk-to-partition-disks)), here are two examples:

- Three primary partitions: two of size 50MB and the rest:


    	sfdisk /dev/hda -uM << EOF
    	,50
    	,50
    	;
    	EOF

- A 1MB OS2 Boot Manager partition, a 50MB DOS partition,
   and three extended partitions (DOS D:, Linux swap, Linux):

    	sfdisk /dev/hda -uM << EOF
    	,1,a
    	,50,6
    	,,E
    	;
    	,20,4
    	,16,S
    	;
    	EOF

As you can see, it is so simple. Coming from the simple `sfdisk` and landed at the complicated `sgdisk`, on looking at the above example, I realize that there is no way I can write the above `sgdisk` code by hand, i.e., I need a tool to write it for me. Of course, the first tool that came into mind is the [universal code generator, easygen](https://github.com/suntong/easygen). After poking around here and there, I [made it](https://github.com/suntong/easygen/commit/8b223d1f20993feb6acfde2bf40a10033eb728d5).

<a name="solution"/>
## The solution
[ ](https://suntong.github.io/blogs/)

So to use the [universal code generator, easygen](https://github.com/suntong/easygen) to create GPT partitions, first we need to define our partitions layout in an easy format as the following:

```
Disk: /dev/sdb

# Common Partitions Types
#
# 8300 Linux filesystem
# 8200 linux swap
# fd00 linux raid
# ef02 BIOS boot
# 0700 Microsoft basic data
#
# For more GPT Partitions Types,
# echo L | gdisk /dev/sdb

Partitions:
  - Name: bios_boot
    Type: ef02
    Size: +200M

  - Name: linux_boot
    Type: 8300
    Size: +20G

  - Name: windows
    Type: "0700"
    Size: +30G

  - Name: linux_swap
    Type: 8200
    Size: +10G

  - Name: os1
    Type: 8300
    Size: +12G

  - Name: os2
    Type: 8300
    Size: +12G

  - Name: os3
    Type: 8300
    Size: +12G

  - Name: data
    Type: 8300
    Size: "0"
```

I.e., for each partition, we need to give its name, type and size. For your convenient, I've put the mostly used GPT types in the comment, as shown above.

That's the hardest part, the rest are simple and straightforward, to generate partition creation code:

    easygen test/sgdisk | tee test/sgdisk.sh

The result:

```bash
 # format /dev/sdb as GPT, GUID Partition Table
 sgdisk -Z /dev/sdb

 sgdisk -n 0:0:+200M -t 0:ef02 -c 0:"bios_boot" /dev/sdb
 sgdisk -n 0:0:+20G -t 0:8300 -c 0:"linux_boot" /dev/sdb
 sgdisk -n 0:0:+30G -t 0:0700 -c 0:"windows" /dev/sdb
 sgdisk -n 0:0:+10G -t 0:8200 -c 0:"linux_swap" /dev/sdb
 sgdisk -n 0:0:+12G -t 0:8300 -c 0:"os1" /dev/sdb
 sgdisk -n 0:0:+12G -t 0:8300 -c 0:"os2" /dev/sdb
 sgdisk -n 0:0:+12G -t 0:8300 -c 0:"os3" /dev/sdb
 sgdisk -n 0:0:0 -t 0:8300 -c 0:"data" /dev/sdb

 sgdisk -p /dev/sdb

 # inform the OS of partition table changes
 partprobe /dev/sdb
 fdisk -l /dev/sdb
```

After carefully read [`sgdisk`'s man page](http://www.rodsbooks.com/gdisk/sgdisk.html), I found it is not that insane or inhuman, but just powerful after all.

The result will be exactly as we are expecting:

```
$ sgdisk -p /dev/sdb
Disk /dev/sdb: 732558336 sectors, 2.7 TiB
Logical sector size: 4096 bytes
Disk identifier (GUID): C4426321-9726-4022-BB20-2EF00B490465
Partition table holds up to 128 entries
First usable sector is 6, last usable sector is 732558330
Partitions will be aligned on 256-sector boundaries
Total free space is 250 sectors (1000.0 KiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1             256           51455   200.0 MiB   EF02  bios_boot
   2           51456         5294335   20.0 GiB    8300  linux_boot
   3         5294336        13158655   30.0 GiB    0700  windows
   4        13158656        15780095   10.0 GiB    8200  linux_swap
   5        15780096        18925823   12.0 GiB    8300  os1
   6        18925824        22071551   12.0 GiB    8300  os2
   7        22071552        25217279   12.0 GiB    8300  os3
   8        25217280       732558330   2.6 TiB     8300  data

$ fdisk -l /dev/sdb

Disk /dev/sdb: 2.7 TiB, 3000558944256 bytes, 732558336 sectors
Units: sectors of 1 * 4096 = 4096 bytes
Sector size (logical/physical): 4096 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: C4426321-9726-4022-BB20-2EF00B490465

Device        Start       End   Sectors  Size Type
/dev/sdb1       256     51455     51200  200M BIOS boot
/dev/sdb2     51456   5294335   5242880   20G Linux filesystem
/dev/sdb3   5294336  13158655   7864320   30G Microsoft basic data
/dev/sdb4  13158656  15780095   2621440   10G Linux swap
/dev/sdb5  15780096  18925823   3145728   12G Linux filesystem
/dev/sdb6  18925824  22071551   3145728   12G Linux filesystem
/dev/sdb7  22071552  25217279   3145728   12G Linux filesystem
/dev/sdb8  25217280 732558330 707341051  2.7T Linux filesystem
```

## Is it better?

I know most GUI lovers would laugh at my *silly* action. I just know them so well that I can imagine exactly what the conversion would be, as follows (**T** stands for "they", whereas **M** stands for me):

T
: *"Ha, you are making this more complicated"*

M
: No, I think I'm making it simpler. For example, the data definition (`.yaml`) file not only serves the purpose of generating the partition-creation code, but also serves the purpose of documentation as well.

T
: *You are just kidding me, the best way to document is to take a screenshot*

M
: Well, I disagree. What if you want to do it again?

T
: *That's so simple. I just need to find out where I store my screenshot, and I can easily redo it again*

M
: From the GUI, doing the clicking all over again?

T
: *Definitely. Who want to learn or use your silly way?*

Oh, well, my tools is definitely not for them, no matter how hard I'd like to try. I think that it is the mentality conflicting between the two camps, the Microsoft spoiled dummies, and die-hard text-everything Unix fans. Personally, I view WYSIWYG GUI tools as carving stones with chisels, and text-based/command-line tools as operating CNC machines. Yes, sure there are strict rules and steep learning curves regarding the CNC machines, but once you are over that fear and hurdle, the benefits or possibilities are endless. 

Talking about partition creation, let alone doing it again on a different machine, even on the same machine, I need to do it several times, because nobody can achieve the perfect score with a single shot without any prior practices. I can't. Doing the clicking over and over again will wear me out very fast, and I most probably would end up with a less optimal setting that I'll be regretting since. Using the `.yaml` file and code generation, it'll be nearly "zero cost", I can do it over and over again until I'm fully satisfied.

Let alone doing it several times, even just doing it once can be simplified by the `.yaml` file as well. Here is a piece of my actual GPT partition definition:


```
  - Name: os1p
    Type: 8300
    Size: +12G

  - Name: os1s
    Type: 8300
    Size: +12G

  - Name: os11
    Type: 8300
    Size: +8G

  - Name: os12
    Type: 8300
    Size: +8G

  - Name: os13
    Type: 8300
    Size: +8G

  - Name: os2p
    Type: 8300
    Size: +12G

  - Name: os2s
    Type: 8300
    Size: +12G

  - Name: os21
    Type: 8300
    Size: +8G

  - Name: os22
    Type: 8300
    Size: +8G

  - Name: os23
    Type: 8300
    Size: +8G
```

I'd like to keep a lot of OS partitions for me to try new things or keeping the old systems so that I can go back to them any time. In the above listing, I have two sets of OS partitions, `os1x` and `os2x`, each with two big partitions (12G), primary and secondary, and three smaller partitions (8G). Using a text editor, I can quickly duplicate the first entry into the first full set, then into both the two sets, without too much trouble. Doing from GUI, not a single chiseling I can save. Err, I meant clicking. 

<a name="advantages"/>
## The advantages
[ ](https://suntong.github.io/blogs/)

The `.yaml` file and code generation will

- serve both the purpose of action and documentation.
- make it very simple to redo/reuse, regardless to different machines, or to the same machines; many times, or just once.
- give me a clearer content. Instead of the exact `sgdisk` commands, i.e., the implementation details, the file contains only each partition's name, type and size. Clear and straight to the point. No trivial details are in the way.
- of course, it also gives the benefit of coding and documenting at the same place. More reasons can be documented in the `.yaml` file, the same way as comments make code-purpose clearer.

Here is the bonus, once the data definition (`.yaml`) file is in place, you can do all sorts of other things you want.

E.g., how about managing the mount-points?

```
$ easygen -tf test/sgdisk-mp test/sgdisk

 mkdir /mnt/bios_boot
 mkdir /mnt/linux_boot
 mkdir /mnt/windows
 mkdir /mnt/linux_swap
 mkdir /mnt/os1
 mkdir /mnt/os2
 mkdir /mnt/os3
 mkdir /mnt/data


 mount LABEL=bios_boot /mnt/bios_boot
 mount LABEL=linux_boot /mnt/linux_boot
 mount LABEL=windows /mnt/windows
 mount LABEL=linux_swap /mnt/linux_swap
 mount LABEL=os1 /mnt/os1
 mount LABEL=os2 /mnt/os2
 mount LABEL=os3 /mnt/os3
 mount LABEL=data /mnt/data
```

Generating the `fstab` entries are as simple as above as well. Or, how about using `mkfs` to format each partition? Simple as well. 

I'll explain more on [the easy to use universal code/text generator easygen](https://github.com/suntong/easygen) next. Meanwhile, you can check out what we have [covered](https://github.com/suntong001/blog/blob/master/GoOptP7-easygen.md) already  [before](https://sfxpt.wordpress.com/2015/07/04/easygen-is-now-coding-itself/), including [mock-up generation](https://sfxpt.wordpress.com/2015/07/10/easygen-for-mock-up/).

Stay tuned.
