include $(TOPDIR)/rules.mk

LUCI_TITLE:=LuCI App - Athena LED Controller
LUCI_DESCRIPTION:=Web interface for AX6600 Athena LED matrix
LUCI_DEPENDS:=+athena-led +luci-base
LUCI_PKGARCH:=all

PKG_NAME:=luci-app-athena-led
PKG_VERSION:=2.5.0
PKG_RELEASE:=1

# === 关键修正：URL 指向你自己的仓库 ===
PKG_MAINTAINER:=guangyin53222
PKG_LICENSE:=Apache-2.0
PKG_URL:=https://github.com/guangyin53222/AX6600-Athena-LED-Controller

include $(TOPDIR)/luci.mk
