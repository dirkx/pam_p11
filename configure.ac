
AC_PREREQ(2.60)

define([PACKAGE_VERSION_MAJOR], [0])
define([PACKAGE_VERSION_MINOR], [1])
define([PACKAGE_VERSION_FIX], [6])
define([PACKAGE_SUFFIX], [_git])

AC_INIT([pam_p11],[PACKAGE_VERSION_MAJOR.PACKAGE_VERSION_MINOR.PACKAGE_VERSION_FIX[]PACKAGE_SUFFIX])
AC_CONFIG_AUX_DIR([.])
AM_CONFIG_HEADER([config.h])
AC_CONFIG_MACRO_DIR([m4])
AM_INIT_AUTOMAKE

AC_CONFIG_SRCDIR([src/pam_p11.c])

AC_CANONICAL_HOST
AC_PROG_CC
PKG_PROG_PKG_CONFIG
AC_C_BIGENDIAN

AC_ARG_ENABLE(
	[strict],
	[AS_HELP_STRING([--enable-strict],[enable strict compile mode @<:@disabled@:>@])],
	,
	[enable_strict="no"]
)

AC_ARG_ENABLE(
	[pedantic],
	[AS_HELP_STRING([--enable-pedantic],[enable pedantic compile mode @<:@disabled@:>@])],
	,
	[enable_pedantic="no"]
)

AC_ARG_WITH(
	[pamdir],
	[AS_HELP_STRING([--with-pamdir=PATH],[Specify the directory where PAM modules are stored])],
	[pamdir="${withval}"],
	[
		if test "${prefix}" = "/usr"; then
			pamdir="/lib${libdir##*/lib}/security"
		else
			pamdir="\$(libdir)/security"
		fi
	]
)

dnl Checks for programs.
AC_PROG_CPP
AC_PROG_INSTALL
AC_PROG_LN_S
AC_PROG_MKDIR_P
AC_PROG_SED
AC_PROG_MAKE_SET

dnl Add libtool support.
ifdef(
	[LT_INIT],
	[LT_INIT],
	[AC_PROG_LIBTOOL]
)

dnl Checks for header files.
AC_HEADER_STDC
AC_HEADER_SYS_WAIT
AC_CHECK_HEADERS([ \
	string.h syslog.h fcntl.h unistd.h security/pam_ext.h \
])
AC_TYPE_SIZE_T
AC_FUNC_MALLOC
AC_FUNC_REALLOC
AC_FUNC_STAT
AC_FUNC_VPRINTF
AC_CHECK_FUNCS([memset strdup strerror])

PKG_CHECK_MODULES([LIBP11], [libp11 >= 0.2.4],, [AC_MSG_ERROR([libp11 is required])])
PKG_CHECK_MODULES(
	[OPENSSL],
	[libcrypto >= 0.9.7],
	,
	[PKG_CHECK_MODULES(
		[OPENSSL],
		[openssl >= 0.9.7],
		,
		[AC_CHECK_LIB(
			[crypto],
			[RSA_version],
			[OPENSSL_LIBS="-lcrypto"],
			[AC_MSG_ERROR([Cannot find OpenSSL])]
		)]
	)]
)

if test -z "${PAM_LIBS}"; then
	AC_ARG_VAR([PAM_CFLAGS], [C compiler flags for pam])
	AC_ARG_VAR([PAM_LIBS], [linker flags for pam])
	AC_CHECK_LIB(
		[pam],
		[pam_authenticate],
		[PAM_LIBS="-lpam"],
		[AC_MSG_ERROR([Cannot find pam])]
	)
fi

AC_SUBST([pamdir])

if test "${enable_pedantic}" = "yes"; then
	enable_strict="yes";
	CFLAGS="${CFLAGS} -pedantic"
fi
if test "${enable_strict}" = "yes"; then
	CFLAGS="${CFLAGS} -Wall -Wextra"
fi

AC_CONFIG_FILES([
	Makefile
	src/Makefile
])
AC_OUTPUT

cat <<EOF

pam_p11 has been configured with the following options:


Version:                 ${PACKAGE_VERSION}
Libraries:               $(eval eval eval echo "${libdir}")

Host:                    ${host}
Compiler:                ${CC}
Preprocessor flags:      ${CPPFLAGS}
Compiler flags:          ${CFLAGS}
Linker flags:            ${LDFLAGS}
Libraries:               ${LIBS}

pamdir                   ${pamdir}

PAM_CFLAGS:              ${PAM_CFLAGS}
PAM_LIBS:                ${PAM_LIBS}
LIBP11_CFLAGS:           ${LIBP11_CFLAGS}
LIBP11_LIBS:             ${LIBP11_LIBS}
OPENSSL_CFLAGS:          ${OPENSSL_CFLAGS}
OPENSSL_LIBS:            ${OPENSSL_LIBS}

EOF
