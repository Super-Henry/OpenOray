include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/target.mk

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)-ipq5018

ifneq ($(CONFIG_USE_UCLIBC),)
  PKG_LIBC_V:=uclibc-1.0.x
else
  PKG_LIBC_V:=$(LIBC)-unknown
endif

ifeq ($(ARCH),arm)
  ifneq ($(findstring $(CPU_SUBTYPE), neon neon-vfpv4 vfpv3),)
	PKG_ARCH_PGYYPN:=arm_$(CPU_TYPE)_$(CPU_SUBTYPE)-$(PKG_LIBC_V)
  else
	PKG_ARCH_PGYYPN:=arm_cortex-a7-$(PKG_LIBC_V)
  endif
endif
ifeq ($(ARCH),aarch64)
  ifneq ($(findstring $(CPU_TYPE), cortex-a53 cortex-a72),)
	PKG_ARCH_PGYYPN:=aarch64_$(CPU_TYPE)-$(PKG_LIBC_V)
  else
	PKG_ARCH_PGYYPN:=aarch64_generic-$(PKG_LIBC_V)
  endif
endif
ifeq ($(ARCH),mips)
	PKG_ARCH_PGYYPN:=mips_24kc-$(PKG_LIBC_V)
endif
ifeq ($(ARCH),mipsel)
  ifneq ($(findstring $(CPU_TYPE), 24kec 74kc 1004kc),)
	PKG_ARCH_PGYYPN:=$(ARCH)_$(CPU_TYPE)-$(PKG_LIBC_V)
  else
	PKG_ARCH_PGYYPN:=mipsel_24kc-$(PKG_LIBC_V)
  endif
endif
ifeq ($(BOARD),x86)
  ifeq ($(ARCH),x86_64)
	PKG_ARCH_PGYYPN:=x86_64-$(PKG_LIBC_V)
  else
	PKG_ARCH_PGYYPN:=i386_pentium-mmx-$(PKG_LIBC_V)
  endif
endif

# 名称改成 ipq5018 专用
PKG_NAME:=pgyvpn-ipq5018
PKG_VERSION:=3.1.0
PKG_RELEASE:=1

# ====================== 关闭官方下载（唯一改动） ======================
PKG_SOURCE:=
PKG_SOURCE_URL:=
# ======================================================================

PKG_HASH:=skip
PKG_FLAGS:=nonshared
PKG_MAINTAINER:=Oray <developer@oray.com>
PKG_USE_MIPS16:=0

include $(INCLUDE_DIR)/package.mk

define Package/pgyvpn-ipq5018
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=VPN
  DEPENDS:=@(aarch64) +iptables +libc +libopenssl1.1 +librt +libpthread +libstdcpp +kmod-tun
  TITLE:=PuGongYing VPN (IPQ5018)
  URL:=https://pgy.oray.com/
endef

define Package/pgyvpn-ipq5018/description
  PuGongYing VPN for IPQ5018, no conflict with stock pgyvpn
endef

# ====================== 禁用下载 ======================
# define Download/extra
#   FILE:=pgyvpn_oraysl
#   URL:=$(PKG_SOURCE_URL)
#   HASH:=skip
# endef
# $(eval $(call Download,extra))

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
	# 复制并重命名二进制文件，避免和原版冲突
	cp ./files/usr/sbin/pgyvpnsvr $(PKG_BUILD_DIR)/pgyvpnsvr-ipq5018
	cp ./files/usr/sbin/pgyvpn_oraysl $(PKG_BUILD_DIR)/pgyvpn_oraysl-ipq5018
	chmod +x $(PKG_BUILD_DIR)/pgyvpnsvr-ipq5018 $(PKG_BUILD_DIR)/pgyvpn_oraysl-ipq5018
	rm -f $(PKG_BUILD_DIR)/Makefile $(PKG_BUILD_DIR)/configure
endef

define Build/Configure
	:
endef

define Build/Compile
	:
endef

define Package/pgyvpn-ipq5018/install
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_DIR) $(1)/etc/hotplug.d/iface
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_DIR) $(1)/usr/share/pgyvpn-ipq5018

	# 安装带 ipq5018 后缀的执行文件
	$(CP) $(PKG_BUILD_DIR)/pgyvpnsvr-ipq5018 $(1)/usr/sbin/
	$(CP) $(PKG_BUILD_DIR)/pgyvpn_oraysl-ipq5018 $(1)/usr/sbin/

	# 安装独立的配置、脚本、资源目录
	$(INSTALL_BIN) ./files/etc/init.d/pgyvpn-ipq5018 $(1)/etc/init.d/
	$(INSTALL_CONF) ./files/etc/config/pgyvpn-ipq5018 $(1)/etc/config/
	$(INSTALL_BIN) ./files/etc/hotplug.d/iface/pgyvpn-ipq5018 $(1)/etc/hotplug.d/iface/
	$(CP) ./files/usr/share/pgyvpn-ipq5018/* $(1)/usr/share/pgyvpn-ipq5018/
endef

# ====================== 已屏蔽 安装脚本 ======================
# 以下内容全部注释/删除，解决 OpenWrt 25.2 编译报错
#
# define Package/pgyvpn-ipq5018/preinst
# 	#!/bin/sh
# 	[ -z "$${IPKG_INSTROOT}" ] && {
# 		pidof pgyvpnsvr > /dev/null && \
# 			test -f $${PKG_ROOT}etc/init.d/pgyvpn && \
# 				/etc/init.d/pgyvpn stop; true
# 	}
# 	exit 0
# endef
#
# define Package/pgyvpn-ipq5018/postinst
# 	#!/bin/sh
# 	[ -z "$${IPKG_INSTROOT}" ] && {
# 		/etc/init.d/pgyvpn restart; true
# 	}
# 	exit 0
# endef
# ==============================================================

$(eval $(call BuildPackage,pgyvpn-ipq5018))
