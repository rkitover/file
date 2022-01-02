dnl Process this file with autoconf to produce a configure script.
AC_INIT([file],[5.41],[christos@astron.com])
AM_INIT_AUTOMAKE([subdir-objects foreign])
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])

AC_CONFIG_HEADERS([config.h])
AC_CONFIG_MACRO_DIR([m4])

AC_MSG_CHECKING(for builtin ELF support)
AC_ARG_ENABLE(elf,
[  --disable-elf            disable builtin ELF support],
[if test "${enableval}" = yes; then
  AC_MSG_RESULT(yes)
  AC_DEFINE([BUILTIN_ELF], 1, [Define if built-in ELF support is used])
else
  AC_MSG_RESULT(no)
fi], [
  dnl enable by default
  AC_MSG_RESULT(yes)
  AC_DEFINE([BUILTIN_ELF], 1, [Define in built-in ELF support is used])
])

AC_MSG_CHECKING(for ELF core file support)
AC_ARG_ENABLE(elf-core,
[  --disable-elf-core       disable ELF core file support],
[if test "${enableval}" = yes; then
  AC_MSG_RESULT(yes)
  AC_DEFINE([ELFCORE], 1, [Define for ELF core file support])
else
  AC_MSG_RESULT(no)
fi], [
  dnl enable by default
  AC_MSG_RESULT(yes)
  AC_DEFINE([ELFCORE], 1, [Define for ELF core file support])
])

AC_MSG_CHECKING(for zlib support)
AC_ARG_ENABLE([zlib],
[AS_HELP_STRING([--disable-zlib], [disable zlib compression support @<:@default=auto@:>@])])
AC_MSG_RESULT($enable_zlib)

AC_MSG_CHECKING(for bzlib support)
AC_ARG_ENABLE([bzlib],
[AS_HELP_STRING([--disable-bzlib], [disable bz2lib compression support @<:@default=auto@:>@])])
AC_MSG_RESULT($enable_bzlib)

AC_MSG_CHECKING(for xzlib support)
AC_ARG_ENABLE([xzlib],
[AS_HELP_STRING([--disable-xzlib], [disable liblzma/xz compression support @<:@default=auto@:>@])])
AC_MSG_RESULT($enable_xzlib)

AC_MSG_CHECKING(for libseccomp support)
AC_ARG_ENABLE([libseccomp],
[AS_HELP_STRING([--disable-libseccomp], [disable libseccomp sandboxing @<:@default=auto@:>@])])
AC_MSG_RESULT($enable_libseccomp)

AC_MSG_CHECKING(for pcre2 support)
AC_ARG_ENABLE([pcre2],
[AS_HELP_STRING([--disable-pcre2], [disable pcre2 regex @<:@default=auto@:>@])])
AC_MSG_RESULT($enable_pcre2)

AC_MSG_CHECKING(for pcre support)
AC_ARG_ENABLE([pcre],
[AS_HELP_STRING([--disable-pcre], [disable pcre regex @<:@default=auto@:>@])])
AC_MSG_RESULT($enable_pcre)

dnl pcre2 and pcre are mutually exclusive.
if test "$enable_pcre2$enable_pcre" = "yesyes"; then
  AC_MSG_ERROR([you can require pcre2 or pcre but NOT both])
fi

AC_MSG_CHECKING(for file formats in man section 5)
AC_ARG_ENABLE(fsect-man5,
[  --enable-fsect-man5      enable file formats in man section 5],
[if test "${enableval}" = yes; then
  AC_MSG_RESULT(yes)
  fsect=5
else
  AC_MSG_RESULT(no)
  fsect=4
fi], [
  dnl disable by default
  AC_MSG_RESULT(no)
  fsect=4
])

AC_SUBST([pkgdatadir], ['$(datadir)/misc'])
AC_SUBST(fsect)
AM_CONDITIONAL(FSECT5, test x$fsect = x5)

AC_SUBST(WARNINGS)

dnl Checks for programs.
AC_PROG_CC
AC_USE_SYSTEM_EXTENSIONS
AM_PROG_CC_C_O
AC_C_BIGENDIAN
AC_PROG_INSTALL
AC_PROG_LN_S
LT_INIT([disable-static pic-only win32-dll])
gl_VISIBILITY

dnl Check for MSVC and MINGW

AC_CANONICAL_HOST

case "$CCDEPMODE" in
  *msvc*)
    MSVC=1
    ;;
  *)
    MSVC=0
    ;;
esac

