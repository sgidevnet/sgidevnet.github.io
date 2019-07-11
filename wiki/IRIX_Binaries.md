# IRIX Binaries

## Recommended: Uploaded tardists

We are uploading tardists of packages at [ports.sgi.sh](http://ports.sgi.sh)!

## GCC

Tardists are available on [ports.sgi.sh](http://ports.sgi.sh) in the `lang` category.

## GNU grep 3.1

[https://esp.iki.fi/irix-gnu-grep.tgz](https://esp.iki.fi/irix-gnu-grep.tgz)

This supports the -r option, making it rather useful for finding out where something is #defined.

## "Lots of stuff"

[https://esp.iki.fi/irix-optlocal.tgz](https://esp.iki.fi/irix-optlocal.tgz)

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

