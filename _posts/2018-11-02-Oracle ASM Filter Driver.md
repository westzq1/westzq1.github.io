---
layout: post
title: Oracle - ASM Filter Driver
modified: 2018-11-02
categories: [Oracle]
tags: 
  - Oracle.ASM
comments: true
---
Oracle ASM Filter Driver is a new way to bind the disk with a permanent path, instead of the using of asmlib or udev.
And, additionally, it provides the protection of the disk device from the crazy operations.

<pre class="prettyprint lang-sh linenums=1 ">
ASMCMD> afd_state
ASMCMD-9526: The AFD state is ‘LOADED’ and filtering is ‘ENABLED’ on host ‘database01’

ASMCMD> afd_lsdsk
——————————————————————————–
Label Filtering Path
================================================================================
CRSDG1 ENABLED /dev/asm-sdd
CRSDG2 ENABLED /dev/asm-sdc
CRSDG3 ENABLED /dev/asm-sdb
MGMT1 ENABLED /dev/asm-sdj
</pre>

<b>Enable:</b><br/>
The simplest way is to choose it during the installment of Grid.<br/>
We can also enable it by using afd_configure in asmcmd.( with CRS off)

<b>Label new disk:</b>
<pre class="prettyprint lang-sh linenums=1 ">
/u01/app/12.2.0/grid/bin/asmcmd afd_label DAT01 /dev/sde
</pre>

<b>Label exist disk:</b>
<pre class="prettyprint lang-sh linenums=1 ">
/u01/app/12.2.0/grid/bin/asmcmd afd_label CRSDG1 /dev/sdd –migrate
</pre>

<b>Unlabel:</b>
<pre class="prettyprint lang-sh linenums=1 ">
/u01/app/12.2.0/grid/bin/asmcmd afd_unlabel DATA1
</pre>

<b>Scan and List( like asmlib )</b>
<pre class="prettyprint lang-sh linenums=1 ">
asmcmd afd_lsdsk
asmcmd afd_scan
</pre>

<b>The status in v$asm_disk</b>
<pre class="prettyprint lang-sql linenums=1 ">
1* select NAME, PATH, mount_status from v$asm_disk
SQL> /

NAME PATH MOUNT_S
———- ——————– ——-
/dev/asm-sdj IGNORED
/dev/asm-sdc IGNORED
/dev/asm-sdd IGNORED
/dev/asm-sdb IGNORED
CRSDG1 AFD:CRSDG1 CACHED
CRSDG2 AFD:CRSDG2 CACHED
MGMT1 AFD:MGMT1 CACHED
CRSDG3 AFD:CRSDG3 CACHED

8 rows selected.
</pre>

<b>Filter</b>
— rewrite the disk
<pre class="prettyprint lang-sql linenums=1 ">
[root@database01 ~]# dd if=/dev/zero of=/dev/oracleafd/disks/CRSDG1 bs=128 count=8000
8000+0 records in
8000+0 records out
1024000 bytes (1.0 MB) copied, 0.0274736 s, 37.3 MB/s

— read the disk and find the data has been cleaned up
[root@database01 ~]# dd if=/dev/oracleafd/disks/CRSDG1 |od -xv|tail -n 10
2000+0 records in
2000+0 records out
1024000 bytes (1.0 MB) copied, 0.358082 s, 2.9 MB/s
3717560 0000 0000 0000 0000 0000 0000 0000 0000
3717600 0000 0000 0000 0000 0000 0000 0000 0000
3717620 0000 0000 0000 0000 0000 0000 0000 0000
3717640 0000 0000 0000 0000 0000 0000 0000 0000
3717660 0000 0000 0000 0000 0000 0000 0000 0000
3717700 0000 0000 0000 0000 0000 0000 0000 0000
3717720 0000 0000 0000 0000 0000 0000 0000 0000
3717740 0000 0000 0000 0000 0000 0000 0000 0000
3717760 0000 0000 0000 0000 0000 0000 0000 0000
3720000

— after reboot, data is remain
[root@database01 ~]# dd if=/dev/oracleafd/disks/CRSDG1 |od -xv|tail -n 10
0+1 records in
0+1 records out
13 bytes (13 B) copied, 3.3587e-05 s, 387 kB/s
0000000 642f 7665 612f 6d73 732d 6464 000a
0000015

— the disk is still a member of votedisk
[grid@database01 ~]$ crsctl query css votedisk
## STATE File Universal Id File Name Disk group
— —– —————– ——— ———
1. ONLINE 88f23183af1f4fdbbf65f93a8b927f27 (AFD:CRSDG1) [CRSDG]
2. ONLINE c761b16740504fd0bfdc280f4cbef571 (AFD:CRSDG2) [CRSDG]
3. ONLINE 24e27d7bf82f4fa4bf8bd69dd3fde7e9 (AFD:CRSDG3) [CRSDG]
Located 3 voting disk(s).
</pre>

<b>The Path of Disk</b>
<pre class="prettyprint lang-sh linenums=1 ">
[grid@database01 ~]$ ls -l /dev/oracleafd/disks
total 16
-rwxrwx— 1 grid oinstall 13 Jun 11 22:54 CRSDG1
-rwxrwx— 1 grid oinstall 13 Jun 11 22:54 CRSDG2
-rwxrwx— 1 grid oinstall 13 Jun 11 22:54 CRSDG3
-rwxrwx— 1 grid oinstall 13 Jun 11 22:54 MGMT1
</pre>