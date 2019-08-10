# IRIX Binaries

## Step 1: Onre's optlocal
[https://esp.iki.fi/irix-optlocal.tgz](https://esp.iki.fi/irix-optlocal.tgz)

This is a large tarball of precompiled IRIX binaries. 

This may be used to 'bootstrap' your IRIX system.

### Usage
Fetch this file (using some method) to your IRIX host. As root, extract it to /.

Inside the file there is an optlocal.profile file which sets rld search paths accordingly.

**NOTE**: this includes an /etc/sudoers file with my username. Replace with yours instead of blaming onre for trying to hack you.

**NOTE**: curl expects to find a cacert.pem containing certificates in /opt/local/etc. You can get it here: [https://curl.haxx.se/ca/cacert.pem](https://curl.haxx.se/ca/cacert.pem).

What's included:

* openssl 1.0.2q
* curl 7.6.2
* bash 4.4
* m4 1.4.17
* berkeley db 6.2.23NC
* sudo 1.8.26
* xz 5.2.4
* perl 5.28.0
* autoconf 2.69
* automake 1.16.1
* libtool 2.4.6
* git 2.19.2
* libexpat 2.2.6
* GNU make 4.2.1
* GNU grep 3.1


## Step 2: Tardist Ports at ports.sgi.sh

[ports.sgi.sh](http://ports.sgi.sh) contains current known-good ports packaged as tardist archives.

### GCC 4.7.4, GCC 8.2.0, Python 3.5

Tardists are available on [ports.sgi.sh](http://ports.sgi.sh) in the `lang` category.

## Step 3: irixports

[irixports](https://github.com/larb0b/irixports) simple ports collection that includes software and patches for compiling on IRIX

## Step 4: SGUG Developer releases

[SGIDevnet repos](https://github.com/sgidevnet/) early access and WIP porting projects


## Other resources
### pkgsrc

Unxmaal and Onre did some work with pkgsrc but the effort stalled due to the size of the project. If you're interested, here are notes:

* [pkgsrc build notes](pkgsrc_build_notes.html)
* [Unxmaal's pkgsrc build notes](unxmaal_pkgsrc_build_notes.md)