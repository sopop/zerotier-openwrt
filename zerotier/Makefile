#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=zerotier
PKG_VERSION:=1.16.0
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/zerotier/ZeroTierOne/tar.gz/$(PKG_VERSION)?
PKG_HASH:=aa9de313d365bf0efb3871aaa56f2d323a08f46df47b627c4eff4f4203fa7fc5
PKG_BUILD_DIR:=$(BUILD_DIR)/ZeroTierOne-$(PKG_VERSION)

PKG_MAINTAINER:=Moritz Warning <moritzwarning@web.de>
PKG_LICENSE:=BSL 1.1
PKG_LICENSE_FILES:=LICENSE.txt

PKG_ASLR_PIE:=0
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=gc-sections

include $(INCLUDE_DIR)/package.mk

DEPENDS:= +libminiupnpc +libnatpmp +libjson-c +libpthread +librt +libstdcpp +libatomic

define Package/zerotier
  SECTION:=net
  CATEGORY:=Network
  TITLE:=ZeroTier One (with Controller)
  URL:=https://www.zerotier.com
  DEPENDS:= +libminiupnpc +libnatpmp +libjson-c +libpthread +librt +libstdcpp +libatomic
  SUBMENU:=VPN
endef

define Package/zerotier/description
	ZeroTier creates a global provider-independent virtual private cloud network.
endef

define Package/zerotier/config
	source "$(SOURCE)/Config.in"
endef

ifeq ($(CONFIG_ZEROTIER_ENABLE_DEBUG),y)
MAKE_FLAGS += ZT_DEBUG=1
endif

MAKE_FLAGS += \
	ZT_EMBEDDED=1 \
	ZT_SSO_SUPPORTED=0 \
	OSTYPE="Linux" \
	ZT_ENABLE_NETWORK_CONTROLLER=1 \
	ZT_CONTROLLER=1 \
	ZT_NONFREE_CONTROLLER=0 \
	ZT_CONTROLLER_USE_LIBPQ=0 \
	ZT_OPENTELEMETRY_ENABLED=0 \
	ZT_USE_REDIS=0 \
	ZT_RUSTYBITS=0

define Build/Compile
	$(MAKE) -C $(PKG_BUILD_DIR) \
		$(MAKE_FLAGS) \
		ZT_RUSTYBITS=0 \
		one
endef

# Make binary smaller
TARGET_CFLAGS += -Wl,-z,noexecstack
TARGET_LDFLAGS += -Wl,--as-needed -Wl,-z,noexecstack

define Package/zerotier/conffiles
/etc/config/zerotier
endef

define Package/zerotier/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/zerotier-one $(1)/usr/bin/
	$(LN) zerotier-one $(1)/usr/bin/zerotier-cli
	$(LN) zerotier-one $(1)/usr/bin/zerotier-idtool
	$(INSTALL_DIR) $(1)/etc/uci-defaults

ifeq ($(CONFIG_ZEROTIER_ENABLE_SELFTEST),y)
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/zerotier-selftest $(1)/usr/bin/
endif

	$(CP) ./files/* $(1)/
endef

$(eval $(call BuildPackage,zerotier))
