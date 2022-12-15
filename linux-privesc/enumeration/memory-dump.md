# Memory Dump

## Mannual process

It is possible to read application memory by executing the following commands given the application's PID:

```
cat /proc/[pid]/maps
```

The output is something like the following

```
00400000-00421000 r-xp 00000000 08:01 592398     /usr/libexec/dovecot/pop3-login
00621000-00622000 rw-p 00021000 08:01 592398     /usr/libexec/dovecot/pop3-login
00622000-0066a000 rw-p 00622000 00:00 0          [heap]
3e73200000-3e7321c000 r-xp 00000000 08:01 229378 /lib64/ld-2.5.so
3e7341b000-3e7341c000 r--p 0001b000 08:01 229378 /lib64/ld-2.5.so
```

the rows can contain different types of information depending on their access permissions:

* **r-xp**: application code loaded in memory
* **rw-p**: readable and writable memory area
* **r--p**: read only memory area

To dump the actual contents of the memory area use gdb passing as parameters the output file and the address ranges (the first two row values printed by cat /proc/\[pid]/maps)

**gdb**

```
gdb --pid [pid]
(gdb) dump memory <output> <start addr> <end addr>
```

**dd**

```
dd if=/proc/<PID>/mem bs=4096 iflag=skip_bytes,count_bytes skip=<start addr> count=$(( <end addr> - <start addr> )) of="<output file>"
```