if test "$MSVC" != "1"; then
  case "$host_os" in
     mingw*)
        MINGW=1
        ;;
     *)
        MINGW=0
        ;;
  esac
else
  MINGW=0
fi

WIN32=0

if test "$MSVC" = 1 || test "$MINGW" = 1; then
  WIN32=1
  AC_DEFINE([WIN32], 1, [Define for native Win32 build])
  AC_SUBST(WIN32)
  AM_CONDITIONAL(WIN32, test "$WIN32" = 1)
fi

dnl Checks for headers
AC_HEADER_MAJOR
AC_HEADER_SYS_WAIT
AC_CHECK_HEADERS(stdint.h fcntl.h inttypes.h unistd.h byteswap.h)
AC_CHECK_HEADERS(spawn.h utime.h wchar.h wctype.h)
AC_CHECK_HEADERS(getopt.h err.h xlocale.h)
AC_CHECK_HEADERS(sys/bswap.h sys/mman.h sys/stat.h sys/types.h sys/utime.h sys/time.h sys/sysmacros.h sys/ioctl.h)
if test "$enable_pcre2" != "no"; then
  AC_CHECK_HEADERS(pcre2posix.h)
fi
if test "$ac_cv_header_pcre2posix_h" != "yes" && \
   test "$enable_pcre" != "no"; then
  AC_CHECK_HEADERS(pcreposix.h)
fi
if test "$ac_cv_header_pcre2posix_h" != "yes" && \
   test "$ac_cv_header_pcreposix_h" != "yes"; then
  AC_CHECK_HEADERS(regex.h,,[
    AC_MSG_ERROR([no include header for regex support found])
  ])
fi
if test "$enable_zlib" != "no"; then
  AC_CHECK_HEADERS(zlib.h)
fi
if test "$enable_bzlib" != "no"; then
  AC_CHECK_HEADERS(bzlib.h)
fi
if test "$enable_xzlib" != "no"; then
  AC_CHECK_HEADERS(lzma.h)
