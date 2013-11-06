AC_INIT([gexiv2], [0.7], [gexiv2-request@lists.yorba.org.], [gexiv2], [http://redmine.yorba.org/projects/gexiv2/])
AC_PREREQ(2.54)

AC_CONFIG_MACRO_DIR([m4])

# Will install auxiliary build tools (depcomp, install-sh, missing...) under build-aux/
AC_CONFIG_AUX_DIR([build-aux])
AM_INIT_AUTOMAKE([1.11 -Wall no-define foreign dist-xz tar-ustar])
LT_PREREQ([2.2])
LT_INIT([disable-static win32-dll])

# This small tricks allows to provide different default compilation flags
# than autoconf, but still let the user overrides our defaults.
CXXFLAGS_COPY="$CXXFLAGS"
AC_PROG_CXX
CXXFLAGS="$CXXFLAGS_COPY"

PKG_PROG_PKG_CONFIG(0.26)

m4_define([gexiv2_major_version], [0])
m4_define([gexiv2_minor_version], [7])
m4_define([gexiv2_micro_version], [0])
m4_define([gexiv2_real_version],
          [gexiv2_major_version.gexiv2_minor_version.gexiv2_micro_version+trunk])
m4_define([gexiv2_version], [gexiv2_real_version])

m4_define([glib_required_version], [2.26.1])
m4_define([exiv2_required_version], [0.21])

# This number must be edited whenever a change has been made that may affect libgexiv2's
# external interface.  Please see http://sourceware.org/autobook/autobook/autobook_91.html
# for the version numbering convention that should be used for this and
# http://davidz25.blogspot.se/2011/07/writing-c-library-part-5.html#abi-api-versioning for
# guidelines about when this should change.
GEXIV2_VERSION_INFO=2:0:0
AC_SUBST(GEXIV2_VERSION_INFO)

# This was necessary because the typelib files don't seem to load if the
# version info has more than one period in it.
GIR_VERSION=0.4
AC_SUBST(GIR_VERSION)

GEXIV2_MAJOR_VERSION=gexiv2_major_version
GEXIV2_MINOR_VERSION=gexiv2_minor_version
GEXIV2_MICRO_VERSION=gexiv2_micro_version
GEXIV2_VERSION=gexiv2_version
AC_SUBST(GEXIV2_MAJOR_VERSION)
AC_SUBST(GEXIV2_MINOR_VERSION)
AC_SUBST(GEXIV2_MICRO_VERSION)
AC_SUBST(GEXIV2_VERSION)

###################
# Check for glib2 #
###################

GLIB_PKGS="gobject-2.0"
GLIB_REQUIRED_VERSION=glib_required_version
AC_SUBST(GLIB_REQUIRED_VERSION)

PKG_CHECK_MODULES(GLIB,
                  glib-2.0 >= $GLIB_REQUIRED_VERSION $GLIB_PKGS,
                  ,
                  AC_MSG_ERROR([GLib is required. Please install it.]))

###################
# Check for exiv2 #
###################

EXIV2_REQUIRED_VERSION=exiv2_required_version
AC_SUBST(EXIV2_REQUIRED_VERSION)

PKG_CHECK_MODULES(EXIV2,
                  exiv2 >= $EXIV2_REQUIRED_VERSION,
                  ,
                  AC_MSG_ERROR([exiv2 is required. Please install it.]))

##############
# Debug Mode #
##############

AC_MSG_CHECKING([debugging mode])
AC_ARG_ENABLE(debug,
              [  --enable-debug          enable debug mode [[default=no]]],
              if eval "test x$enable_debug = xyes"; then
              DEBUG_CFLAGS="-O0"
              fi)

if test -n "$DEBUG_CFLAGS"; then
    AC_MSG_RESULT([yes])
    if test -z "$CXXFLAGS"; then
        CXXFLAGS="-O0 -g -pipe -nostdlib -shared"
    else
        CXXFLAGS="$DEBUG_CFLAGS $CXXFLAGS"
    fi
else
    AC_MSG_RESULT([no])
    if test -z "$CXXFLAGS"; then
        CXXFLAGS="-O2 -g -pipe -nostdlib -shared"
    fi
fi

#################
# Introspection #
#################

enable_introspection=no
AC_MSG_CHECKING([enable instrospection])
AC_ARG_ENABLE(introspection,
              [  --enable-introspection  enable instrospection [[default=no]]],
              if eval "test x$enable_introspection = xyes"; then
              enable_introspection="yes"
              fi)

AM_CONDITIONAL(ENABLE_INTROSPECTION, test "x$enable_introspection" = "xyes")
AC_MSG_RESULT([$enable_introspection])

enable_python2="no (instrospection disabled)"
enable_python3="no (instrospection disabled)"
if test "x$enable_introspection" = "xyes"; then
    # Used to determine where Python wants the .gi file to be installed ...
    # if Python is not installed, PYTHON2 and PYTHON3 will be empty
    PYTHON="import gi; print(gi._overridesdir)"
    PYTHON2_GIDIR=$(python2 -c $PYTHON 2> /dev/null)
    PYTHON3_GIDIR=$(python3 -c $PYTHON 2> /dev/null)
    if test "x$PYTHON2" != "x"; then
        enable_python2=yes
    else
        enable_python2=no
    fi
    if test "x$PYTHON3" != "x"; then
        enable_python3=yes
    else
        enable_python3=no
    fi
    AC_SUBST(PYTHON2_GIDIR)
    AC_SUBST(PYTHON3_GIDIR)
fi
AM_CONDITIONAL(HAS_PYTHON2, test "x$PYTHON2_GIDIR" != "x")
AM_CONDITIONAL(HAS_PYTHON3, test "x$PYTHON3_GIDIR" != "x")

#################
# Vala Binding  #
#################

AC_ARG_ENABLE(vala, [  --disable-vala          disable vala binding [[default=no]]],
              if eval "test x$enable_vala = xno"; then
              enable_vala=no
              else
              enable_vala=yes
              fi,
              enable_vala=yes)

if test "x$enable_vala" != "xno"; then
    AC_CHECK_TOOL([VAPIGEN], [vapigen], [:])
    AC_CHECK_TOOL([VALA_GEN_INSTROSPECT], [vala-gen-introspect], [:])
    if test "x$VAPIGEN" = "x:" || test "x$VALA_GEN_INSTROSPECT" = "x:"; then
        enable_vala="no (missing vapigen and/or vala-gen-introspect)"
    fi
fi
AM_CONDITIONAL(ENABLE_VALA, test "x$enable_vala" = "xyes")

AC_MSG_CHECKING([enable vala])
AC_MSG_RESULT([$enable_vala])

###################
# Check Word Size #
###################

AC_CHECK_SIZEOF([int *])
AC_SUBST(SIZEOF_INT_P)
AM_CONDITIONAL(WORD_64, test "x$SIZEOF_INT_P" = "x8" )

###########################
# Check target architecture
###########################

AC_MSG_CHECKING([for target architecture])
case x"$target" in
    xNONE | x)
        target_or_host="$host" ;;
    *)
        target_or_host="$target" ;;
