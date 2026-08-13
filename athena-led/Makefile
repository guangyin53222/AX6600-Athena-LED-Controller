# SPDX-License-Identifier: GPL-2.0-only

include $(TOPDIR)/rules.mk

PKG_NAME:=athena-led
PKG_VERSION:=2.5.0
PKG_RELEASE:=1

# 使用预编译的 musl 二进制（由 CI 的 build-rust job 产出）
PKG_SOURCE_NAME:=athena-led-aarch64-unknown-linux-musl-v$(PKG_VERSION).tar.gz
PKG_SOURCE:=$(PKG_SOURCE_NAME)
PKG_SOURCE_URL:=https://github.com/guangyin53222/AX6600-Athena-LED-Controller/releases/download/v$(PKG_VERSION)/
PKG_HASH:=skip

PKG_MAINTAINER:=guangyin53222
PKG_LICENSE:=Apache-2.0

include $(INCLUDE_DIR)/package.mk

define Package/athena-led
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=AX6600 Athena LED Matrix Controller (Core)
  DEPENDS:=@USE_MUSL @aarch64
endef

define Package/athena-led/description
  Core driver for JDCloud AX6600 (Athena) LED matrix display.
  Controls the LED panel via I2C/SPID.
  Pre-compiled static binary (aarch64-unknown-linux-musl).
endef

# 不需要 Rust 编译——直接用预编译好的静态二进制
define Build/Compile
	# 二进制已经在 Build/Prepare 阶段从 tarball 解压出来了
	# 确认它在 build dir 里
	ls -la $(PKG_BUILD_DIR)/athena-led
endef

define Package/athena-led/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/athena-led $(1)/usr/bin/athena-led

	# 安装 init 脚本（如果 files/ 里有）
	if [ -f ./files/etc/init.d/athena-led ]; then \
		$(INSTALL_DIR) $(1)/etc/init.d; \
		$(INSTALL_BIN) ./files/etc/init.d/athena-led $(1)/etc/init.d/; \
	fi

	# 安装 UCI 默认配置（如果 files/ 里有）
	if [ -f ./files/etc/config/athena-led ]; then \
		$(INSTALL_DIR) $(1)/etc/config; \
		$(INSTALL_CONF) ./files/etc/config/athena-led $(1)/etc/config/; \
	fi
endef

$(eval $(call BuildPackage,athena-led))
