# pkgsrc build notes

### DISCLAIMER:

**THIS DOCUMENT IS A WORK IN PROGRESS!**

**ANOTHER DISCLAIMER:**

We know much of this is hacky. If you have time and/or resources to do
stuff in a more nice way, please do share the results!

### GENERAL NOTES:

- quick way of searching for packages in pkgsrc: `cd ${wherever_you_put_pkgsrc}; ls -d */packagename`
- looks like _POSIX90 and _NO_ANSIMODE are always defined, at least with --std=c99

### SUGGESTED FIRST PACKAGES TO INSTALL:

...

### OUTSTANDING ISSUES:

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

### PER PACKAGE NOTES:

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
work/Python-2.7.15/Include/pyport.h:

* line 332, comment it out - sys/time.h and unistd.h can conflict
  because sys/time.h redefines select() under certain conditions

Modules/stringmodule.c:

```
#define _ABIAPI 1 just before including sys/time.h
```

##### vim-share:
* make pathdef.sh use bash

WIP

##### mandoc:
compat_strtonum.c:

define __c99 for the duration of including limits.h

##### libatomic_ops:
atomic_ops.c:

`#include <sys/resource.h>`


