# pkgsrc setup notes
* Install https://esp.iki.fi/irix-optlocal.tgz
* Install gcc 8.2 from this tardist http://ports.sgi.sh/lang/gcc82/index.html 

* Run this fix
```
cp /opt/local/gcc-4.7.4/lib/gcc/mips-sgi-irix6.5/4.7.4/include-fixed/limits.h /opt/local/gcc-8.2.0/lib/gcc/mips-sgi-irix6.5/8.2.0/include-fixed/limits.h
```
* Make a script to set up your environment
```
#/root/setenv.sh
#_cdir="gcc-4.4.7"
_cdir="gcc-8.2.0"
export CC=/opt/local/$_cdir/bin/gcc
export CXX=/opt/local/$_cdir/bin/gcc
export CFLAGS="-I/usr/nekoware/include -std=gnu99 -g0 -O2 -mips4"
export CXXFLAGS="-g0 -O2 -mips4"
export CPPFLAGS="-D_POSIX90 -I/usr/nekoware/include"
export LDFLAGS=" -L/usr/local/lib -L/usr/local/lib32 -L/usr/nekoware/lib"
export LD_LIBRARY_PATH=/opt/local/curl/lib:/opt/local/expat/lib:/opt/local/berkeley-db/lib:/opt/local/gmp/lib:/opt/local/mpc/lib:/opt/local/mpfr:/lib:/opt/local/mpfr/lib:/opt/local/$_cdir/lib32:/opt/local/$_cdir/lib
export LIBRARY_PATH=$LD_LIBRARY_PATH
export LD_LIBRARYN32_PATH=$LD_LIBRARY_PATH

export AR=ar
#export AR="ar cr" # worst case use this one
export ARFLAGS=cr
export AR_FLAGS=cr
#export MAKE=gmake
#export PERL=/usr/local/bin/perl
#export FREETYPE_CFLAGS="-I/usr/local/include/freetype2"
#export FREETYPE_LIBS="-L/usr/local/lib -lfreetype"
#export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
#export PKG_CONFIG_LIBDIR=/usr/local/lib
#export FONTCONFIG_PATH=/usr/local/etc/fonts/conf.d
#export FONTCONFIG_FILE=/usr/local/etc/fonts/fonts.conf

source /root/setenv.sh
```
* Get pkgsrc from https://github.com/sgidevnet/pkgsrc
* cd /usr/pkgsrc/bootstrap
* Set up a mk.conf fragment in /usr/pkg/etc/mk.conf
```
# Mon Feb  4 06:40:48 EST 2019

.ifdef BSD_PKG_MK       # begin pkgsrc settings

OPSYS=                  IRIX
ABI=                    32
PKGSRC_COMPILER=        gcc

UNPRIVILEGED=           yes
PKG_DBDIR=              /usr/pkg/pkgdb
LOCALBASE=              /usr/pkg
VARBASE=                /var
PKG_TOOLS_BIN=          /usr/pkg/sbin
PKGINFODIR=             info
PKGMANDIR=              man

MAKE_JOBS=              4

PKGSRC_SHOW_BUILD_DEFS?=yes
FIX_SYSTEM_HEADERS=yes
SMART_MESSAGES=yes

TOOLS_PLATFORM.install?=        /usr/pkg/bin/install-sh
TOOLS_PLATFORM.awk?=            /usr/nekoware/bin/gawk
TOOLS_PLATFORM.sed?=            /usr/nekoware/bin/sed
IMAKEOPTS+=             -DBuildN32 -DSgiISA32=4

.endif                  # end pkgsrc settings
```
* Run bootstrap with this mk.conf "fragment"
```
./bootstrap --mk-fragment mk.conf
```

# Install bzip2
bzip2 is the first major dependency constraint.

```
cd /usr/pkgsrc/archivers/bzip2
bmake

cp bzip2-1.0.6/.libs/bzip2 .buildlink/bin/.
cp ./bzip2-1.0.6/.libs/* .buildlink/lib/.

bmake install
```



-----------
Compile time benchmarks

```
O2 R10000 250Mhz
real    8m13.416s
user    4m45.820s
sys     0m41.799s

O300 R14000 2x600
real    2m28.847s
user    1m57.118s
sys     0m27.539s
```
----------

# List of failures and experiments

Better debug:
```
export PS4='${FUNCNAME[*]} : $LINENO '
```

bmake in libtool failed at bzip2

```
 -D_LARGE_FILES -D_FILE_OFFSET_BITS=64 randtable.c -o randtable.o >/dev/null 2>&1
libtool  --tag=CC --mode=link gcc -L/usr/local/lib -L/usr/local/lib32 -L/usr/nekoware/lib -L/usr/local/lib -L/usr/local/lib32 -L/usr/nekoware/lib -Wl,-R/usr/pkg/lib -I/usr/nekoware/include -std=gnu99 -g0 -O2 -mips4 -I/usr/nekoware/include -std=gnu99 -g0 -O2 -mips4 -D_POSIX90 -I/usr/nekoware/include -D_POSIX90 -I/usr/nekoware/include -Wall -Winline -fomit-frame-pointer -D_LARGEFILE_SOURCE -D_LARGE_FILES -D_FILE_OFFSET_BITS=64 -o libbz2.la  blocksort.lo bzlib.lo compress.lo crctable.lo decompress.lo huffman.lo randtable.lo -version-info 0:0 -rpath /usr/pkg/lib -no-undefined
libtool: link: gcc -shared  -DPIC  .libs/blocksort.o .libs/bzlib.o .libs/compress.o .libs/crctable.o .libs/decompress.o .libs/huffman.o .libs/randtable.o   -L/usr/pkgsrc/archivers/bzip2/work/.buildlink/lib  -Wl,-R/usr/pkg/lib -g0 -O2 -mips4 -g0 -O2 -mips4   -Wl,-soname -Wl,libbz2.so.1 `test -n "sgi1.0" && func_echo_all "-Wl,-set_version -Wl,sgi1.0"` -Wl,-update_registry -Wl,.libs/so_locations -o .libs/libbz2.so.1.0
/opt/local/gcc-8.2.0/lib/gcc/mips-sgi-irix6.5/8.2.0/../../../../mips-sgi-irix6.5/bin/ld: sgi1.0: No such file: No such file or directory
collect2: error: ld returned 1 exit status
```
-----------------


