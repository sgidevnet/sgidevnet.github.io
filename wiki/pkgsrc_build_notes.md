# pkgsrc build notes

### DISCLAIMER

**THIS DOCUMENT IS A WORK IN PROGRESS!**

### ANOTHER DISCLAIMER

We know much of this is hacky. If you have time and/or resources to do
stuff in a more nice way, please do share the results!

### GENERAL NOTES

- quick way of searching for packages in pkgsrc: `cd ${wherever_you_put_pkgsrc}; ls -d */packagename`
- looks like _POSIX90 and _NO_ANSIMODE are always defined, at least with --std=c99
- in due time I (onre) will make proper patches of all of this

### OUTSTANDING ISSUES

* if there's a package which depends on its own libraries (say, bzip2
  which uses libbz2 in linking the bzip2 command), buildlink fails to
  provide the library in work/.buildlink/lib - this can be fixed by
  manually symlinking the libraries from
  work/.destdir/${your_prefix}/lib to work/.buildlink/lib and
  re-running bmake install

  * this might be an actual bug in buildlink. more investigation is needed.

* for packages using libnbcompat as feature (not the installed
  package), get them so that the opterr/optopt dance does not need to
  be repeated on every one of them

* libtool wrapper script failure: if you read this from a package
  description, read xz package's description to see what it's
  about. (pain)
  
* weird linking error with ld not finding "sgi2.0": libtool script
  does not believe we have GNU binutils. it has to be edited directly
  to get rid of this behaviour.

### PER PACKAGE NOTES

If something is marked as WIP, it's work in progress and won't work as is.

##### bootstrap:
* requires CC to be set, otherwise tries to use MipsPRO at a certain step
* after bootstrap you need to manually add this to your etc/mk.conf to
  make sure pkgsrc does not use the system ksh.

```
SH=                     ${your_prefix}/pkg/bin/pdksh
CONFIG_SHELL=           ${your_prefix}/pkg/bin/pdksh
```

(there was nonsense here about c99. gcc mkheaders script took care of that.)

##### bsdtar:

libarchive-3.3.2/libarchive/archive_read_disk_posix.c:
steal this from <sys/dir.h> which conflicts with <dirent.h>:
```
#define dirfd(dirp)     ((dirp)->dd_fd)
```

##### gettext-tools:


gettext-0.19.8.1/gettext-tools/gnulib-lib/libxml/trionan.c:
```
#include <float.h>
```

##### xz:
aforementioned libtool/buildlink bug

##### perl5:
* find the perl link command (string '-o perl') in work/.work.log and relink without
rpath /usr/lib32

* change LD_LIBRARY_PATH to LD_LIBRARYN32_PATH in Makefile

##### help2man:
in bindtextdomain.c:
```
#ifdef __sgi
static void *RTLD_NEXT = 0;
#endif
```

##### gtexinfo:
half of this needs SIZE_MAX. add this to top of 
tp/Texinfo/Convert/XSParagraph/config.h:
```
#include <stdint-gcc.h>
```

##### screen:
socket.c:
```
#define SCM_RIGHTS 1
```

##### pkgconf:
* libtool wrapper script failure
* stdinc.h: change line 55 to:
```
# define SIZE_FMT_SPECIFIER     "%lu"
```
  ... otherwise the binary will crash. IRIX seriously does not like %zu.

##### scmcvs:
pkgconf managed to find an incorrect libz to link against, congrats pkgconf

##### openssh:
```
$ nm `which ssh`|grep ntop
         U __b64_ntop
         U inet_ntop
```
* these are in /usr/lib32/libc.so, but ...something
* just remove from config.h and recompile

##### flex:
* add `-lgen` to LIBS in Makefile and src/Makefile

##### util-linux:
* this is a mess. no surprise, given the name!

include/c.h:
```
/*
 * MAXHOSTNAMELEN replacement
 */
static inline size_t get_hostname_max(void)
{
#ifdef _SC_HOST_NAME_MAX
        long len = sysconf(_SC_HOST_NAME_MAX);

        if (0 < len)
                return len;
#eldef MAXHOSTNAMELEN
        return MAXHOSTNAMELEN;
#elif HOST_NAME_MAX
        return HOST_NAME_MAX;
#endif
        return 64;
```

lib/env.c line 93:

```
#if defined(HAVE_PRCTL) && !defined(__sgi)
```

config.h:

just disable HAVE_PRCTL entirely

lib/pager.c:

remove err.h

...still not there...

##### bsdtar: 
* add `<sys/resource.h>` to archive_random.c

##### gettext-tools:
- add `<float.h>` to trionan.c
- add to plural-eval.h:

