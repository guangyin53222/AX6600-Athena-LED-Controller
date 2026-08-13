#
# athena-led - JDCloud AX6600 (Athena) LED Matrix Controller Core
# Feed package Makefile
#
# Installation via feed:
#   echo "src-git athena https://github.com/guangyin53222/AX6600-Athena-LED-Controller.git;main" >> feeds.conf
#   ./scripts/feeds update athena
#   ./scripts/feeds install -a -p athena
#

include $(TOPDIR)/rules.mk

PKG_NAME:=athena-led
PKG_VERSION:=2.5.0
PKG_RELEASE:=1

PKG_MAINTAINER:=guangyin53222
PKG_LICENSE:=Apache-2.0
PKG_LICENSE_FILES:=LICENSE

# Pre-compiled Rust binary (aarch64-unknown-linux-musl)
PKG_SOURCE:=athena-led-aarch64-unknown-linux-musl-v$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/guangyin53222/AX6600-Athena-LED-Controller/releases/download/v$(PKG_VERSION)/
PKG_HASH:=skip

include $(INCLUDE_DIR)/package.mk

define Package/$(PKG_NAME)
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=JDCloud AX6600 (Athena) LED matrix screen daemon
  URL:=https://github.com/guangyin53222/AX6600-Athena-LED-Controller
  DEPENDS:=@(aarch64||arm)
endef

define Package/$(PKG_NAME)/description
 Rust daemon driving the JDCloud AX6600 (Athena) LED matrix screen.
 Features: network monitoring, system status, weather, smart sleep,
 MQTT integration, GPIO dual-backend auto-detection (cdev/sysfs).
endef

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
	tar xf $(DL_DIR)/$(PKG_SOURCE) -C $(PKG_BUILD_DIR)
endef

# Pre-compiled binary, no build step needed
define Build/Compile
endef

define Package/$(PKG_NAME)/conffiles
/etc/config/athena_led
endef

define Package/$(PKG_NAME)/install
	# 1. Main binary
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/athena-led $(1)/usr/bin/

	# 2. Button detection script
	$(INSTALL_BIN) ./files/find_button.sh $(1)/usr/bin/find_button

	# 3. init.d script
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/athena_led.init $(1)/etc/init.d/athena_led

	# 4. UCI config
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/athena_led.config $(1)/etc/config/athena_led

	# 5. Preset animations
	$(INSTALL_DIR) $(1)/etc/athena_led/anim
	$(CP) ./files/anim/*.bin $(1)/etc/athena_led/anim/ 2>/dev/null || true
endef

define Package/$(PKG_NAME)/postinst
#!/bin/sh
if [ -z "$${IPKG_INSTROOT}" ]; then
	# Config migration for upgrades
	if [ -f /etc/config/athena_led.apk-new ]; then
		mv /etc/config/athena_led /etc/config/athena_led.v1_bak 2>/dev/null
		mv /etc/config/athena_led.apk-new /etc/config/athena_led
	fi
	if [ -f /etc/config/athena_led-opkg ]; then
		mv /etc/config/athena_led /etc/config/athena_led.v1_bak 2>/dev/null
		mv /etc/config/athena_led-opkg /etc/config/athena_led
	fi
	/etc/init.d/athena_led enable 2>/dev/null
	/etc/init.d/athena_led restart >/dev/null 2>&1
fi
exit 0
endef

define Package/$(PKG_NAME)/prerm
#!/bin/sh
if [ -z "$${IPKG_INSTROOT}" ]; then
	/etc/init.d/athena_led stop >/dev/null 2>&1
	/etc/init.d/athena_led disable 2>/dev/null
fi
exit 0
endef

$(eval $(call BuildPackage,$(PKG_NAME)))