Changes to libtool:
```
#!/bin/ksh to #!/opt/local/bin/bash -x

SHELL="/opt/local/bin/bash"
ECHO="printf %s\\n"
SED="/usr/nekoware/bin/sed"
GREP="/opt/local/bin/grep"
EGREP="/opt/local/bin/egrep"
FGREP="/opt/local/bin/fgrep"
lt_truncate_bin="/usr/bin/dd bs=4096 count=1" 
library_names_spec="\$libname\$release\$shared_ext \$libname\$shared_ext
```
---------------
```
libtool: link: gcc -shared  -DPIC  .libs/blocksort.o .libs/bzlib.o .libs/compress.o .libs/crctable.o .libs/decompress.o .libs/huffman.o .libs/randtable.o   -L/usr/pkgsrc/archivers/bzip2/work/.buildlink/lib  -Wl,-R/usr/pkg/lib -g0 -O2 -mips4 -g0 -O2 -mips4   -Wl,-soname -Wl,libbz2.so.1 `test -n "sgi1.0" && func_echo_all "-Wl,-set_version -Wl,sgi1.0"` -Wl,-update_registry -Wl,.libs/so_locations -o .libs/libbz2.so
++ IFS='
'
+ false
+ eval 'gcc -shared  -DPIC  .libs/blocksort.o .libs/bzlib.o .libs/compress.o .libs/crctable.o .libs/decompress.o .libs/huffman.o .libs/randtable.o   -L/usr/pkgsrc/archivers/bzip2/work/.buildlink/lib  -Wl,-R/usr/pkg/lib -g0 -O2 -mips4 -g0 -O2 -mips4   -Wl,-soname -Wl,libbz2.so.1 `test -n "sgi1.0" && func_echo_all "-Wl,-set_version -Wl,sgi1.0"` -Wl,-update_registry -Wl,.libs/so_locations -o .libs/libbz2.so'
+++ test -n sgi1.0
+++ func_echo_all '-Wl,-set_version -Wl,sgi1.0'
+++ printf '%s\n' '-Wl,-set_version -Wl,sgi1.0'
++ gcc -shared -DPIC .libs/blocksort.o .libs/bzlib.o .libs/compress.o .libs/crctable.o .libs/decompress.o .libs/huffman.o .libs/randtable.o -L/usr/pkgsrc/archivers/bzip2/work/.buildlink/lib -Wl,-R/usr/pkg/lib -g0 -O2 -mips4 -g0 -O2 -mips4 -Wl,-soname -Wl,libbz2.so.1 -Wl,-set_version -Wl,sgi1.0 -Wl,-update_registry -Wl,.libs/so_locations -o .libs/libbz2.so
/opt/local/gcc-8.2.0/lib/gcc/mips-sgi-irix6.5/8.2.0/../../../../mips-sgi-irix6.5/bin/ld: sgi1.0: No such file: No such file or directory
collect2: error: ld returned 1 exit status
+ lt_exit=1
+ test relink = link
+ exit 1
*** Error code 1
```

Running this manually shows the same error. 
```
[root@o2:/usr/pkgsrc/archivers/bzip2/work/bzip2-1.0.6] # gcc -shared -DPIC .libs/blocksort.o .libs/bzlib.o .libs/compress.o .libs/crctable.o .libs/decompress.o .libs/huffman.o .libs/randtable.o -L/usr/pkgsrc/archivers/bzip2/work/.buildlink/lib -Wl,-R/usr/pkg/lib -g0 -O2 -mips4 -g0 -O2 -mips4 -Wl,-soname -Wl,libbz2.so.1 -Wl,-set_version -Wl,sgi1.0 -Wl,-update_registry -Wl,.libs/so_locations -o .libs/libbz2.s
/opt/local/gcc-8.2.0/lib/gcc/mips-sgi-irix6.5/8.2.0/../../../../mips-sgi-irix6.5/bin/ld: sgi1.0: No such file: No such file or directory
```
However, removing these 2 items allows the command to complete: -Wl,sgi1.0 -Wl,.libs/so_locations
```
[root@o2:/usr/pkgsrc/archivers/bzip2/work/bzip2-1.0.6] # gcc -shared -DPIC .libs/blocksort.o .libs/bzlib.o .libs/compress.o .libs/crctable.o .libs/decompress.o .libs/huffman.o .libs/randtable.o -L/usr/pkgsrc/archivers/bzip2/work/.buildlink/lib -Wl,-R/usr/pkg/lib -g0 -O2 -mips4 -g0 -O2 -mips4 -Wl,-soname -Wl,libbz2.so.1 -Wl,-set_version -Wl,-update_registry -o .libs/libbz2.s
```

Changing archive_cmds:
```
archive_cmds="\$CC -shared \$pic_flag \$libobjs \$deplibs \$compiler_flags \$wl-soname \$wl\$soname  \$wl-update_registry -o \$lib"
```

