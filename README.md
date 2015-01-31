SystemTap Lightweight Tools (lwtools)
===========================

A miscellaneous collection of in-development and unsupported SystemTap tools and one-liners, which use a limited set of SystemTap capabilities (lightweight). These provide advanced observability for the analysis of system and cloud performance. 

An aim of this collection is to explore what SystemTap can do using basic features and without the need for kernel debuginfo, which can be an onerous requirement. For a similar set of ftrace- and perf\_events-based tools, see the [perf tools](https://github.com/brendangregg/perf-tools) collection, which is more developed.

Some expert assembly will likely be required with these SystemTap tools. Also see warnings.

## Warnings

SystemTap is in-development kernel-level software, so **there is the possibility of kernel panics or freezes**. I've developed these for use in a lab environment, where panics can be tolerated.

Also, tools that use the inherently unstable dynamic tracing interface may either not work at all (print errors), or work and show bogus data, or work and show plausible but still incorrect data. The same is true for any dynamic tracing-based scripts (including my DTrace ones).

Note that the panics may be due to SystemTap or other kernel origins. Use crash dump analysis to determine the real cause, and please [report bugs](https://sourceware.org/systemtap/wiki/HowToReportBugs).

## Prerequisites

SystemTap, for the stap command, and an environment that is fault tolerant (see Warnings). It is **strongly** recommended that you try the latest version of SystemTap, which probably means compiling from source. See git://sourceware.org/git/systemtap.git, and the [build readme](https://sourceware.org/git/?p=systemtap.git;a=blob_plain;f=README;hb=HEAD).

Where possible, these tools avoid the requirement of having kernel debuginfo, which can be onerous in cloud environments. This may obfuscate and complicate some scripts.

## Contents

- [oneliners.txt](oneliners.txt): a collection of lightweight SystemTap one-liners.
- [execsnoop-nd.stp](execsnoop-nd.stp): trace process exec() with command line argument details. [Examples](examples/execsnoop-nd_example.txt).
- [opensnoop-nd.stp](opensnoop-nd.stp): trace open() syscalls showing filenames. [Examples](examples/opensnoop-nd_example.txt).
- [killsnoop-nd.stp](killsnoop-nd.stp): trace kill() signals showing process and signal details. [Examples](examples/killsnoop-nd_example.txt).
- net/[accept2close-nd.stp](net/accept2close-nd.stp): show socket lifespan, from accept() to close(). [Examples](examples/accept2close-nd_example.txt).
- proc/[rwtime-nd.stp](proc/rwtime-nd.stp): summarize read() and write() latency. [Examples](examples/rwtime-nd_example.txt).
- proc/[syscallbypid-nd.stp](proc/syscallbypid-nd.stp): count syscalls with process details. [Examples](examples/syscallbypid-nd_example.txt).

There are also man pages under [man/man8](man/man8).

## Internals

These tools are designed to be short, simple, and documented. Each tool has an examples file under [examples](examples), a man page under [man/man8](man/man8), a symlink in [bin](bin), and a link in this README.

## See Also

For SystemTap tools that use its full capabilities, see the examples in the SystemTap source under share/doc/systemtap/examples. These are likely to require kernel debuginfo.
