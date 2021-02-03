#
# Copyright (C) 2020 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=sysrepo
PKG_VERSION:=1.4.66
PKG_RELEASE:=4

PKG_LICENSE:=Apache-2.0
PKG_MAINTAINER:=Luka Paulic <luka.paulic@sartura.hr>

PKG_SOURCE:=${PKG_NAME}-${PKG_VERSION}.tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/sysrepo/sysrepo/tar.gz/v$(PKG_VERSION)?
PKG_HASH:=4dbb916c32e0229238a06bfa929e5615af89e9f79d225b558df99efe6ac84c03

CMAKE_INSTALL:=1

PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk
ifeq ($(CONFIG_SYSREPO_PYTHON),y)
$(call include_mk, python-package.mk)
endif

define Package/libsysrepo/config
	source "$(SOURCE)/Config_libsysrepo.in"
endef

PKG_BUILD_DEPENDS:=+SYSREPO_BINDINGS:swig/host

define Package/sysrepo
  SECTION:=utils
  CATEGORY:=Utilities
  URL:=$(PKG_SOURCE_URL)
  TITLE:=YANG-based data store library
  DEPENDS:=+libyang +libpthread +libatomic
  MENU:=1
endef

define Package/sysrepoctl
  SECTION:=utils
  CATEGORY:=Utilities
  URL:=$(PKG_SOURCE_URL)
  TITLE:=sysrepo cli tool
  DEPENDS:=+sysrepo
endef

define Package/sysrepocfg
  SECTION:=utils
  CATEGORY:=Utilities
  URL:=$(PKG_SOURCE_URL)
  TITLE:=sysrepo configuration tool
  DEPENDS:=+sysrepo
endef

define Package/sysrepo/description
Sysrepo is an YANG-based configuration and operational state data store for Unix/Linux applications.
endef

CMAKE_OPTIONS += \
	-DCMAKE_BUILD_TYPE:STRING=Release \
	-DREPO_PATH:PATH=/etc/sysrepo \
	-DCMAKE_INSTALL_PREFIX:PATH=/usr \

define Package/sysrepo/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libsysrepo.so* $(1)/usr/lib/

	$(INSTALL_DIR) $(1)/etc/sysrepo/yang
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/modules/ietf-netconf-notifications.yang $(1)/etc/sysrepo/yang/ietf-netconf-notifications@2012-02-06.yang
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/modules/ietf-netconf-with-defaults.yang $(1)/etc/sysrepo/yang/ietf-netconf-with-defaults@2011-06-01.yang
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/modules/ietf-netconf.yang $(1)/etc/sysrepo/yang/ietf-netconf@2011-06-01.yang

	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/sysrepo-plugind $(1)/usr/bin/

	$(INSTALL_DIR) $(1)/etc/init.d/
	$(INSTALL_BIN) ./files/sysrepo.init $(1)/etc/init.d/sysrepo

	$(INSTALL_DIR) $(1)/usr/lib/sysrepo/plugins
endef

define Package/sysrepoctl/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/sysrepoctl $(1)/usr/bin/
endef

define Package/sysrepocfg/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/sysrepocfg $(1)/usr/bin/
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libsysrepo.so* $(1)/usr/lib/

	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/usr/lib/pkgconfig/sysrepo.pc $(1)/usr/lib/pkgconfig/

	$(INSTALL_DIR) $(1)/usr/include/sysrepo
	$(CP) $(PKG_INSTALL_DIR)/usr/include/* $(1)/usr/include
endef

$(eval $(call BuildPackage,sysrepo))
$(eval $(call BuildPackage,sysrepoctl))
$(eval $(call BuildPackage,sysrepocfg))