```
#include <stdint.h>
typedef __uint64_t sigjmp_buf[_SIGJBLEN];
```

##### python27:
pkgsrc Makefile:
change:
```
.elif ${OPSYS} == "IRIX"
PY_PLATNAME=    ${LOWER_OPSYS}${OS_VERSION:C/\..*//}
```

PLIST:
add `spwd.so` and `libpython.a`

Makefile (after running configure):

change LD definitions to:
```
LDSHARED=       gcc  -shared  $(LDFLAGS)
BLDSHARED=      gcc  -shared  $(LDFLAGS)
LDCXXSHARED=    g++  -shared
```

Include/Python.h:
move `#include "pyport.h"` up so that it is included before `<unistd.h>`

Modules/socketmodule.c:
`#define NI_NUMERICHOST  0x2`

Modules/mathmodule.c:
change line 58 to 
`#if (defined(_OSF_SOURCE) || defined(__sgi))`

WIP - there must be a clean and sane way of getting everything defined. I just haven't figured it out yet.

##### vim-share:
WIP

* make pathdef.sh use bash

##### mandoc:
compat_strtonum.c:

define __c99 for the duration of including limits.h

##### libatomic_ops:
atomic_ops.c:

`#include <sys/resource.h>`

##### libidn:
WIP

gl/getopt-ext.h:
comment out the offending things on lines 50 and 66

gl/getopt1.c:
comment out the function starting at line 28

##### ruby25:
Truly a WIP.

addr2line.c:
```
#ifdef __sgi
#include <rld_interface.h>
#include <dlfcn.h>
#define _RLD_DLADDR             14
int dladdr(void *address, Dl_info *dl);

int dladdr(void *address, Dl_info *dl)
{
        void *v;
        v = _rld_new_interface(_RLD_DLADDR,address,dl);
        return (int)v;
}
                        
#endif
```
cont.c:
```
#if defined(MAP_STACK) && !defined(__FreeBSD__) && !defined(__FreeBSD_kernel__)
#define FIBER_STACK_FLAGS (MAP_PRIVATE | MAP_ANON | MAP_STACK)
#elif defined(__sgi)
#define FIBER_STACK_FLAGS (MAP_PRIVATE)
#else
#define FIBER_STACK_FLAGS (MAP_PRIVATE | MAP_ANON)
#endif
```
io.c:
line 1649:
```
#ifdef __sgi
#define IOV_MAX sysconf(_SC_IOV_MAX)
#endif
```

Alter Makefile so that when linking miniruby
`-Wl,--unresolved-symbols=ignore-all` is used, as _rld_new_interface is
provided by IRIX runtime linker and thus not visible in linking
phase. The executable will nonetheless work.

`-lpthread` may end up twice into Makefile

`-L/usr/lib32` needs to be added in many places - binutils should
search there by default?

##### libxml2

xpath.c:
```
#ifndef NAN
#define NAN (0.0 / 0.0)
#endif

#ifndef INFINITY
#define INFINITY HUGE_VAL
#endif

#define INFINITY HUGE_VAL
double xmlXPathNAN;
double xmlXPathPINF;
double xmlXPathNINF;

/**
 * xmlXPathInit:
 *
 * Initialize the XPath environment
 *
 * Does nothing but must be kept as public function.
 */
 
void
xmlXPathInit(void) {
        xmlXPathNAN = NAN;
        xmlXPathPINF = INFINITY;
        xmlXPathNINF = -INFINITY;
}
```

##### gmp
do something to ABI in pkgsrc Makefile

##### unzip
add IRIX to the if clause for `CPPFLAGS+=      -DNO_LCHMOD`

##### libxcb
undefined HAVE_SENDMSG in config.h as IRIX version doesn't support SCM_RIGHTS

##### libX11
xcb_io.c:
#include <sys/resource.h>

##### libSM:
sm_genid.c:
uuid.h is sys/uuid.h on IRIX

##### libexecinfo:
```
#if defined(__sun) || defined(__sgi)
#include <alloca.h>
#endif

#ifdef __sgi
#include <rld_interface.h>
#include <dlfcn.h>
#define _RLD_DLADDR             14
int dladdr(void *address, Dl_info *dl);

int dladdr(void *address, Dl_info *dl)
{
        void *v;
        v = _rld_new_interface(_RLD_DLADDR,address,dl);
        return (int)v;
}
                        
#endif
```

##### libuv:
src/unix/core.c:
```
#if defined(__sun) || defined(__sgi)
```

##### curl:
pkgsrc Makefile:
```
LIBS+= -lpthread
```

##### cmake:
export CXX=g++
export MAKE=gmake
