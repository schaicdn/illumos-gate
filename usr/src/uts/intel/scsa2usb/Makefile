#
# CDDL HEADER START
#
# The contents of this file are subject to the terms of the
# Common Development and Distribution License (the "License").
# You may not use this file except in compliance with the License.
#
# You can obtain a copy of the license at usr/src/OPENSOLARIS.LICENSE
# or http://www.opensolaris.org/os/licensing.
# See the License for the specific language governing permissions
# and limitations under the License.
#
# When distributing Covered Code, include this CDDL HEADER in each
# file and include the License file at usr/src/OPENSOLARIS.LICENSE.
# If applicable, add the following below this CDDL HEADER, with the
# fields enclosed by brackets "[]" replaced with your own identifying
# information: Portions Copyright [yyyy] [name of copyright owner]
#
# CDDL HEADER END
#
#
# uts/intel/scsa2usb/Makefile
# Copyright 2009 Sun Microsystems, Inc.  All rights reserved.
# Use is subject to license terms.
#
#
#	This makefile drives the production of the scsa2usb driver
#	kernel module. intel architecture dependent
#

#
#	Path to the base of the uts directory tree (usually /usr/src/uts).
#
UTSBASE	= ../..

#
#	Define the module and object file sets.
#
MODULE		= scsa2usb
OBJECTS		= $(SCSA2USB_OBJS:%=$(OBJS_DIR)/%)
LINTS		= $(SCSA2USB_OBJS:%.o=$(LINTS_DIR)/%.ln)
ROOTMODULE	= $(ROOT_DRV_DIR)/$(MODULE)
CONF_SRCDIR	= $(UTSBASE)/common/io/usb/scsa2usb
WARLOCK_OUT	= $(SCSA2USB_OBJS:%.o=%.ll)
WARLOCK_OK	= $(MODULE).ok
WLCMD_DIR	= $(UTSBASE)/common/io/warlock

#
#	Include common rules.
#
include $(UTSBASE)/intel/Makefile.intel

#
#	Define targets
#
ALL_TARGET	= $(BINARY) $(SRC_CONFILE)
LINT_TARGET	= $(MODULE).lint
INSTALL_TARGET	= $(BINARY) $(ROOTMODULE) $(ROOT_CONFFILE)

#
#	Override defaults to build a unique, local modstubs.o.
#
MODSTUBS_DIR	 = $(OBJS_DIR)
CLEANFILES	+= $(MODSTUBS_O)

#
# depends on misc/usba misc/scsi
#
LDFLAGS		+= -dy -Nmisc/usba -Nmisc/scsi

#
# For now, disable these lint checks; maintainers should endeavor
# to investigate and remove these for maximum lint coverage.
# Please do not carry these forward to new Makefiles.
#
LINTTAGS	+= -erroff=E_BAD_PTR_CAST_ALIGN

CERRWARN	+= -_gcc=-Wno-unused-label
CERRWARN	+= -_gcc=-Wno-uninitialized

#
#	Default build targets.
#
.KEEP_STATE:

def:		$(DEF_DEPS)

all:		$(ALL_DEPS)

clean:		$(CLEAN_DEPS)
		$(RM) $(WARLOCK_OUT) $(WARLOCK_OK)

clobber:	$(CLOBBER_DEPS)
		$(RM) $(WARLOCK_OUT) $(WARLOCK_OK)

lint:		$(LINT_DEPS)

modlintlib:	$(MODLINTLIB_DEPS)

clean.lint:	$(CLEAN_LINT_DEPS)

install:	$(INSTALL_DEPS)

#
#	Include common targets.
#
include $(UTSBASE)/intel/Makefile.targ

#
#	Defines for local commands.
#
WARLOCK		= warlock
WLCC		= wlcc
TOUCH		= touch
TEST		= test

#
#	lock_lint rules
#
USBA_FILES = $(USBA_WITHOUT_WUSB_OBJS:%.o=../usba/%.ll)
UHCI_FILES = $(UHCI_OBJS:%.o=../uhci/%.ll)
OHCI_FILES = $(OHCI_OBJS:%.o=../ohci/%.ll)
EHCI_FILES = $(EHCI_OBJS:%.o=../ehci/%.ll)
SD_FILES   = $(SD_OBJS:%.o=../sd/%.ll)
ST_FILES   = $(ST_OBJS:%.o=../st/%.ll)
SCSI_FILES = $(SCSI_OBJS:%.o= -l ../scsi/%.ll)
CMLB_FILES = $(CMLB_OBJS:%.o=-l ../cmlb/%.ll)

warlock: $(WARLOCK_OK) warlock_with_usba

$(WARLOCK_OK): $(WARLOCK_OUT) $(WLCMD_DIR)/scsa2usb.wlcmd warlock_ddi.files \
	scsi.files
	$(WARLOCK) -c $(WLCMD_DIR)/scsa2usb.wlcmd $(WARLOCK_OUT)  \
		../warlock/scsi.ll \
		$(SCSI_FILES) \
		-l ../warlock/ddi_dki_impl.ll
	$(TOUCH) $@

%.ll: $(UTSBASE)/common/io/usb/scsa2usb/%.c \
		$(UTSBASE)/common/sys/usb/clients/mass_storage/usb_bulkonly.h \
		$(UTSBASE)/common/sys/usb/clients/mass_storage/usb_cbi.h \
		$(UTSBASE)/common/sys/usb/scsa2usb/scsa2usb.h
	$(WLCC)  $(CPPFLAGS) -DDEBUG -o $@ $<

warlock_with_usba: $(WLCMD_DIR)/scsa2usb_with_usba.wlcmd \
	$(WARLOCK_OUT) usba_files \
	ohci_files ehci_files uhci_files warlock_ddi.files sd.files st.files \
	cmlb.files scsi.files
	$(WARLOCK) -c $(WLCMD_DIR)/scsa2usb_with_usba.wlcmd \
		$(USBA_FILES) $(OHCI_FILES) $(EHCI_FILES) $(UHCI_FILES) \
		$(SD_FILES) \
		$(ST_FILES) \
		$(CMLB_FILES) \
		$(SCSI_FILES) \
		$(WARLOCK_OUT) \
		-l ../warlock/ddi_dki_impl.ll

usba_files:
	@cd ../usba;pwd; $(MAKE) warlock

uhci_files:
	@cd ../uhci;pwd; $(MAKE) warlock

ohci_files:
	@cd ../ohci;pwd; $(MAKE) warlock

ehci_files:
	@cd ../ehci;pwd; $(MAKE) warlock

warlock_ddi.files:
	@cd ../warlock; pwd; $(MAKE) warlock

scsi.files:
	@cd ../scsi; pwd; $(MAKE) warlock

sd.files:
	@cd ../sd; pwd; $(MAKE) warlock_alone

st.files:
	@cd ../st; pwd; $(MAKE) warlock_alone

cmlb.files:
	 @cd ../cmlb; pwd; $(MAKE) warlock
