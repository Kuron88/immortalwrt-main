#
# Copyright (C) 2011-2013 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=perf
PKG_VERSION:=$(LINUX_VERSION)
PKG_RELEASE:=6

PKG_BUILD_FLAGS:=no-mips16 no-lto
PKG_BUILD_PARALLEL:=1
PKG_MAINTAINER:=Felix Fietkau <nbd@nbd.name>
PKG_FLAGS:=nonshared

# Perf's makefile and headers are not relocatable and must be built from the
# Linux sources directory
PKG_BUILD_DIR:=$(LINUX_DIR)/tools/perf-$(TARGET_DIR_NAME)

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk

define Package/perf
  SECTION:=devel
  CATEGORY:=Development
  DEPENDS:= +libelf +libdw +PACKAGE_libunwind:libunwind +libpthread +librt +objdump @!IN_SDK @KERNEL_PERF_EVENTS \
	    +PACKAGE_libbfd:libbfd +PACKAGE_libopcodes:libopcodes +libtraceevent
  TITLE:=Linux performance monitoring tool
  VERSION:=$(LINUX_VERSION)-$(PKG_RELEASE)
  URL:=http://www.kernel.org
endef

define Package/perf/description
  perf is the Linux performance monitoring tool
endef

HOST_CFLAGS += -I$(LINUX_DIR)/tools/include

TARGET_LDFLAGS += $(INTL_LDFLAGS)

MAKE_FLAGS = \
	ARCH="$(LINUX_KARCH)" \
	NO_LIBPERL=1 \
	NO_LIBPYTHON=1 \
	NO_NEWT=1 \
	NO_LZMA=1 \
	NO_BACKTRACE=1 \
	NO_LIBNUMA=1 \
	NO_SLANG=1 \
	NO_GTK2=1 \
	NO_SLANG=1 \
	NO_LIBAUDIT=1 \
	NO_LIBCRYPTO=1 \
	NO_LIBUNWIND=1 \
	NO_LIBZSTD=1 \
	NO_LIBCAP=1 \
	CROSS_COMPILE="$(TARGET_CROSS)" \
	CC="$(TARGET_CC)" \
	LD="$(TARGET_CROSS)ld" \
	EXTRA_CFLAGS="$(TARGET_CFLAGS) $(TARGET_CPPFLAGS)" \
	LDFLAGS="$(TARGET_LDFLAGS)" \
	KBUILD_HOSTCFLAGS="$(HOST_CFLAGS)" \
	$(if $(findstring c,$(OPENWRT_VERBOSE)),V=1,V='') \
	PKG_CONFIG="$(PKG_CONFIG)" \
	PKG_CONFIG_PATH="$(PKG_CONFIG_PATH)" \
	EXCLUDE_EXTLIBS="-lstdc++" \
	EXTRA_PERFLIBS="$(shell $(TARGET_CC) -print-file-name=libstdc++.a)" \
	WERROR=0 \
	O=$(PKG_BUILD_DIR) \
	prefix=/usr

ifeq ($(LINUX_KARCH),powerpc)
	MAKE_FLAGS += NO_AUXTRACE=1
endif

define Build/Compile
	+$(MAKE) $(PKG_JOBS) $(MAKE_FLAGS) \
		--no-print-directory \
		-C $(LINUX_DIR)/tools/perf \
		-f Makefile.perf
endef

define Package/perf/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/perf $(1)/usr/bin/
endef

$(eval $(call BuildPackage,perf))
