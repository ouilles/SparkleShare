dnl Process this file with autoconf to produce a configure script.
m4_define([sparkleshare_version], [1.3])

AC_PREREQ([2.54])
AC_INIT([SparkleShare], sparkleshare_version)
AM_INIT_AUTOMAKE([1.11 dist-bzip2 dist-zip foreign tar-pax])
AM_MAINTAINER_MODE

dnl Export Version Info
AC_SUBST([ASM_VERSION], [sparkleshare_version])

dnl pkg-config
AC_PATH_PROG(PKG_CONFIG, pkg-config, no)
if test "x$PKG_CONFIG" = "xno"; then
        AC_MSG_ERROR([You need to install pkg-config])
fi

AC_SUBST([ACLOCAL_AMFLAGS], ["-I build/m4/sparkleshare -I build/m4/shamrock -I build/m4/shave \${ACLOCAL_FLAGS}"])

SHAMROCK_EXPAND_LIBDIR
SHAMROCK_EXPAND_BINDIR
SHAMROCK_EXPAND_DATADIR

AC_PROG_INSTALL

dnl Mono and gmcs
SHAMROCK_CHECK_MONO_MODULE(2.8)
SHAMROCK_FIND_MONO_RUNTIME
    
PKG_CHECK_EXISTS([mono >= 2.8],
[SHAMROCK_FIND_MONO_4_0_COMPILER
SHAMROCK_CHECK_MONO_4_0_GAC_ASSEMBLIES([
    System
])]
)

AC_ARG_ENABLE(debug,
	AC_HELP_STRING([--enable-debug],
		[Use 'DEBUG' Configuration [default=YES]]),
		enable_debug=yes, enable_debug=no)
AM_CONDITIONAL(ENABLE_DEBUG, test x$enable_debug = xyes)
if test "x$enable_debug" = "xyes" ; then
	CONFIG_REQUESTED="yes"
fi
AC_ARG_ENABLE(release,
	AC_HELP_STRING([--enable-release],
		[Use 'RELEASE' Configuration [default=NO]]),
		enable_release=yes, enable_release=no)
AM_CONDITIONAL(ENABLE_RELEASE, test x$enable_release = xyes)
if test "x$enable_release" = "xyes" ; then
	CONFIG_REQUESTED="yes"
fi
if test -z "$CONFIG_REQUESTED" ; then
	AM_CONDITIONAL(ENABLE_DEBUG, true)
	enable_debug=yes
fi

AC_ARG_ENABLE(appindicator,
	AS_HELP_STRING([--enable-appindicator[=@<:@no/auto/yes@:>@]],[Build support for application indicators ]),
	[enable_appindicator=$enableval],
	[enable_appindicator="auto"])

OSNAME=`uname -s`
if test "$OSNAME" = "Linux" ; then
	SPARKLESHARE_CHECK_GTK_SHARP

	PKG_CHECK_MODULES(NOTIFY_SHARP, notify-sharp-3.0)

	dnl check for webkitgtk-sharp
	PKG_CHECK_MODULES(WEBKITGTK_SHARP, webkitgtk-sharp-3.0, have_webkitgtk_sharp=yes, have_webkitgtk_sharp=no)
	if test "x$have_webkitgtk_sharp" = "xno" ; then
		AC_ERROR("webkitgtk-sharp is a required dependency: you need to install the appropriate devel package before you can compile")
	fi
	AC_SUBST(WEBKITGTK_SHARP_LIBS)

	APPINDICATOR_REQUIRED=0.1

	if test x$enable_appindicator = xauto ; then
			PKG_CHECK_EXISTS([appindicator3-sharp-0.1 >= $APPINDICATOR_REQUIRED],
							 enable_appindicator="yes",
							 enable_appindicator="no")
	fi

	if test x$enable_appindicator = xyes ; then
			PKG_CHECK_EXISTS([appindicator3-sharp-0.1 >= $APPINDICATOR_REQUIRED],,
							 AC_MSG_ERROR([appindicator3-sharp-0.1 is not installed]))
			PKG_CHECK_MODULES(APP_INDICATOR,
							appindicator3-sharp-0.1 >= $APPINDICATOR_REQUIRED)
			AC_SUBST(APP_INDICATOR_CFLAGS)
			AC_SUBST(APP_INDICATOR_LIBS)
			AC_DEFINE(HAVE_APP_INDICATOR, 1, [Have AppIndicator])
	fi

	GUISUBDIRS=SparkleShare
else
	GUISUBDIRS=
fi

AM_CONDITIONAL(HAVE_APP_INDICATOR, test x"$enable_appindicator" = xyes)
AC_SUBST([GUISUBDIRS])

SHAMROCK_CHECK_NUNIT

SHAVE_INIT([build/m4/shave], [enable])

AC_OUTPUT([
build/Makefile
build/m4/Makefile
build/m4/shave/shave
build/m4/shave/shave-libtool
SparkleLib/Defines.cs
SparkleLib/Makefile
SparkleLib/Git/Makefile
SparkleShare/Common/Makefile
SparkleShare/Common/HTML/Makefile
SparkleShare/Common/Plugins/Makefile
SparkleShare/Common/Pixmaps/Makefile
SparkleShare/Linux/Makefile
SparkleShare/Linux/Pixmaps/Makefile
SparkleShare/Linux/Pixmaps/icons/Makefile
SparkleShare/Linux/Pixmaps/icons/gnome/Makefile
SparkleShare/Linux/Pixmaps/icons/ubuntu-mono-dark/Makefile
SparkleShare/Linux/Pixmaps/icons/ubuntu-mono-light/Makefile
SparkleShare/Linux/sparkleshare
SparkleShare/Makefile
Makefile
])

echo "
SparkleShare ${VERSION}

Configuration:
       Prefix : ${prefix}

"

