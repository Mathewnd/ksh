AC_PREREQ([2.69])
AC_INIT([ksh93], [1.1.0-alpha], [https://github.com/ksh93/ksh/issues], [ksh], [https://github.com/ksh93/ksh])
AC_CONFIG_SRCDIR([src/cmd/ksh93/sh/main.c])
AC_CONFIG_AUX_DIR([build-aux])
AC_REQUIRE_AUX_FILE([install-sh])
AC_CONFIG_HEADERS([config.h])

AC_CANONICAL_BUILD
AC_CANONICAL_HOST
AC_PROG_CC
AC_PROG_CPP
AC_PROG_INSTALL
AC_PROG_LN_S
AC_CHECK_TOOL([AR], [ar])
AC_CHECK_TOOL([RANLIB], [ranlib], [:])
AC_ARG_VAR([CC_FOR_BUILD], [C compiler for build-machine bootstrap tools])
AC_ARG_VAR([CFLAGS_FOR_BUILD], [C compiler flags for build-machine bootstrap tools])
AS_IF([test "x$CC_FOR_BUILD" = x], [
	AC_CHECK_PROGS([CC_FOR_BUILD], [cc gcc clang], [cc])
])

test -n "$AR" || AC_MSG_ERROR([ar is required])

AC_ARG_VAR([CCFLAGS], [legacy ksh93 compiler flags; appended to CFLAGS])
AS_IF([test "x$CCFLAGS" != x], [CFLAGS="$CFLAGS $CCFLAGS"])

AC_ARG_ENABLE([shared],
	[AS_HELP_STRING([--disable-shared], [do not build dynamically linked ksh artifacts])],
	[], [enable_shared=yes])
AC_ARG_ENABLE([static],
	[AS_HELP_STRING([--disable-static], [do not build static ksh artifacts])],
	[], [enable_static=yes])

AC_ARG_ENABLE([audit],
	[AS_HELP_STRING([--disable-audit], [disable SHOPT_AUDIT])],
	[], [enable_audit=yes])
AC_ARG_ENABLE([sysrc],
	[AS_HELP_STRING([--disable-sysrc], [disable loading /etc/ksh.kshrc by default])],
	[], [enable_sysrc=yes])
AC_ARG_ENABLE([multibyte],
	[AS_HELP_STRING([--enable-multibyte], [enable multibyte character handling])],
	[], [enable_multibyte=no])
AC_ARG_ENABLE([debug],
	[AS_HELP_STRING([--enable-debug], [compile with DEBUG and debug symbols])],
	[], [enable_debug=no])

AC_ARG_WITH([audit-file],
	[AS_HELP_STRING([--with-audit-file=PATH], [path compiled into SHOPT_AUDITFILE])],
	[with_audit_file=$withval], [with_audit_file=/etc/ksh_audit])

AS_CASE([$enable_shared], [yes|no], [], [AC_MSG_ERROR([--enable-shared expects yes or no])])
AS_CASE([$enable_static], [yes|no], [], [AC_MSG_ERROR([--enable-static expects yes or no])])
AS_IF([test "$enable_static" = no && test "$enable_shared" = no],
	[AC_MSG_ERROR([at least one of --enable-static or --enable-shared must be enabled])])

AC_CHECK_HEADERS([dlfcn.h errno.h fcntl.h inttypes.h limits.h locale.h stdarg.h stddef.h stdint.h stdlib.h string.h sys/stat.h sys/types.h time.h unistd.h wchar.h wctype.h])
AC_CHECK_FUNCS([dup2 fork getcwd gethostname getrlimit iswctype lstat memmove memset mkstemp setlocale sigaction strchr strerror strtol strtoll])
AC_CHECK_LIB([m], [sin], [MATH_LIBS=-lm], [MATH_LIBS=])
AC_CHECK_LIB([dl], [dlopen], [DL_LIBS=-ldl], [DL_LIBS=])

AS_IF([test "$cross_compiling" = yes], [KSH_AUTOCONF_CROSS=1], [KSH_AUTOCONF_CROSS=0])

AS_IF([test "x$host" != "x$build" && test "$cross_compiling" != yes], [
	AC_MSG_ERROR([--host=$host was requested, but the selected compiler produces build-machine binaries; set a real cross CC])
])

AS_IF([test "$cross_compiling" = yes], [
	AC_CACHE_CHECK([whether ksh93 cross feature cache was acknowledged],
		[ksh_cv_cross_feature_cache],
		[AC_MSG_ERROR([cross-compiling requires ksh_cv_cross_feature_cache=yes in CONFIG_SITE or config.cache to acknowledge target feature answers are externally supplied])])
])

AM_CONDITIONAL_FALSE='false'
AC_SUBST([AM_CONDITIONAL_FALSE])
AC_SUBST([AR])
AC_SUBST([RANLIB])
AC_SUBST([CC_FOR_BUILD])
AC_SUBST([CFLAGS_FOR_BUILD])
AC_SUBST([MATH_LIBS])
AC_SUBST([DL_LIBS])
AC_SUBST([KSH_AUTOCONF_CROSS])

AS_IF([test "$enable_shared" = yes], [KSH_ENABLE_SHARED=1], [KSH_ENABLE_SHARED=0])
AS_IF([test "$enable_static" = yes], [KSH_ENABLE_STATIC=1], [KSH_ENABLE_STATIC=0])
AS_IF([test "$enable_audit" = yes], [KSH_SHOPT_AUDIT=1], [KSH_SHOPT_AUDIT=0])
AS_IF([test "$enable_sysrc" = yes], [KSH_SHOPT_SYSRC=1], [KSH_SHOPT_SYSRC=0])
AS_IF([test "$enable_multibyte" = yes], [KSH_SHOPT_MULTIBYTE=1], [KSH_SHOPT_MULTIBYTE=0])
AS_IF([test "$enable_debug" = yes], [KSH_ENABLE_DEBUG=1], [KSH_ENABLE_DEBUG=0])

AC_SUBST([KSH_ENABLE_SHARED])
AC_SUBST([KSH_ENABLE_STATIC])
AC_SUBST([KSH_SHOPT_AUDIT])
AC_SUBST([KSH_SHOPT_SYSRC])
AC_SUBST([KSH_SHOPT_MULTIBYTE])
AC_SUBST([KSH_ENABLE_DEBUG])
AC_SUBST([KSH_AUDIT_FILE], [$with_audit_file])

AC_CONFIG_FILES([Makefile build-aux/autoconf-build])
AC_CONFIG_COMMANDS([build-driver-mode], [chmod +x build-aux/autoconf-build])
AC_OUTPUT