esac
AC_MSG_RESULT([$target_or_host])

case "$target_or_host" in
    i*86-*-*)
        have_x86=yes
        AC_DEFINE(ARCH_X86, 1, [Define to 1 if you are compiling for ix86.])
    ;;
    x86_64-*-*)
        have_x86=yes
        AC_DEFINE(ARCH_X86, 1, [Define to 1 if you are compiling for ix86.])
        AC_DEFINE(ARCH_X86_64, 1, [Define to 1 if you are compiling for amd64.])
    ;;
    ppc-*-* | powerpc-*)
        have_ppc=yes
        AC_DEFINE(ARCH_PPC, 1, [Define to 1 if you are compiling for PowerPC.])
    ;;
    ppc64-*-* | powerpc64-*)
        have_ppc=yes
        AC_DEFINE(ARCH_PPC, 1, [Define to 1 if you are compiling for PowerPC.])
        AC_DEFINE(ARCH_PPC64, 1, [Define to 1 if you are compiling for PowerPC64.])
    ;;
    *)
    ;;
esac

#################
# Check for Win32
#################

AC_MSG_CHECKING([Win32 platform])
case "$target_or_host" in
    *-*-mingw* | *-*-cygwin*)
        platform_win32=yes
    ;;
    *)
        platform_win32=no
    ;;
esac
AC_MSG_RESULT([$platform_win32])
AM_CONDITIONAL(PLATFORM_WIN32, test "$platform_win32" = "yes")

##############
# Generation #
##############

AC_CONFIG_FILES([
Makefile
gexiv2.pc:gexiv2.pc.in
])

AC_OUTPUT

AC_MSG_RESULT([
GExiv2 will be built with prefix $prefix for $host (cross-compiling: $cross_compiling).

Options:
    Vala binding:       $enable_vala
    Introspection:      $enable_introspection
    Python2 binding:    $enable_python2
    Python3 binding:    $enable_python3
])

if test "x$enable_vala" = "xyes"; then
AC_MSG_RESULT([
NOTE: the Vala binding can only be built and installed after the C API.
After installing with "make install", run "make vapi" to build the bindings and "make install-vapi" to install them.
])
fi