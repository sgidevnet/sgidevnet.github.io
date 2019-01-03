# IRIX Binaries

## GCC

[https://esp.iki.fi/gcc-4.7.4-irix-3.tgz](https://esp.iki.fi/gcc-4.7.4-irix-3.tgz)

Cross-built on a Linux host. Includes GNU binutils 2.17.

This is an updated version, fixing a couple of stupidities in the previous tarball. After installing it, edit /opt/local/gcc-4.7.4/libexec/gcc/mips-sgi-irix6.5/4.7.4/install-tools/mkheaders to point to your copy of bash (it appears in three places in the file) and run the script to get fixed versions of headers in /opt/local/gcc-4.7.4/lib/gcc/mips-sgi-irix6.5/4.7.4/include-fixed.

## GNU grep 3.1

[https://esp.iki.fi/irix-gnu-grep.tgz](https://esp.iki.fi/irix-gnu-grep.tgz)

## "Lots of stuff"

[https://esp.iki.fi/irix-optlocal.tgz](https://esp.iki.fi/irix-optlocal.tgz)

Inside the file there is an optlocal.profile file which sets rld search paths accordingly.

**NOTE**: this includes an /etc/sudoers file with my username. Replace with yours instead of blaming me about trying to hack you.

**NOTE**: curl expects to find a cacert.pem containing certificates in /opt/local/etc. More info.

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

