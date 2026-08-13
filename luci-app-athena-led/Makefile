include $(TOPDIR)/rules.mk

PKG_NAME:=athena-led
PKG_VERSION:=2.5.0
PKG_RELEASE:=1

PKG_MAINTAINER:=guangyin53222
PKG_LICENSE:=Apache-2.0

include $(INCLUDE_DIR)/package.mk

define Package/athena-led
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=AX6600 Athena LED Matrix Controller
  DEPENDS:=+libuci +libubus
endef

define Package/athena-led/description
  Core driver for JDCloud AX6600 (Athena) LED matrix display.
  Controls the LED panel via I2C/SPI.
endef

# 不编译，直接复制预编译好的二进制（由 CI Job 1 用 cargo-zigbuild 编译）
define Build/Compile
	# 二进制由 CI 预编译好放在 src/bin/athena-led
endef

define Package/athena-led/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/src/bin/athena-led $(1)/usr/bin/athena-led

	if [ -d ./files ]; then \
		$(CP) ./files/* $(1)/; \
	fi
endef

$(eval $(call BuildPackage,athena-led))