This allowed bmake to complete, but 'bmake install' fails:
```
++ IFS='
'
+ eval '(cd /usr/pkgsrc/archivers/bzip2/work/bzip2-1.0.6; LIBRARY_PATH=/opt/local/curl/lib:/opt/local/expat/lib:/opt/local/berkeley-db/lib:/opt/local/gmp/lib:/opt/local/mpc/lib:/opt/local/mpfr:/lib:/opt/local/mpfr/lib:/opt/local/gcc-8.2.0/lib32:/opt/local/gcc-8.2.0/lib; export LIBRARY_PATH; { test -z "${COMPILER_PATH+set}" || unset COMPILER_PATH || { COMPILER_PATH=; export COMPILER_PATH; }; }; { test -z "${GCC_EXEC_PREFIX+set}" || unset GCC_EXEC_PREFIX || { GCC_EXEC_PREFIX=; export GCC_EXEC_PREFIX; }; }; LD_LIBRARYN32_PATH=/opt/local/curl/lib:/opt/local/expat/lib:/opt/local/berkeley-db/lib:/opt/local/gmp/lib:/opt/local/mpc/lib:/opt/local/mpfr:/lib:/opt/local/mpfr/lib:/opt/local/gcc-8.2.0/lib32:/opt/local/gcc-8.2.0/lib; export LD_LIBRARYN32_PATH; PATH=/usr/pkgsrc/archivers/bzip2/work/.wrapper/bin:/usr/pkgsrc/archivers/bzip2/work/.buildlink/bin:/usr/pkgsrc/archivers/bzip2/work/.tools/bin:/usr/pkgsrc/archivers/bzip2/work/.gcc/bin:/usr/pkg/bin:/usr/pkg/bin:/usr/pkg/sbin:/usr/pkg/bin:/usr/pkg/sbin:/opt/local/bin:/opt/local/sbin:/usr/nekoware/bin:/usr/nekoware/sbin:/usr/etc:/usr/sbin:/usr/bsd:/sbin:/usr/bin:/etc:/usr/etc:/usr/bin/X11:/opt/local/gcc-8.2.0/bin/; export PATH; gcc -Wl,-R/usr/pkg/lib -std=gnu99 -g0 -O2 -mips4 -std=gnu99 -g0 -O2 -mips4 -D_POSIX90 -Wall -Winline -fomit-frame-pointer -D_LARGEFILE_SOURCE -D_LARGE_FILES -D_FILE_OFFSET_BITS=64 -o /tmp/libtool-31123413537/bzip2 bzip2.o  -L/usr/pkg/lib -lbz2 -L/usr/pkgsrc/archivers/bzip2/work/.buildlink/lib -Wl,-rpath -Wl,/usr/pkg/lib)'
++ cd /usr/pkgsrc/archivers/bzip2/work/bzip2-1.0.6
++ LIBRARY_PATH=/opt/local/curl/lib:/opt/local/expat/lib:/opt/local/berkeley-db/lib:/opt/local/gmp/lib:/opt/local/mpc/lib:/opt/local/mpfr:/lib:/opt/local/mpfr/lib:/opt/local/gcc-8.2.0/lib32:/opt/local/gcc-8.2.0/lib
++ export LIBRARY_PATH
++ test -z ''
++ test -z ''
++ LD_LIBRARYN32_PATH=/opt/local/curl/lib:/opt/local/expat/lib:/opt/local/berkeley-db/lib:/opt/local/gmp/lib:/opt/local/mpc/lib:/opt/local/mpfr:/lib:/opt/local/mpfr/lib:/opt/local/gcc-8.2.0/lib32:/opt/local/gcc-8.2.0/lib
++ export LD_LIBRARYN32_PATH
++ PATH=/usr/pkgsrc/archivers/bzip2/work/.wrapper/bin:/usr/pkgsrc/archivers/bzip2/work/.buildlink/bin:/usr/pkgsrc/archivers/bzip2/work/.tools/bin:/usr/pkgsrc/archivers/bzip2/work/.gcc/bin:/usr/pkg/bin:/usr/pkg/bin:/usr/pkg/sbin:/usr/pkg/bin:/usr/pkg/sbin:/opt/local/bin:/opt/local/sbin:/usr/nekoware/bin:/usr/nekoware/sbin:/usr/etc:/usr/sbin:/usr/bsd:/sbin:/usr/bin:/etc:/usr/etc:/usr/bin/X11:/opt/local/gcc-8.2.0/bin/
++ export PATH
++ gcc -Wl,-R/usr/pkg/lib -std=gnu99 -g0 -O2 -mips4 -std=gnu99 -g0 -O2 -mips4 -D_POSIX90 -Wall -Winline -fomit-frame-pointer -D_LARGEFILE_SOURCE -D_LARGE_FILES -D_FILE_OFFSET_BITS=64 -o /tmp/libtool-31123413537/bzip2 bzip2.o -L/usr/pkg/lib -lbz2 -L/usr/pkgsrc/archivers/bzip2/work/.buildlink/lib -Wl,-rpath -Wl,/usr/pkg/lib
/opt/local/gcc-8.2.0/lib/gcc/mips-sgi-irix6.5/8.2.0/../../../../mips-sgi-irix6.5/bin/ld: cannot find -lbz2
collect2: error: ld returned 1 exit status
```
Failure is here:
```
+ func_source /usr/pkgsrc/archivers/bzip2/work/bzip2-1.0.6/bzip2
+ :
+ case $1 in
+ . /usr/pkgsrc/archivers/bzip2/work/bzip2-1.0.6/bzip2
++ sed_quote_subst='s|\([`"$\\]\)|\\\1|g'
++ test -n ''
++ case `(set -o) 2>/dev/null` in
++ set -o posix
++ BIN_SH=xpg4
++ export BIN_SH
++ DUALCASE=1
++ export DUALCASE
++ unset CDPATH
++ relink_command='(cd /usr/pkgsrc/archivers/bzip2/work/bzip2-1.0.6; LIBRARY_PATH=/opt/local/curl/lib:/opt/local/expat/lib:/opt/local/berkeley-db/lib:/opt/local/gmp/lib:/opt/local/mpc/lib:/opt/local/mpfr:/lib:/opt/local/mpfr/lib:/opt/local/gcc-8.2.0/lib32:/opt/local/gcc-8.2.0/lib; export LIBRARY_PATH; { test -z "${COMPILER_PATH+set}" || unset COMPILER_PATH || { COMPILER_PATH=; export COMPILER_PATH; }; }; { test -z "${GCC_EXEC_PREFIX+set}" || unset GCC_EXEC_PREFIX || { GCC_EXEC_PREFIX=; export GCC_EXEC_PREFIX; }; }; LD_LIBRARYN32_PATH=/opt/local/curl/lib:/opt/local/expat/lib:/opt/local/berkeley-db/lib:/opt/local/gmp/lib:/opt/local/mpc/lib:/opt/local/mpfr:/lib:/opt/local/mpfr/lib:/opt/local/gcc-8.2.0/lib32:/opt/local/gcc-8.2.0/lib; export LD_LIBRARYN32_PATH; PATH=/usr/pkgsrc/archivers/bzip2/work/.wrapper/bin:/usr/pkgsrc/archivers/bzip2/work/.buildlink/bin:/usr/pkgsrc/archivers/bzip2/work/.tools/bin:/usr/pkgsrc/archivers/bzip2/work/.gcc/bin:/usr/pkg/bin:/usr/pkg/bin:/usr/pkg/sbin:/usr/pkg/bin:/usr/pkg/sbin:/opt/local/bin:/opt/local/sbin:/usr/nekoware/bin:/usr/nekoware/sbin:/usr/etc:/usr/sbin:/usr/bsd:/sbin:/usr/bin:/etc:/usr/etc:/usr/bin/X11:/opt/local/gcc-8.2.0/bin/; export PATH; gcc -Wl,-R/usr/pkg/lib -std=gnu99 -g0 -O2 -mips4 -std=gnu99 -g0 -O2 -mips4 -D_POSIX90 -Wall -Winline -fomit-frame-pointer -D_LARGEFILE_SOURCE -D_LARGE_FILES -D_FILE_OFFSET_BITS=64 -o @OUTPUT@ bzip2.o  -L/usr/pkg/lib -lbz2 -L/usr/pkgsrc/archivers/bzip2/work/.buildlink/lib -Wl,-rpath -Wl,/usr/pkg/lib)'
```
Failing due to missing -lbz2

## relink_command failure 
```
11302  relink_command="(cd `pwd`; $SHELL \"$progpath\" $preserve_args --mode=relink $libtool_args @inst_prefix_dir@)"
```
Could be due to sed issues, line 860.




Located in this file:
```
grep bz2 /usr/pkgsrc/archivers/bzip2/work/bzip2-1.0.6/bzip2
-L/usr/pkg/lib -lbz2
```
---------------
```
ffunc_mode_install main : 5020 gcc -Wl,-R/usr/pkg/lib -std=gnu99 -g0 -O2 -mips4 -std=gnu99 -g0 -O2 -mips4 -D_POSIX90 -Wall -Winline -fomit-frame-pointer -D_LARGEFILE_SOURCE -D_LARGE_FILES -D_FILE_OFFSET_BITS=64 -o /tmp/libtool-10442415420/bzip2 bzip2.o -L/usr/pkg/lib -L/usr/pkgsrc/archivers/bzip2/work/.buildlink/lib -Wl,-rpath -Wl,/usr/pkg/lib
bzip2.o: In function `license':
bzip2.c:(.text+0x430): undefined reference to `BZ2_bzlibVersion'
```

It appears bmake didn't populate work/.buildlink at all. 

## missing libs in buildlink 

Restarted via 'bmake install'.

/usr/pkgsrc/archivers/bzip2/work/.buildlink/ does have some dirs, but they are empty

>>>>>>>>>>>>
Restored library_names_spec . I am suspicious of the changes sed makes. Now that I have better debugging, I want to see what the original settings do.

Ran bmake
saw .o files in 
```
./bzip2-1.0.6/.libs/blocksort.o
./bzip2-1.0.6/.libs/bzlib.o
./bzip2-1.0.6/.libs/compress.o
./bzip2-1.0.6/.libs/crctable.o
./bzip2-1.0.6/.libs/decompress.o
./bzip2-1.0.6/.libs/huffman.o
./bzip2-1.0.6/.libs/randtable.o
./bzip2-1.0.6/blocksort.o
./bzip2-1.0.6/bzlib.o
./bzip2-1.0.6/compress.o
./bzip2-1.0.6/crctable.o
./bzip2-1.0.6/decompress.o
./bzip2-1.0.6/huffman.o
./bzip2-1.0.6/randtable.o
./bzip2-1.0.6/bzip2.o
./bzip2-1.0.6/bzip2recover.o
```
Noted in work/.pkginstall there is a dedicated INSTALL script with different vars for things like sed and sh.
-- doesn't seem to cause issues.

## libtool relink error:
libtool:   error: error: relink 'bzip2' with the above command before installing it
```
libtool: install: (cd /usr/pkgsrc/archivers/bzip2/work/bzip2-1.0.6; LIBRARY_PATH=/opt/local/curl/lib:/opt/local/expat/lib:/opt/local/berkeley-db/lib:/opt/local/gmp/lib:/opt/local/mpc/lib:/opt/local/mpfr:/lib:/opt/local/mpfr/lib:/opt/local/gcc-8.2.0/lib32:/opt/local/gcc-8.2.0/lib; export LIBRARY_PATH; { test -z "" || unset COMPILER_PATH || { COMPILER_PATH=; export COMPILER_PATH; }; }; { test -z "" || unset GCC_EXEC_PREFIX || { GCC_EXEC_PREFIX=; export GCC_EXEC_PREFIX; }; }; LD_LIBRARYN32_PATH=/opt/local/curl/lib:/opt/local/expat/lib:/opt/local/berkeley-db/lib:/opt/local/gmp/lib:/opt/local/mpc/lib:/opt/local/mpfr:/lib:/opt/local/mpfr/lib:/opt/local/gcc-8.2.0/lib32:/opt/local/gcc-8.2.0/lib; export LD_LIBRARYN32_PATH; PATH=/usr/pkgsrc/archivers/bzip2/work/.wrapper/bin:/usr/pkgsrc/archivers/bzip2/work/.buildlink/bin:/usr/pkgsrc/archivers/bzip2/work/.tools/bin:/usr/pkgsrc/archivers/bzip2/work/.gcc/bin:/usr/pkg/bin:/usr/pkg/bin:/usr/pkg/sbin:/usr/pkg/bin:/usr/pkg/sbin:/opt/local/bin:/opt/local/sbin:/usr/nekoware/bin:/usr/nekoware/sbin:/usr/etc:/usr/sbin:/usr/bsd:/sbin:/usr/bin:/etc:/usr/etc:/usr/bin/X11:/opt/local/gcc-8.2.0/bin/; export PATH; gcc -Wl,-R/usr/pkg/lib -std=gnu99 -g0 -O2 -mips4 -std=gnu99 -g0 -O2 -mips4 -D_POSIX90 -Wall -Winline -fomit-frame-pointer -D_LARGEFILE_SOURCE -D_LARGE_FILES -D_FILE_OFFSET_BITS=64 -o /tmp/libtool-16277425941/bzip2 bzip2.o  -L/usr/pkg/lib -lbz2 -L/usr/pkgsrc/archivers/bzip2/work/.buildlink/lib -Wl,-rpath -Wl,/usr/pkg/lib)
```
```
libtool: install: (cd /usr/pkgsrc/archivers/bzip2/work/bzip2-1.0.6; 
    LIBRARY_PATH=/opt/local/curl/lib:/opt/local/expat/lib:/opt/local/berkeley-db/lib:/opt/local/gmp/lib:/opt/local/mpc/lib:\
        /opt/local/mpfr:/lib:/opt/local/mpfr/lib:/opt/local/gcc-8.2.0/lib32:/opt/local/gcc-8.2.0/lib; 
    export LIBRARY_PATH; 
    { test -z "" || \
        unset COMPILER_PATH || \
        { COMPILER_PATH=; 
            export COMPILER_PATH; \
        }; \
    }; 
    { test -z "" || \
        unset GCC_EXEC_PREFIX || \
        { GCC_EXEC_PREFIX=; \
                export GCC_EXEC_PREFIX; \
        }; \
    }; 
    LD_LIBRARYN32_PATH=/opt/local/curl/lib:/opt/local/expat/lib:/opt/local/berkeley-db/lib:/opt/local/gmp/lib:/opt/local/mpc/lib:\
        /opt/local/mpfr:/lib:/opt/local/mpfr/lib:/opt/local/gcc-8.2.0/lib32:/opt/local/gcc-8.2.0/lib; 
    export LD_LIBRARYN32_PATH; \
    PATH=/usr/pkgsrc/archivers/bzip2/work/.wrapper/bin:/usr/pkgsrc/archivers/bzip2/work/.buildlink/bin:\
        /usr/pkgsrc/archivers/bzip2/work/.tools/bin:/usr/pkgsrc/archivers/bzip2/work/.gcc/bin:/usr/pkg/bin:\
        /usr/pkg/bin:/usr/pkg/sbin:/usr/pkg/bin:/usr/pkg/sbin:/opt/local/bin:/opt/local/sbin:/usr/nekoware/bin:\
        /usr/nekoware/sbin:/usr/etc:/usr/sbin:/usr/bsd:/sbin:/usr/bin:/etc:/usr/etc:/usr/bin/X11:/opt/local/gcc-8.2.0/bin/; \
    export PATH; \
    gcc -Wl,-R/usr/pkg/lib \
        -std=gnu99 \
        -g0 \
        -O2 \
        -mips4 \
        -std=gnu99 \
        -g0 \
        -O2 \
        -mips4 \
        -D_POSIX90 \
        -Wall \
        -Winline \
        -fomit-frame-pointer \
        -D_LARGEFILE_SOURCE \
        -D_LARGE_FILES \
        -D_FILE_OFFSET_BITS=64 \
        -o /tmp/libtool-16277425941/bzip2 bzip2.o  \
        -L/usr/pkg/lib \
        -lbz2 \
        -L/usr/pkgsrc/archivers/bzip2/work/.buildlink/lib \
        -Wl,-rpath \
        -Wl,/usr/pkg/lib\
)
```
------
-lbz2 found in work/.wrapper/tmp/cache

----------------

## New idea: adjust per-pkg Makefiles to replace libtool with 'cp' for 'bmake install'


Wrapper files are found in work/bzip2-1.0.6. 
The actual binaries are in work/bzip2-1.0.6/.libs



I adjusted bzip2's Makefile to override LIBTOOL and other vars to just cp the files.
```
LIBTOOL= cp
LN= ln -s
WRKSRC= work/bzip2-1.0.6/
DESTDIR= work/.destdir/
PREFIX= usr/pkg

