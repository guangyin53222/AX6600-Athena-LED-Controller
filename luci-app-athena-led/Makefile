#
# luci-app-athena-led - LuCI Web Interface for Athena LED Controller
# Feed package Makefile (JS architecture, no luci-compat needed)
#
# Installation via feed:
#   echo "src-git athena https://github.com/guangyin53222/AX6600-Athena-LED-Controller.git;main" >> feeds.conf
#   ./scripts/feeds update athena
#   ./scripts/feeds install -a -p athena
#

include $(TOPDIR)/rules.mk

PKG_NAME:=luci-app-athena-led
PKG_VERSION:=2.5.0
PKG_RELEASE:=1

PKG_MAINTAINER:=guangyin53222
PKG_LICENSE:=Apache-2.0
PKG_LICENSE_FILES:=LICENSE

include $(INCLUDE_DIR)/package.mk

define Package/$(PKG_NAME)
  SECTION:=luci
  CATEGORY:=LuCI
  SUBMENU:=3. Applications
  TITLE:=LuCI Support for Athena LED (JS UI)
  URL:=https://github.com/guangyin53222/AX6600-Athena-LED-Controller
  DEPENDS:=+athena-led +luci-base
  PKGARCH:=all
endef

define Package/$(PKG_NAME)/description
 Web UI for the JDCloud AX6600 (Athena) LED screen controller.
 Modern LuCI JS architecture (no luci-compat needed).
 Features: visual config with drag-drop sorting, advanced monitoring,
 smart sleep scheduling, service control, MQTT display.
endef

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
endef

define Build/Compile
	# Compile Chinese translation if available
	[ -d ./po/zh_Hans ] && po2lmo ./po/zh_Hans/athena_led.po $(PKG_BUILD_DIR)/zh_Hans.lmo || true
endef

define Package/$(PKG_NAME)/install
	# 1. JS view
	$(INSTALL_DIR) $(1)/www/luci-static/resources/view/athena_led
	$(INSTALL_DATA) ./htdocs/luci-static/resources/view/athena_led/settings.js \
		$(1)/www/luci-static/resources/view/athena_led/

	# 2. Menu registration (JSON declarative, replaces Lua controller)
	$(INSTALL_DIR) $(1)/usr/share/luci/menu.d
	$(INSTALL_DATA) ./root/usr/share/luci/menu.d/luci-app-athena-led.json \
		$(1)/usr/share/luci/menu.d/

	# 3. rpcd ACL (uci read/write, status query, service control)
	$(INSTALL_DIR) $(1)/usr/share/rpcd/acl.d
	$(INSTALL_DATA) ./root/usr/share/rpcd/acl.d/luci-app-athena-led.json \
		$(1)/usr/share/rpcd/acl.d/

	# 4. i18n (dual-suffix for maximum compatibility)
	$(INSTALL_DIR) $(1)/usr/lib/lua/luci/i18n
	[ -f $(PKG_BUILD_DIR)/zh_Hans.lmo ] && { \
		$(INSTALL_DATA) $(PKG_BUILD_DIR)/zh_Hans.lmo $(1)/usr/lib/lua/luci/i18n/athena_led.zh-cn.lmo; \
		$(INSTALL_DATA) $(PKG_BUILD_DIR)/zh_Hans.lmo $(1)/usr/lib/lua/luci/i18n/athena_led.zh_Hans.lmo; \
	} || true
endef

define Package/$(PKG_NAME)/postinst
#!/bin/sh
if [ -z "$${IPKG_INSTROOT}" ]; then
	rm -rf /tmp/luci-indexcache /tmp/luci-modulecache/* 2>/dev/null
	# ACL changes require rpcd reload
	/etc/init.d/rpcd reload >/dev/null 2>&1
fi
exit 0
endef

$(eval $(call BuildPackage,$(PKG_NAME)))
