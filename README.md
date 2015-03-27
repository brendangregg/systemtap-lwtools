SystemTap Lightweight Tools (lwtools)
===========================

A miscellaneous collection of in-development and unsupported SystemTap tools and one-liners, which use a limited set of SystemTap capabilities (lightweight). These provide advanced observability for the analysis of system and cloud performance. 

An aim of this collection is to explore what SystemTap can do using basic features and without the need for kernel debuginfo, which can be an onerous requirement. For a similar set of ftrace- and perf\_events-based tools, see the [perf tools](https://github.com/brendangregg/perf-tools) collection, which is more developed. An advantage of these SystemTap tools is that they can perform custom in-kernel aggregations (eg, latency distributions) with low overhead.

Some expert assembly will likely be required with these SystemTap tools. Also see warnings.

## Warnings

SystemTap is in-development kernel-level software, so **there is the possibility of kernel panics or freezes**. I've developed these for use in a lab environment, where panics can be tolerated.

Also, tools that use the inherently unstable dynamic tracing interface may either not work at all (print errors), or work and show bogus data, or work and show plausible but still incorrect data. The same is true for any dynamic tracing-based scripts (including my DTrace ones).

Note that the panics may be due to SystemTap or other kernel origins. Use crash dump analysis to determine the real cause, and please [report bugs](https://sourceware.org/systemtap/wiki/HowToReportBugs).

## Prerequisites

SystemTap, for the stap command, and an environment that is fault tolerant (see Warnings). It is **strongly** recommended that you try the latest version of SystemTap, which probably means compiling from source. See git://sourceware.org/git/systemtap.git, and the [build readme](https://sourceware.org/git/?p=systemtap.git;a=blob_plain;f=README;hb=HEAD).

Where possible, these tools avoid the requirement of having kernel debuginfo.

## Contents

- [oneliners.txt](oneliners.txt): a collection of lightweight SystemTap one-liners.
- [execsnoop-nd.stp](execsnoop-nd.stp): trace process exec() with command line argument details. [Examples](examples/execsnoop-nd_example.txt).
- [opensnoop-nd.stp](opensnoop-nd.stp): trace open() syscalls showing filenames. [Examples](examples/opensnoop-nd_example.txt).
- [killsnoop-nd.stp](killsnoop-nd.stp): trace kill() signals showing process and signal details. [Examples](examples/killsnoop-nd_example.txt).
- [biolatency-nd.stp](biolatency-nd.stp): measure block I/O latency distribution. [Examples](examples/biolatency-nd_example.txt).
- disk/[bitesize-nd.stp](disk/bitesize-nd.stp): measure block I/O size distribution. [Examples](examples/bitesize-nd_example.txt).
- fs/[fslatency-nd.stp](fs/fslatency-nd.stp): measure file system sync read and write latency distribution. [Examples](examples/fslatency-nd_example.txt).
- fs/[fsslower-nd.stp](fs/fsslower-nd.stp): trace slow file system sync reads and writes. [Examples](examples/fsslower-nd_example.txt).
- net/[accept2close-nd.stp](net/accept2close-nd.stp): show socket lifespan, from accept() to close(). [Examples](examples/accept2close-nd_example.txt).
- net/[weblatency-nd.stp](net/weblatency-nd.stp): Trace HTTP GETs and their latency (sendto()->recvfrom()). [Examples](examples/weblatency-nd_example.txt).
- proc/[rwtime-nd.stp](proc/rwtime-nd.stp): summarize read() and write() latency. [Examples](examples/rwtime-nd_example.txt).
- proc/[syscallbypid-nd.stp](proc/syscallbypid-nd.stp): count syscalls with process details. [Examples](examples/syscallbypid-nd_example.txt).

There are also man pages under [man/man8](man/man8).

## Screenshots

Histogram of syscall writes by requested size (one-liner):

<pre># <b>stap -e 'global a; probe nd_syscall.write { a &lt;&lt;&lt; int_arg(3); } probe end { print(@hist_log(a)); }'</b>
^Cvalue |-------------------------------------------------- count
    0 |                                                    0
    1 |@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@                35
    2 |@                                                   1
    4 |@                                                   1
    8 |                                                    0
   16 |@@@@@@@@@@@@@@@@@@@@@@                             22
   32 |@@@@@@@@@@@@@@@                                    15
   64 |@@@@@@@@@@@@@@@@@                                  17
  128 |@@                                                  2
  256 |@@                                                  2
  512 |                                                    0
 1024 |                                                    0
</pre>

Tracing exec() syscalls to show new process execution:

<pre># <b>./execsnoop-nd.stp</b>
TIME                        UID   PPID    PID           COMM ARGS
Sat Jan 31 20:39:37 2015      0    878   5744           sshd /usr/sbin/sshd -R
Sat Jan 31 20:39:38 2015      0   5744   5746           bash -bash
Sat Jan 31 20:39:38 2015      0   5747   5748             id id -un
Sat Jan 31 20:39:38 2015      0   5749   5750       hostname /bin/hostname
Sat Jan 31 20:39:38 2015      0   5751   5752            tty tty -s
Sat Jan 31 20:39:38 2015      0   5751   5753           tput tput colors
Sat Jan 31 20:39:38 2015      0   5754   5755      dircolors dircolors --sh /etc/DIR_COLORS.256color
Sat Jan 31 20:39:38 2015      0   5746   5756           grep grep -qi ^COLOR.*none /etc/DIR_COLORS.256color
Sat Jan 31 20:39:40 2015      0   5746   5757             ls ls --color=auto
Sat Jan 31 20:39:40 2015      0   5746   5758           date date
</pre>

Tracing open() syscalls with process and pathnames:

<pre># <b>./opensnoop-nd.stp</b>
   UID    PID             COMM   FD PATH
     0  13858           iostat    3 /etc/ld.so.cache
     0  13858           iostat    3 /lib64/libc.so.6
     0  13858           iostat    3 /usr/lib/locale/locale-archive
     0  13858           iostat    3 /sys/devices/system/cpu
     0  13858           iostat    3 /proc/diskstats
     0  13858           iostat    3 /etc/localtime
     0  13858           iostat    3 /proc/uptime
     0  13858           iostat    3 /proc/stat
     0  13858           iostat    3 /proc/diskstats
     0  13858           iostat    4 /etc/sysconfig/sysstat.ioconf
     0  13858           iostat    3 /proc/uptime
     0  13858           iostat    3 /proc/stat
     0  13858           iostat    3 /proc/diskstats
[...]
</pre>

Summarizing read() and write() syscall latency for processes named "node":

<pre># <b>./rwtime-nd.stp node</b>
Tracing read/write syscalls for processes named "node"... Hit Ctrl-C to end.
^C
syscall read latency (ns):
value |-------------------------------------------------- count
  512 |                                                     0
 1024 |                                                     0
 2048 |                                                     2
 4096 |@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@     278
 8192 |                                                     1
16384 |                                                     5
32768 |                                                     0
65536 |                                                     0

syscall write latency (ns):
 value |-------------------------------------------------- count
  4096 |                                                     0
  8192 |                                                     0
 16384 |@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@      135
 32768 |@@                                                   8
 65536 |                                                     0
131072 |                                                     0
</pre>

## Internals

These tools are designed to be short, simple, and documented, and use as few SystemTap capabilities as possible. They also avoid using kernel debuginfo, so that these scripts can be used in environments that lack it (eg, cloud instances that are created and destroyed quickly, where including debuginfo can be onerous). This can complicate or obfuscate some scripts.

Each tool has an examples file under [examples](examples), a man page under [man/man8](man/man8), a symlink in [bin](bin), and a link in this README.

## See Also

For SystemTap tools that use its full capabilities, see the [SystemTap examples](http://sourceware.org/systemtap/examples) page, particularly the "best examples" section, as well as the examples in the SystemTap source under share/doc/systemtap/examples. Most of these are likely to require kernel debuginfo.
