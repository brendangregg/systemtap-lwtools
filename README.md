SystemTap Lightweight Tools (lwtools)
===========================

A miscellaneous collection of in-development and unsupported SystemTap tools and one-liners, which use a limited set of SystemTap capabilities (lightweight). These provide advanced observability for the analysis of system and cloud performance. 

An aim of this collection is to explore what SystemTap can do using basic features and without the need for kernel debuginfo, which can be an onerous requirement. For a similar set of ftrace- and perf\_events-based tools, see the [perf tools](https://github.com/brendangregg/perf-tools) collection, which is more developed.

Some expert assembly will likely be required with these SystemTap tools. Also see warnings.

## Warnings

SystemTap is in-development kernel-level software, so **there is the possibility of kernel panics or freezes**. I've developed these for study of simulated issues in a lab environment, where panics can be tolerated.

Also, since dynamic tracing-based tools use an inherently unstable interface, these may either not work at all (print errors), or work and show obviously bogus values, or work and show plausible but still incorrect values. The same is true for any dynamic tracing-based scripts (including my DTrace ones).

Note that the panics are not necessarily due to SystemTap itself, but may be due to it exercising bugs in typically dormant kernel code. Use crash dump analysis to determine the real cause, and please [report bugs](https://sourceware.org/systemtap/wiki/HowToReportBugs).

## Prerequisites

SystemTap, which provides the stap command, and an environment that is fault tolerant (see Warnings). It's **strongly** recommended that you try the latest version of SystemTap, which probably means fetching and compiling from source. See git://sourceware.org/git/systemtap.git, and the [build readme](https://sourceware.org/git/?p=systemtap.git;a=blob_plain;f=README;hb=HEAD).

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

These tools are designed to be short, simple, and documented, since it is expected that they will need to be revisited, understood, and adjusted to match developments in SystemTap and the target software.

## See Also

For SystemTap tools that use its full capabilities, see the examples in the SystemTap source under share/doc/systemtap/examples. These are likely to require kernel debuginfo.
