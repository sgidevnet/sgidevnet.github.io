# Manually building GCC 4.7.4 IRIX crosscompiler

### Dependencies

Needed dependencies for a Debian/Ubuntu system are:
```
build-essential bison flex libgmp-dev libmpfr-dev libmpc-dev texinfo git
```
You also need a copy of IRIX's headers and libraries. That location will be referred to as $IRIXROOT.

### Binutils

Grab GNU binutils 2.17a from [https://ftp.gnu.org/gnu/binutils/binutils-2.17a.tar.bz2](https://ftp.gnu.org/gnu/binutils/binutils-2.17a.tar.bz2).

Extract it, then build it to a prefix of your choice with a configure line such as this:
`./configure --target=mips-sgi-irix6.5 --prefix=$BINUTILS_PREFIX --with-sysroot=$IRIXROOT --enable-werror=no`

Add `$BINUTILS_PREFIX/bin` to your PATH.

### GCC Build

Clone Onre's GCC 4.7.4 IRIX branch somewhere (we will call this $GCCSRC):
`git clone -b gcc-4_7-irix https://github.com/onre/gcc`

Choose a prefix where everything will be installed. We will call this $YOUR_PREFIX.

Create a directory for target-specific libraries and make a symlink so
that the cross-building process can find the system headers. Create
another symlink to that the cross-compiler finds the correct binutils.

```
mkdir -p $YOUR_PREFIX/mips-sgi-irix6.5/
ln -s $IRIXROOT/usr/include $YOUR_PREFIX/mips-sgi-irix6.5/sys-include
ln -s $BINUTILS_PREFIX/mips-sgi-irix6.5/bin $YOUR_PREFIX/mips-sgi-irix6.5/bin
```

Create a separate build directory for gcc and run the configure command from the source directory:
```
export AS_FOR_TARGET="mips-sgi-irix6.5-as"
export LD_FOR_TARGET="mips-sgi-irix6.5-ld"
export NM_FOR_TARGET="mips-sgi-irix6.5-nm"
export OBJDUMP_FOR_TARGET="mips-sgi-irix6.5-objdump"
export RANLIB_FOR_TARGET="mips-sgi-irix6.5-ranlib"
export STRIP_FOR_TARGET="mips-sgi-irix6.5-strip"
export READELF_FOR_TARGET="mips-sgi-irix6.5-readelf"
export target_configargs="--enable-libstdcxx-threads=no"

$GCCSRC/configure --enable-obsolete --disable-multilib --prefix=$YOUR_PREFIX --target=mips-sgi-irix6.5 --disable-nls --enable-languages=c,c++
```

After this, you may run `make`. Building the GNU info file for GCC will fail, `touch gcc/doc/gcc.info` and run `make` again.



### What's next?

* Multilib and libstdcxx threads need to be tested