....

do-install:
        ${LIBTOOL} ${WRKSRC}/libbz2.la ${DESTDIR}${PREFIX}/lib
        ${LIBTOOL} ${WRKSRC}/bzip2 ${DESTDIR}${PREFIX}/bin
        ${LN} -f ${DESTDIR}${PREFIX}/bin/bzip2 ${DESTDIR}${PREFIX}/bin/bunzip2
        ${LN} -f ${DESTDIR}${PREFIX}/bin/bzip2 ${DESTDIR}${PREFIX}/bin/bzcat
        ${LIBTOOL}  ${WRKSRC}/bzip2recover ${DESTDIR}${PREFIX}/bin
        ${INSTALL_MAN} ${WRKSRC}/bzip2.1 ${DESTDIR}${PREFIX}/${PKGMANDIR}/man1
        cd ${DESTDIR}${PREFIX}/${PKGMANDIR}/man1 && rm -f bunzip2.1 bzcat.1 bzip2recover.1
        ${LN} -s bzip2.1 ${DESTDIR}${PREFIX}/${PKGMANDIR}/man1/bunzip2.1
        ${LN} -s bzip2.1 ${DESTDIR}${PREFIX}/${PKGMANDIR}/man1/bzcat.1
        ${LN} -s bzip2.1 ${DESTDIR}${PREFIX}/${PKGMANDIR}/man1/bzip2recover.1
        ${INSTALL_DATA} ${WRKSRC}/bzlib.h ${DESTDIR}${PREFIX}/include
        ${INSTALL_SCRIPT} ${WRKSRC}/bzmore ${DESTDIR}${PREFIX}/bin/bzmore
        ${LN} -s bzmore ${DESTDIR}${PREFIX}/bin/bzless
        ${INSTALL_MAN} ${WRKSRC}/bzmore.1 ${DESTDIR}${PREFIX}/${PKGMANDIR}/man1
        ${LN} -s bzmore.1 ${DESTDIR}${PREFIX}/${PKGMANDIR}/man1/bzless.1
        ${INSTALL_SCRIPT} ${WRKSRC}/bzdiff ${DESTDIR}${PREFIX}/bin/bzdiff
        ${LN} -s bzdiff ${DESTDIR}${PREFIX}/bin/bzcmp
        ${INSTALL_MAN} ${WRKSRC}/bzdiff.1 ${DESTDIR}${PREFIX}/${PKGMANDIR}/man1
        ${LN} -s bzdiff.1 ${DESTDIR}${PREFIX}/${PKGMANDIR}/man1/bzcmp.1
        ${INSTALL_SCRIPT} ${WRKSRC}/bzgrep ${DESTDIR}${PREFIX}/bin/bzgrep
        ${LN} -s bzgrep ${DESTDIR}${PREFIX}/bin/bzegrep
        ${LN} -s bzgrep ${DESTDIR}${PREFIX}/bin/bzfgrep
        ${INSTALL_MAN} ${WRKSRC}/bzgrep.1 ${DESTDIR}${PREFIX}/${PKGMANDIR}/man1
        ${LN} -s bzgrep.1 ${DESTDIR}${PREFIX}/${PKGMANDIR}/man1/bzegrep.1
        ${LN} -s bzgrep.1 ${DESTDIR}${PREFIX}/${PKGMANDIR}/man1/bzfgrep.1
