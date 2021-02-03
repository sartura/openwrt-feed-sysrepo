#
# Copyright (C) 2020 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

# Warning by default OpenWrt does not have a root password which is necessery for NETCONF server.

include $(TOPDIR)/rules.mk

PKG_NAME:=netopeer2
PKG_VERSION:=1.1.34
PKG_RELEASE:=4

PKG_LICENSE:=BSD-3-Clause
PKG_MAINTAINER:=Luka Paulic <luka.paulic@sartura.hr>

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/CESNET/netopeer2/tar.gz/v$(PKG_VERSION)?
PKG_HASH:=66f3ad68cc6e41f3231b090ef27016ccbfe007cda7d08ec19f409f7429f46ff9

CMAKE_INSTALL:=1

PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

define Package/netopeer2-server
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=NETCONF server
  URL:=$(PKG_SOURCE_URL)
  DEPENDS:=+libcurl +libpthread +libyang +libnetconf2 +sysrepo +sysrepocfg +sysrepoctl
  MENU:=1
endef

define Package/netopeer2-cli
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=netopeer2 cli tool
  URL:=$(PKG_SOURCE_URL)
  DEPENDS:=+libpthread +libyang +libnetconf2 +libopenssl
endef

define Package/netopeer2/description
 Netopeer2 is a set of tools implementing network configuration tools based on the NETCONF
 Protocol. This is the second generation of the toolset, originally available as the Netopeer
 project. Netopeer2 is based on the new generation of the NETCONF and YANG libraries -
 libyang and libnetconf2. The Netopeer server uses sysrepo as a NETCONF datastore implementation.
endef

CMAKE_OPTIONS += \
	-DCMAKE_INSTALL_PREFIX:PATH=/usr \
	-DCMAKE_BUILD_TYPE:STRING=Release \
	-DINSTALL_MODULES:BOOL=OFF \
	-DGENERATE_HOSTKEY:BOOL=OFF \
	-DMERGE_LISTEN_CONFIG:BOOL=OFF \

ifndef PACKAGE_netopeer2-cli
	CMAKE_OPTIONS += BUILD_CLI:BOOL=OFF
endif

define Package/netopeer2-server/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/netopeer2-server $(1)/usr/bin/

	$(INSTALL_DIR) $(1)/etc/sysrepo/yang
	$(INSTALL_DIR) $(1)/etc/netopeer2/modules
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/modules/* $(1)/etc/netopeer2/modules/

	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_BIN) ./files/netopeer2-server-setup.default $(1)/etc/uci-defaults/97_netopeer2-server
	$(INSTALL_BIN) ./files/netopeer2-server-merge-hostkey.default $(1)/etc/uci-defaults/98_netopeer2-server
	$(INSTALL_BIN) ./files/netopeer2-server-merge-config.default $(1)/etc/uci-defaults/99_netopeer2-server

	$(INSTALL_DIR) $(1)/etc/init.d/
	$(INSTALL_BIN) ./files/netopeer2-server.init $(1)/etc/init.d/netopeer2-server
endef

define Package/netopeer2-cli/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/netopeer2-cli $(1)/usr/bin/
endef

$(eval $(call BuildPackage,netopeer2-server))
$(eval $(call BuildPackage,netopeer2-cli))
