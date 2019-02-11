# General information about porting software to IRIX

## Introduction

IRIX has a long history, and for this reason there is a lot of stuff
in headers allowing software from different eras to be built on
it. Given that now, in 2019, most porting tends to focus on recent
software, this can cause all kinds of headache when trying to figure
out how to get all the necessary definitions included. Here we attempt
to document some of that to avoid diving into the deep end every time
the compiler complains to us about an `incomplete type` or an
`implicit declaration`.

## Where to find what

### struct timeval

A bit surprisingly, this is easiest found in `<sys/resource.h>`

## Equivalences

### anonymous mmap()

IRIX does not have this. Instead, you can open /dev/zero, establish a
private mmap and immediately close /dev/zero. Functionally this should
be a close equivalent.

### CLOCK_MONOTONIC

This definition is used to set up a clock that has an arbitrary
starting point in time and thus is not affected by changing system
time - basically a hardware counter unaffected by wall clock
time. IRIX has this functionality in `<sys/ptimers.h>`, it's called
`CLOCK_SGI_CYCLE`.

## Missing stuff

### setenv and getenv

Yeah, they simply aren't there. A wrapper is rather trivial to write.

### b64_ntop and b64_pton

These symbols exist in libc.so but are hidden. If an application needs
these, they need to be sourced from elsewhere. A suitable
implementation for copypasting into your code can be found in glibc.

## Miscellaneous information

### _SGIAPI, _ABIAPI etc

There's an explanation on how these work in `<sys/standards.h>`
comments. Basically - the larger value you assign to _XOPEN_SOURCE,
the more modern featureset you will get.
