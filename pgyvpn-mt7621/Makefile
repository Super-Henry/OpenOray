include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/target.mk

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

# 名称改成 mt7621 专用
PKG_NAME:=pgyvpn-mt7621
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

define Package/pgyvpn-mt7621
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=VPN
  DEPENDS:=@(mipsel) +iptables +libc +libopenssl1.1 +librt +libpthread +libstdcpp +kmod-tun
  TITLE:=PuGongYing VPN, Fast networking
  URL:=https://pgy.oray.com/
endef

define Package/pgyvpn-mt7621/description
  PuGongYing VPN is a product of Oray Company, support custom network, account security
  system, traffic monitoring, cloud application access, virtual serial port, etc.
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
	# ====================== 读取本地文件（唯一改动） ======================
	cp ./files/usr/sbin/pgyvpnsvr $(PKG_BUILD_DIR)/
	cp ./files/usr/sbin/pgyvpn_oraysl $(PKG_BUILD_DIR)/
	chmod +x $(PKG_BUILD_DIR)/{pgyvpn_oraysl,pgyvpnsvr}
	rm -f $(PKG_BUILD_DIR)/Makefile $(PKG_BUILD_DIR)/configure
endef

define Build/Configure
	:
endef

define Build/Compile
	:
endef

define Package/pgyvpn-mt7621/install
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_DIR) $(1)/etc/hotplug.d/iface
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_DIR) $(1)/usr/share/pgyvpn

	$(CP) $(PKG_BUILD_DIR)/{pgyvpn_oraysl,pgyvpnsvr} $(1)/usr/sbin
	$(INSTALL_BIN) ./files/etc/init.d/* $(1)/etc/init.d
	$(INSTALL_CONF) ./files/etc/config/* $(1)/etc/config
	$(INSTALL_BIN) ./files/etc/hotplug.d/iface/* $(1)/etc/hotplug.d/iface
	$(INSTALL_BIN) ./files/usr/share/pgyvpn/* $(1)/usr/share/pgyvpn
endef

define Package/pgyvpn-mt7621/preinst
	#!/bin/sh
	[ -z "$${IPKG_INSTROOT}" ] && {
		pidof pgyvpnsvr > /dev/null && \
			test -f $${PKG_ROOT}etc/init.d/pgyvpn && \
				/etc/init.d/pgyvpn stop; true
	}
	exit 0
endef

define Package/pgyvpn-mt7621/postinst
	#!/bin/sh
	[ -z "$${IPKG_INSTROOT}" ] && {
		/etc/init.d/pgyvpn restart; true
	}
	exit 0
endef

$(eval $(call BuildPackage,pgyvpn-mt7621))
