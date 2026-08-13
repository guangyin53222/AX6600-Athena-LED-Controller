#
# AX6600 Athena LED Controller - Core Daemon
# Maintainer: guangyin53222
#

include $(TOPDIR)/rules.mk

PKG_NAME:=athena-led
PKG_VERSION:=2.5.0
PKG_RELEASE:=1

# === 关键修正 1：下载地址必须指向你自己的仓库 ===
PKG_SOURCE:=athena-led-aarch64-unknown-linux-musl-v$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/guangyin53222/AX6600-Athena-LED-Controller/releases/download/v$(PKG_VERSION)/
PKG_HASH:=skip

# === 关键修正 2：维护者改成你自己 ===
PKG_MAINTAINER:=guangyin53222
PKG_LICENSE:=Apache-2.0
PKG_LICENSE_FILES:=LICENSE

include $(INCLUDE_DIR)/package.mk

define Package/$(PKG_NAME)
	SECTION:=utils
	CATEGORY:=Utilities
	TITLE:=JDCloud AX6600 (Athena) LED matrix screen daemon
	URL:=https://github.com/guangyin53222/AX6600-Athena-LED-Controller
	DEPENDS:=@TARGET_mediatek_filogic +libuci +libubus
endef

define Package/$(PKG_NAME)/description
Rust daemon driving the JDCloud AX6600 (Athena) LED matrix screen.
Prebuilt aarch64-musl binary, works across QWRT / iStoreOS / official OpenWrt.
endef

# 预编译二进制，无需 Rust 编译环境
define Build/Compile
endef

# === 关键修正 3：files 路径修正（Feed 模式下必须用 CURDIR）===
define Package/$(PKG_NAME)/install
	# 1. 主程序（tar.gz 解压后顶层就是 athena-led）
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/athena-led $(1)/usr/bin/

	# 2. 物理按键探测脚本
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(CURDIR)/files/find_button.sh $(1)/usr/bin/find_button

	# 3. init.d 守护脚本
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) $(CURDIR)/files/athena_led.init $(1)/etc/init.d/athena_led

	# 4. UCI 配置文件
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) $(CURDIR)/files/athena_led.config $(1)/etc/config/athena_led

	# 5. 预置动画 (.bin)
	$(INSTALL_DIR) $(1)/etc/athena_led/anim
	$(CP) $(CURDIR)/files/anim/*.bin $(1)/etc/athena_led/anim/
endef

define Package/$(PKG_NAME)/postinst
#!/bin/sh
if [ -z "$${IPKG_INSTROOT}" ]; then
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
