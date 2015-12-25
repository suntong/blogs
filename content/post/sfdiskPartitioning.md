---
title: "Use sfdisk to partition disks"
date: "2015-12-25T16:32:39-05:00"
categories: ["Tech"]
tags: ["Debian","Ubuntu","Linux","partition","CLI"]
---

<!--
[category Tech][tags Debian,Ubuntu,Linux,partition,CLI]
-->

I've long been enjoying using `sfdisk` to manipulate my disk partitions, especially for creating disk partitions. 

<!--more-->

Creating disk partitions with `sfdisk` is super easy. The followings are the notes I jotted down back in the old days when HD were still called `hda` instead of `sda`. Replace `hdX` with `sdX`, and everything are still as good as new, as far as MBR type of disks are concerned. 

Input lines have fields `<start>,<size>,<type>`... - see sfdisk.8.
`sfdisk` reads lines of the form

              <start> <size> <id> <bootable> <c,h,s> <c,h,s>

Usually no `<start>` is given, and input lines start with a comma.

## Example 1

1) One big partition:

	sfdisk /dev/hda << EOF
	;
	EOF

or, 

	echo ';' | sfdisk /dev/sdc

(If there was garbage on the disk before, you may get error messages
like: `ERROR: sector 0 does not have an msdos signature`
and `/dev/hda: unrecognized partition`. This does not matter
if you write an entirely fresh partition table anyway.)

The output will be:

```
Old situation:
...
New situation:
Units = cylinders of 208896 bytes, blocks of 1024 bytes, counting from 0

   Device Boot Start     End   #cyls   #blocks   Id  System
/dev/hda1          0+   1023    1024-   208895+  83  Linux native
Successfully wrote the new partition table
  hda: hda1
```

Writing and rereading the partition table takes a few seconds -
don't be alarmed if nothing happens for six seconds or so.

To create a single "W95 FAT32 (LBA)" partition, try:

    echo ',,c;' | sfdisk /dev/sdd

## Example 2

2) Three primary partitions: two of size 50MB and the rest:

	sfdisk /dev/hda -uM << EOF
	,50
	,50
	;
	EOF

The result:

```
New situation:
Units = megabytes of 1048576 bytes, blocks of 1024 bytes, counting from 0

   Device Boot Start   End     MB   #blocks   Id  System
/dev/hda1         0+    50-    51-    51203+  83  Linux native
/dev/hda2        50+   100-    51-    51204   83  Linux native
/dev/hda3       100+   203    104-   106488   83  Linux native
Successfully wrote the new partition table
  hda: hda1 hda2 hda3
```

`/dev/hda1` is one block (in fact only half a block) shorter than
`/dev/hda2` because its start had to be shifted away from zero in
order to leave room for the Master Boot Record (MBR).

## Example 3

3) A 1MB OS2 Boot Manager partition, a 50MB DOS partition,
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

The result:

```
   Device Boot Start   End     MB   #blocks   Id  System
/dev/hda1         0+     1-     2-     1223+   a  OS/2 Boot Manager
/dev/hda2         1+    51-    51-    51204    6  DOS 16-bit FAT >=32M
/dev/hda3        51+   203    153-   156468    5  Extended
/dev/hda4         0      -      0         0    0  Empty
/dev/hda5        51+    71-    21-    20603+   4  DOS 16-bit FAT <32M
/dev/hda6        71+    87-    17-    16523+  82  Linux swap
/dev/hda7        87+   203    117-   119339+  83  Linux native
Successfully wrote the new partition table
  hda: hda1 hda2 hda3 < hda5 hda6 hda7 >
```

All these rounded numbers look better in cylinder units:


```
% sfdisk -l /dev/hda
   Device Boot Start     End   #cyls   #blocks   Id  System
/dev/hda1          0+      5       6-     1223+   a  OS/2 Boot Manager
/dev/hda2          6     256     251     51204    6  DOS 16-bit FAT >=32M
/dev/hda3        257    1023     767    156468    5  Extended
/dev/hda4          0       -       0         0    0  Empty
/dev/hda5        257+    357     101-    20603+   4  DOS 16-bit FAT <32M
/dev/hda6        358+    438      81-    16523+  82  Linux swap
/dev/hda7        439+   1023     585-   119339+  83  Linux native
```

## Explanations

But still - why does /dev/hda5 not start on a cylinder boundary?
Because it is contained in an extended partition that does.
Of the chain of extended partitions, usually only the first is
shown. (The others have no name under Linux anyway.) But
these additional extended partitions can be made visible:

	% sfdisk -l -x /dev/hda

```
   Device Boot Start     End   #cyls   #blocks   Id  System
/dev/hda1          0+      5       6-     1223+   a  OS/2 Boot Manager
/dev/hda2          6     256     251     51204    6  DOS 16-bit FAT >=32M
/dev/hda3        257    1023     767    156468    5  Extended
/dev/hda4          0       -       0         0    0  Empty

/dev/hda5        257+    357     101-    20603+   4  DOS 16-bit FAT <32M
    -            358    1023     666    135864    5  Extended
    -            257     256       0         0    0  Empty
    -            257     256       0         0    0  Empty

/dev/hda6        358+    438      81-    16523+  82  Linux swap
    -            439    1023     585    119340    5  Extended
    -            358     357       0         0    0  Empty
    -            358     357       0         0    0  Empty

/dev/hda7        439+   1023     585-   119339+  83  Linux native
    -            439     438       0         0    0  Empty
    -            439     438       0         0    0  Empty
    -            439     438       0         0    0  Empty
```

Why the empty 4th input line? The description of the extended partitions
starts after that of the four primary partitions.
You force an empty partition with a ",0" input line, but here all
space was divided already, so the fourth partition became empty
automatically.

How did I know about 4,6,a,E,S? Well, **E,S,L** stand for *Extended*,
*Swap* and *Linux*. The other values are hexadecimal and come from
the table:

	% sfdisk -T
	Id  Name
	
	 0  Empty
	 1  DOS 12-bit FAT
	 2  XENIX root
	 3  XENIX usr
	 4  DOS 16-bit FAT <32M
	 5  Extended
	 6  DOS 16-bit FAT >=32M
	 7  OS/2 HPFS or QNX or Advanced UNIX
	 8  AIX data
	 9  AIX boot or Coherent
	 a  OS/2 Boot Manager
	...

Ref: 

/usr/share/doc/util-linux/examples/sfdisk.examples.gz

