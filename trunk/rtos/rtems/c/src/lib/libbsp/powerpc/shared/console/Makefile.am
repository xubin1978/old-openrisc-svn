##
## $Id: Makefile.am,v 1.2 2001-09-27 12:01:06 chris Exp $
##

AUTOMAKE_OPTIONS = foreign 1.4

C_FILES = console.c inch.c polled_io.c uart.c

H_FILES = consoleIo.h keyboard.h uart.h

$(PROJECT_INCLUDE)/bsp:
	$(mkinstalldirs) $@

$(PROJECT_INCLUDE)/bsp/%.h: %.h
	$(INSTALL_DATA) $< $@

PREINSTALL_FILES += $(PROJECT_INCLUDE)/bsp \
    $(H_FILES:%.h=$(PROJECT_INCLUDE)/bsp/%.h)

all-local: $(PREINSTALL_FILES)

EXTRA_DIST = console.c consoleIo.h inch.c keyboard.h polled_io.c uart.c \
    uart.h

include $(top_srcdir)/../../../../../automake/local.am
