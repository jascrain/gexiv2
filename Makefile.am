ACLOCAL_AMFLAGS = -I m4 ${ACLOCAL_FLAGS}

# Installation directories.
pkgconfigdir     = $(libdir)/pkgconfig
GEXIV2_publicdir = $(includedir)/gexiv2
girdir           = $(datadir)/gir-1.0
typelibdir       = `pkg-config gobject-introspection-no-export-1.0 --variable typelibdir`
py2gidir         = $(PYTHON2_GIDIR)
py3gidir         = $(PYTHON3_GIDIR)

# Extra package files. #
EXTRA_DIST =
	@PACKAGE_NAME@.pc.in	\
	GExiv2.py				\
	@PACKAGE_NAME@.vapi		\
	$(TYPELIB_FILES)

# Installed Files #
pkgconfig_DATA  = @PACKAGE_NAME@.pc
lib_LTLIBRARIES = lib@PACKAGE_NAME@.la
if ENABLE_INTROSPECTION
gir_DATA        = GExiv2-$(GIR_VERSION).gir
typelib_DATA    = GExiv2-$(GIR_VERSION).typelib
# No introspection, no .gi file for the Python module to call into
if HAS_PYTHON2
py2gi_DATA      = GExiv2.py
endif
if HAS_PYTHON3
py3gi_DATA      = GExiv2.py
endif
endif

GEXIV2_public_HEADERS = \
	gexiv2/gexiv2.h \
	gexiv2/gexiv2-metadata.h \
	gexiv2/gexiv2-managed-stream.h \
	gexiv2/gexiv2-preview-properties.h \
	gexiv2/gexiv2-preview-image.h \
	gexiv2/gexiv2-log.h \
	gexiv2/gexiv2-startup.h \
	gexiv2/gexiv2-version.h

# Non-installed source files. #
GEXIV2_sources = \
	gexiv2/gexiv2-metadata.cpp \
	gexiv2/gexiv2-metadata-exif.cpp \
	gexiv2/gexiv2-metadata-gps.cpp \
	gexiv2/gexiv2-metadata-iptc.cpp \
	gexiv2/gexiv2-metadata-xmp.cpp \
	gexiv2/gexiv2-stream-io.cpp \
	gexiv2/gexiv2-preview-properties.cpp \
	gexiv2/gexiv2-preview-image.cpp \
	gexiv2/gexiv2-log.cpp \
	gexiv2/gexiv2-startup.cpp
	gexiv2/gexiv2-log-private.h \
	gexiv2/gexiv2-metadata-private.h \
	gexiv2/gexiv2-stream-io.h \
	gexiv2/gexiv2-preview-properties-private.h \
	gexiv2/gexiv2-preview-image-private.h

# compilation setup. #

if PLATFORM_WIN32
no_undefined = -no-undefined
endif

# REQUIRED_CFLAGS absolutely get appended to CFLAGS, whatever the
# the value of CFLAGS in the environment.
REQUIRED_CXXFLAGS=-Wl,-lstdc++

# Because -fPIC can interfere with compilation on 32-bit platforms but
# is absolutely necessary for AMD64, we check what the target machine's
# word size is, and set our required flags based upon that.
if WORD_64
REQUIRED_CXXFLAGS += -fPIC -DPIC
endif

CXXFLAGS += $(REQUIRED_CXXFLAGS)

gexiv2/gexiv2-version.h: gexiv2/gexiv2-version.m4
	m4 '--define=_VERSION_MAJOR_=$(GEXIV2_MAJOR_VERSION)' '--define=_VERSION_MINOR_=$(GEXIV2_MINOR_VERSION)' '--define=_VERSION_MICRO_=$(GEXIV2_MICRO_VERSION)' $< > $@

lib@PACKAGE_NAME@_la_SOURCES  = $(GEXIV2_sources) $(GEXIV2_public_HEADERS)
lib@PACKAGE_NAME@_la_LIBADD   = $(EXIV2_LIBS) $(GLIB_LIBS)
lib@PACKAGE_NAME@_la_CPPFLAGS = $(EXIV2_CFLAGS) $(GLIB_CFLAGS)

lib@PACKAGE_NAME@_la_LDFLAGS  = \
	$(no_undefined) -export-dynamic -version-info $(GEXIV2_VERSION_INFO)

# Vala #

if ENABLE_VALA

install-vapi: vapi
	mkdir -p $(DESTDIR)$(datadir)/vala/vapi
	$(INSTALL_DATA) @PACKAGE_NAME@.vapi "$(DESTDIR)$(datadir)/vala/vapi"

vapi: @PACKAGE_NAME@.vapi

@PACKAGE_NAME@.vapi: vapi/@PACKAGE_NAME@.gi
	vapigen --library=@PACKAGE_NAME@ --metadatadir=vapi vapi/@PACKAGE_NAME@.gi

vapi/@PACKAGE_NAME@.gi:
	@echo NOTE: This version of gexiv2 must be installed to generate a VAPI file.
	pkg-config --exact-version=$(GEXIV2_VERSION) --print-errors @PACKAGE_NAME@
	vala-gen-introspect @PACKAGE_NAME@ vapi

endif

# Optional Introspection #

if ENABLE_INTROSPECTION
TYPELIB_FILES=GExiv2-$(GIR_VERSION).typelib GExiv2-$(GIR_VERSION).gir
endif

BUILD_DIR=gexiv2

%.typelib: %.gir
	g-ir-compiler --includedir=$(BUILD_DIR) -o $@ $<

GExiv2-$(GIR_VERSION).gir: lib@PACKAGE_NAME@.la
	g-ir-scanner --verbose                          \
		--namespace=GExiv2                      \
		--nsversion=$(GIR_VERSION)              \
		--symbol-prefix=@PACKAGE_NAME@              \
		--include=GObject-2.0                   \
		--add-include-path=$(BUILD_DIR)         \
		--output $@                             \
		-I @PACKAGE_NAME@                           \
		$(GEXIV2_public_HEADERS)      \
		$(GEXIV2_sources)                   \
		--library lib@PACKAGE_NAME@.la                \
		$(LIBTOOL)                              \
		--pkg=gobject-2.0