fi
AC_CHECK_TYPE([sig_t],[AC_DEFINE([HAVE_SIG_T],1,[Have sig_t type])],,[#include <signal.h>])

dnl Checks for typedefs, structures, and compiler characteristics.
AC_TYPE_OFF_T
AC_TYPE_SIZE_T
AC_CHECK_MEMBERS([struct stat.st_rdev])

AC_CHECK_MEMBERS([struct tm.tm_gmtoff],,,[#include <time.h>])
AC_STRUCT_TIMEZONE
AC_STRUCT_TIMEZONE_DAYLIGHT
AC_SYS_LARGEFILE
AC_FUNC_FSEEKO
AC_TYPE_MBSTATE_T

AC_STRUCT_OPTION_GETOPT_H
AC_TYPE_PID_T
AC_TYPE_UINT8_T
AC_TYPE_UINT16_T
AC_TYPE_UINT32_T
AC_TYPE_INT32_T
AC_TYPE_UINT64_T
AC_TYPE_INT64_T
AC_TYPE_INTPTR_T
AC_TYPE_UINTPTR_T
AC_FUNC_MMAP
AC_FUNC_FORK
AC_FUNC_MBRTOWC

AC_MSG_CHECKING(for gcc compiler warnings)
AC_ARG_ENABLE(warnings,
[  --disable-warnings	disable compiler warnings],
[if test "${enableval}" = no -o "$GCC" = no; then
   AC_MSG_RESULT(no)
   WARNINGS=
else
   AC_MSG_RESULT(yes)
   WARNINGS="-Wall -Wstrict-prototypes -Wmissing-prototypes -Wpointer-arith \
       -Wmissing-declarations -Wredundant-decls -Wnested-externs \
       -Wsign-compare -Wreturn-type -Wswitch -Wshadow \
       -Wcast-qual -Wwrite-strings -Wextra -Wunused-parameter -Wformat=2"
fi], [
if test "$GCC" = yes; then
   AC_MSG_RESULT(yes)
   WARNINGS="-Wall -Wstrict-prototypes -Wmissing-prototypes -Wpointer-arith \
       -Wmissing-declarations -Wredundant-decls -Wnested-externs \
       -Wsign-compare -Wreturn-type -Wswitch -Wshadow \
       -Wcast-qual -Wwrite-strings -Wextra -Wunused-parameter -Wformat=2"
else
   WARNINGS=
   AC_MSG_RESULT(no)
fi])

dnl Checks for functions
AC_CHECK_FUNCS(strndup mkstemp mkostemp utimes utime wcwidth strtof newlocale uselocale freelocale memmem pipe2 posix_spawnp)

dnl Provide implementation of some required functions if necessary
AC_REPLACE_FUNCS(getopt_long asprintf vasprintf strlcpy strlcat getline ctime_r asctime_r localtime_r gmtime_r pread strcasestr fmtcheck dprintf)

dnl Checks for libraries
if test "$enable_zlib" != "no"; then
  AC_SEARCH_LIBS(gzopen, [z zlib])
fi
if test "$enable_bzlib" != "no"; then
  AC_CHECK_LIB(bz2, BZ2_bzCompressInit)
fi
if test "$enable_xzlib" != "no"; then
  AC_CHECK_LIB(lzma, lzma_stream_decoder)
fi
if test "$enable_libseccomp" != "no"; then
    AC_CHECK_LIB(seccomp, seccomp_init)
fi
if test "$enable_pcre2" != "no"; then
  dnl The -static suffix is generally only used on Windows, where linking this library statically is preferrable.
  AC_SEARCH_LIBS(pcre2_regcomp, [pcre2-posix-static],,,[-lpcre2-8-static])
  if test "$ac_cv_search_pcre2_regcomp" = "no"; then
    unset ac_cv_search_pcre2_regcomp
    AC_SEARCH_LIBS(pcre2_regcomp, [pcre2-posix])
  fi
  dnl In case it's static...
  if test "$ac_cv_search_pcre2_regcomp" = "no"; then
    unset ac_cv_search_pcre2_regcomp
    AC_SEARCH_LIBS(pcre2_regcomp, [pcre2-posix],,,[-lpcre2-8])
  fi
fi
if test "$ac_cv_search_pcre2_regcomp" = "no" && \
   test "enable_pcre" != "no"; then
  AC_SEARCH_LIBS(pcreposix_regcomp, [pcreposix-static pcreposix])
fi
if ( \
     test "$enable_pcre2" = "no" || \
     test "$ac_cv_search_pcre2_regcomp" = "no" \
   ) && ( \
     test "$enable_pcre" = "no" || \
     test "$ac_cv_search_pcreposix_regcomp" = "no" \
   ); then
  AC_SEARCH_LIBS(regcomp,[gnurx],,[
    AC_MSG_ERROR([no link library for regex support found])
  ])
fi

dnl See if we are cross-compiling
AM_CONDITIONAL(IS_CROSS_COMPILE, test "$cross_compiling" = yes)

dnl Final sanity checks
if test "$enable_pcre2" = "yes"; then
  if test "$ac_cv_header_pcre2posix_h$ac_cv_search_pcre2_regcomp" != "yesyes"; then
    AC_MSG_ERROR([pcre2 support requested but not found])
  fi
fi
if test "$enable_pcre" = "yes"; then
  if test "$ac_cv_header_pcreposix_h$ac_cv_search_pcreposix_regcomp" != "yesyes"; then
    AC_MSG_ERROR([pcre support requested but not found])
  fi
fi
if test "$enable_zlib" = "yes"; then
  if test "$ac_cv_header_zlib_h$ac_cv_lib_z_gzopen" != "yesyes"; then
    AC_MSG_ERROR([zlib support requested but not found])
  fi
fi
if  test "$ac_cv_header_zlib_h$ac_cv_lib_z_gzopen" = "yesyes"; then
  AC_DEFINE([ZLIBSUPPORT], 1, [Enable zlib compression support])
fi
if test "$enable_bzlib" = "yes"; then
  if test "$ac_cv_header_bzlib_h$ac_cv_lib_bz2_BZ2_bzCompressInit" != "yesyes"; then
    AC_MSG_ERROR([bzlib support requested but not found])
  fi
fi
if  test "$ac_cv_header_bzlib_h$ac_cv_lib_bz2_BZ2_bzCompressInit" = "yesyes"; then
  AC_DEFINE([BZLIBSUPPORT], 1, [Enable bzlib compression support])
fi
if test "$enable_xzlib" = "yes"; then
  if test "$ac_cv_header_lzma_h$ac_cv_lib_lzma_lzma_stream_decoder" != "yesyes"; then
    AC_MSG_ERROR([xzlib support requested but not found])
  fi
fi
if  test "$ac_cv_header_lzma_h$ac_cv_lib_lzma_lzma_stream_decoder" = "yesyes"; then
  AC_DEFINE([XZLIBSUPPORT], 1, [Enable xzlib compression support])
fi

AC_CONFIG_FILES([Makefile src/Makefile magic/Makefile tests/Makefile doc/Makefile python/Makefile libmagic.pc])
AC_OUTPUT