```

!!! NOTE The BSD Makefile uses "Tabs" instead of 4 spaces, like cavemen.


Using 'cp' instead of libtool allows make install to complete, but fails to link the libs properly.

Per above, command is:
```
cd /usr/pkgsrc/archivers/bzip2/work/bzip2-1.0.6
LIBRARY_PATH=/opt/local/curl/lib:/opt/local/expat/lib:/opt/local/berkeley-db/lib:/opt/local/gmp/lib:\
    /opt/local/mpc/lib:/opt/local/mpfr:/lib:/opt/local/mpfr/lib:/opt/local/gcc-8.2.0/lib32:/opt/local/gcc-8.2.0/lib
export LIBRARY_PATH
{ test -z "${COMPILER_PATH+set}" || \
    unset COMPILER_PATH || \
        { COMPILER_PATH=
            export COMPILER_PATH
        }
}
{ test -z "${GCC_EXEC_PREFIX+set}" || \
    unset GCC_EXEC_PREFIX || \
        { GCC_EXEC_PREFIX=
            export GCC_EXEC_PREFIX
        }
}
LD_LIBRARYN32_PATH=/opt/local/curl/lib:/opt/local/expat/lib:/opt/local/berkeley-db/lib:/opt/local/gmp/lib:\
    /opt/local/mpc/lib:/opt/local/mpfr:/lib:/opt/local/mpfr/lib:/opt/local/gcc-8.2.0/lib32:/opt/local/gcc-8.2.0/lib
export LD_LIBRARYN32_PATH
PATH=/usr/pkgsrc/archivers/bzip2/work/.wrapper/bin:/usr/pkgsrc/archivers/bzip2/work/.buildlink/bin:/usr/pkgsrc/archivers/bzip2/work/.tools/bin:\
    /usr/pkgsrc/archivers/bzip2/work/.gcc/bin:/usr/pkg/bin:/usr/pkg/bin:/usr/pkg/sbin:/usr/pkg/bin:/usr/pkg/sbin:/opt/local/bin:\
    /opt/local/sbin:/usr/nekoware/bin:/usr/nekoware/sbin:/usr/etc:/usr/sbin:/usr/bsd:/sbin:/usr/bin:/etc:/usr/etc:/usr/bin/X11:\
    /opt/local/gcc-8.2.0/bin/
export PATH
gcc -Wl,-R/usr/pkg/lib -std=gnu99 -g0 -O2 -mips4 -std=gnu99 -g0 -O2 -mips4 -D_POSIX90 -Wall -Winline -fomit-frame-pointer -D_LARGEFILE_SOURCE -D_LARGE_FILES -D_FILE_OFFSET_BITS=64 -o @OUTPUT@ bzip2.o  -L/usr/pkg/lib -lbz2 -L/usr/pkgsrc/archivers/bzip2/work/.buildlink/lib -Wl,-rpath -Wl,/usr/pkg/lib
```
----------

Note here, .buildlink/lib is empty. 

I copied the appropriate items into .buildlink/ and it created the package properly.
```
cp bzip2-1.0.6/.libs/bzip2 .buildlink/bin/.
cp ./bzip2-1.0.6/.libs/* .buildlink/lib/.
```

This solved the problem.

---------- 
# Building XZ

xz is the next target, as it is required by many packages.

```
++ gcc -D_REENTRANT -fvisibility=hidden -Wall -Wextra -Wvla -Wformat=2 -Winit-self -Wmissing-include-dirs -Wstrict-aliasing -Wfloat-equal -Wundef -Wshadow -Wpointer-arith -Wbad-function-cast -Wwrite-strings -Wlogical-op -Waggregate-return -Wstrict-prototypes -Wold-style-definition -Wmissing-prototypes -Wmissing-declarations -Wmissing-noreturn -Wredundant-decls -std=gnu99 -g0 -O2 -mips4 -I/usr/pkgsrc/archivers/xz/work/.buildlink/include -Wl,-R/usr/lib32 -Wl,-R/usr/pkg/lib -o .libs/xzdec xzdec-xzdec.o xzdec-tuklib_progname.o xzdec-tuklib_exit.o -L/usr/pkgsrc/archivers/xz/work/.buildlink/lib ../../src/liblzma/.libs/liblzma.so ../../lib/libgnu.a -lpthread -lrt -Wl,-rpath -Wl,/usr/pkgsrc/archivers/xz/work/xz-5.2.4/src/liblzma/.libs:/usr/pkg/lib
--- lzmadec ---
lzmadec-xzdec.o: In function `main':
(.text.startup+0x68): undefined reference to `rpl_getopt_long'
lzmadec-xzdec.o: In function `main':
(.text.startup+0x90): undefined reference to `rpl_optind'
lzmadec-xzdec.o: In function `main':
(.text.startup+0xc0): undefined reference to `rpl_getopt_long'
lzmadec-xzdec.o: In function `main':
(.text.startup+0xf0): undefined reference to `rpl_optind'
collect2: error: ld returned 1 exit status
```

@HAL says to not use neko libs
```
export CC=/opt/local/$_cdir/bin/gcc
export CXX=/opt/local/$_cdir/bin/gcc
#export CFLAGS="-I/usr/nekoware/include -std=gnu99 -g0 -O2 -mips4"
export CFLAGS="-std=gnu99 -g0 -O2 -mips4"
export CXXFLAGS="-g0 -O2 -mips4"
#export CPPFLAGS="-D_POSIX90 -I/usr/nekoware/include"
export CPPFLAGS="-D_POSIX90"
#export LDFLAGS=" -L/usr/local/lib -L/usr/local/lib32 -L/usr/nekoware/lib"
export LDFLAGS=" -L/usr/local/lib -L/usr/local/lib32"
export LD_LIBRARY_PATH=/opt/local/curl/lib:/opt/local/expat/lib:/opt/local/berkeley-db/lib:/opt/local/gmp/lib:/opt/local/mpc/lib:/opt/local/mpfr:/lib:/opt/local/mpfr/lib:/opt/local/$_cdir/lib32:/opt/local/$_cdir/lib
export LIBRARY_PATH=$LD_LIBRARY_PATH
export LD_LIBRARYN32_PATH=$LD_LIBRARY_PATH

export AR=ar
export ARFLAGS=cr
export AR_FLAGS=cr
```
------

this failed.

Noticed ya_getopt from @onre. 
```
export CPPFLAGS="-D_POSIX90 -I/opt/local/ya_getopt/include"
export LDFLAGS=" -L/usr/local/lib -L/usr/local/lib32 -L/opt/local/ya_getopt/lib"
export LD_LIBRARY_PATH=/opt/local/curl/lib:/opt/local/expat/lib:/opt/local/berkeley-db/lib:/opt/local/gmp/lib:/opt/local/mpc/lib:/opt/local/mpfr:/lib:/opt/local/mpfr/lib:/opt/local/$_cdir/lib32:/opt/local/$_cdir/lib:/opt/local/ya_getopt/lib"
```
This also failed.

Current error example:

```
++ printf '%s\n' 'libtool: link: gcc -D_REENTRANT -fvisibility=hidden -Wall -Wextra -Wvla -Wformat=2 -Winit-self -Wmissing-include-dirs -Wstrict-aliasing -Wfloat-equal -Wundef -Wshadow -Wpointer-arith -Wbad-function-cast -Wwrite-strings -Wlogical-op -Waggregate-return -Wstrict-prototypes -Wold-style-definition -Wmissing-prototypes -Wmissing-declarations -Wmissing-noreturn -Wredundant-decls -std=gnu99 -g0 -O2 -mips4 -I/usr/pkg/include -I/usr/include -Wl,-R/usr/lib32 -Wl,-R/usr/pkg/lib -o .libs/xzdec xzdec-xzdec.o xzdec-tuklib_progname.o xzdec-tuklib_exit.o  -L/usr/local/lib -L/usr/local/lib32 -L/opt/local/ya_getopt/lib -L/usr/pkg/lib -L/usr/lib32 ../../src/liblzma/.libs/liblzma.so -L/usr/pkgsrc/archivers/xz/work/.buildlink/lib ../../lib/libgnu.a -lpthread -lrt -Wl,-rpath -Wl,/usr/pkgsrc/archivers/xz/work/xz-5.2.4/src/liblzma/.libs:/usr/pkg/lib'
libtool: link: gcc -D_REENTRANT -fvisibility=hidden -Wall -Wextra -Wvla -Wformat=2 -Winit-self -Wmissing-include-dirs -Wstrict-aliasing -Wfloat-equal -Wundef -Wshadow -Wpointer-arith -Wbad-function-cast -Wwrite-strings -Wlogical-op -Waggregate-return -Wstrict-prototypes -Wold-style-definition -Wmissing-prototypes -Wmissing-declarations -Wmissing-noreturn -Wredundant-decls -std=gnu99 -g0 -O2 -mips4 -I/usr/pkg/include -I/usr/include -Wl,-R/usr/lib32 -Wl,-R/usr/pkg/lib -o .libs/xzdec xzdec-xzdec.o xzdec-tuklib_progname.o xzdec-tuklib_exit.o  -L/usr/local/lib -L/usr/local/lib32 -L/opt/local/ya_getopt/lib -L/usr/pkg/lib -L/usr/lib32 ../../src/liblzma/.libs/liblzma.so -L/usr/pkgsrc/archivers/xz/work/.buildlink/lib ../../lib/libgnu.a -lpthread -lrt -Wl,-rpath -Wl,/usr/pkgsrc/archivers/xz/work/xz-5.2.4/src/liblzma/.libs:/usr/pkg/lib
++ IFS='
'
++ :
+ false
+ eval 'gcc -D_REENTRANT -fvisibility=hidden -Wall -Wextra -Wvla -Wformat=2 -Winit-self -Wmissing-include-dirs -Wstrict-aliasing -Wfloat-equal -Wundef -Wshadow -Wpointer-arith -Wbad-function-cast -Wwrite-strings -Wlogical-op -Waggregate-return -Wstrict-prototypes -Wold-style-definition -Wmissing-prototypes -Wmissing-declarations -Wmissing-noreturn -Wredundant-decls -std=gnu99 -g0 -O2 -mips4 -I/usr/pkg/include -I/usr/include -Wl,-R/usr/lib32 -Wl,-R/usr/pkg/lib -o .libs/xzdec xzdec-xzdec.o xzdec-tuklib_progname.o xzdec-tuklib_exit.o  -L/usr/local/lib -L/usr/local/lib32 -L/opt/local/ya_getopt/lib -L/usr/pkg/lib -L/usr/lib32 ../../src/liblzma/.libs/liblzma.so -L/usr/pkgsrc/archivers/xz/work/.buildlink/lib ../../lib/libgnu.a -lpthread -lrt -Wl,-rpath -Wl,/usr/pkgsrc/archivers/xz/work/xz-5.2.4/src/liblzma/.libs:/usr/pkg/lib'
++ gcc -D_REENTRANT -fvisibility=hidden -Wall -Wextra -Wvla -Wformat=2 -Winit-self -Wmissing-include-dirs -Wstrict-aliasing -Wfloat-equal -Wundef -Wshadow -Wpointer-arith -Wbad-function-cast -Wwrite-strings -Wlogical-op -Waggregate-return -Wstrict-prototypes -Wold-style-definition -Wmissing-prototypes -Wmissing-declarations -Wmissing-noreturn -Wredundant-decls -std=gnu99 -g0 -O2 -mips4 -I/usr/pkg/include -I/usr/include -Wl,-R/usr/lib32 -Wl,-R/usr/pkg/lib -o .libs/xzdec xzdec-xzdec.o xzdec-tuklib_progname.o xzdec-tuklib_exit.o -L/usr/local/lib -L/usr/local/lib32 -L/opt/local/ya_getopt/lib -L/usr/pkg/lib -L/usr/lib32 ../../src/liblzma/.libs/liblzma.so -L/usr/pkgsrc/archivers/xz/work/.buildlink/lib ../../lib/libgnu.a -lpthread -lrt -Wl,-rpath -Wl,/usr/pkgsrc/archivers/xz/work/xz-5.2.4/src/liblzma/.libs:/usr/pkg/lib
xzdec-xzdec.o: In function `main':
(.text.startup+0x68): undefined reference to `rpl_getopt_long'
xzdec-xzdec.o: In function `main':
(.text.startup+0x90): undefined reference to `rpl_optind'
xzdec-xzdec.o: In function `main':
(.text.startup+0xc0): undefined reference to `rpl_getopt_long'
xzdec-xzdec.o: In function `main':
(.text.startup+0xf0): undefined reference to `rpl_optind'
collect2: error: ld returned 1 exit status
+ _G_status=1
+ test 0 -ne 1
+ eval '(exit 1); exit $?'
++ exit 1
++ exit 1
*** Error code 1

Stop.
bmake[3]: stopped in /usr/pkgsrc/archivers/xz/work/xz-5.2.4/src/xzdec
*** Error code 1
```