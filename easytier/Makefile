include $(TOPDIR)/rules.mk
-include $(dir $(lastword $(MAKEFILE_LIST)))../version.mk

PKG_NAME:=easytier
PKG_VERSION:=$(or $(EASYTIER_VERSION),2.7.0)

ifeq ($(ARCH),mipsel)
	APP_ARCH:=mipsel
endif
ifeq ($(ARCH),mips)
	APP_ARCH:=mips
endif
ifeq ($(ARCH),arm)
	APP_ARCH:=arm
endif
ifeq ($(BOARD),kirkwood)
	APP_ARCH:=arm
endif
ifeq ($(ARCH),armv7)
	APP_ARCH:=armv7
endif
ifeq ($(ARCH),aarch64)
	APP_ARCH:=aarch64
endif
ifeq ($(ARCH),arm64)
	APP_ARCH:=aarch64
endif
ifeq ($(ARCH),armv8)
	APP_ARCH:=aarch64
endif
ifeq ($(ARCH),x86_64)
	APP_ARCH:=x86_64
endif

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk

RSTRIP:=:

define Package/$(PKG_NAME)
	SECTION:=net
	CATEGORY:=Network
	TITLE:=A simple, decentralized mesh VPN with WireGuard support.
	DEPENDS:=@(x86_64||arm||aarch64||mipsel||mips) +kmod-tun
	URL:=https://github.com/EasyTier/EasyTier
	MENU:=1
endef

define Package/$(PKG_NAME)/description
  A simple, decentralized mesh VPN with WireGuard support.
endef

define Package/$(PKG_NAME)/config
	config EASYTIER_INCLUDE_WEBCONSOLE
		bool "Include Web Console (easytier-web)"
		depends on PACKAGE_$(PKG_NAME)
		depends on !(mips || mipsel)
		default y
		help
		  Install the easytier-web web console.
endef

# Bundled prebuilt binaries (offline build): files/bin/
EASYTIER_LOCAL_BIN:=$(CURDIR)/files/bin

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
	if [ -f $(EASYTIER_LOCAL_BIN)/easytier-core ]; then \
		echo "Using bundled easytier binaries"; \
		cp -f $(EASYTIER_LOCAL_BIN)/easytier-core $(PKG_BUILD_DIR)/; \
		cp -f $(EASYTIER_LOCAL_BIN)/easytier-cli $(PKG_BUILD_DIR)/; \
		[ ! -f $(EASYTIER_LOCAL_BIN)/easytier-web-embed ] || cp -f $(EASYTIER_LOCAL_BIN)/easytier-web-embed $(PKG_BUILD_DIR)/; \
	elif [ ! -f $(PKG_BUILD_DIR)/easytier-core ]; then \
		echo "Downloading easytier $(PKG_VERSION) for $(APP_ARCH)"; \
		wget https://github.com/EasyTier/EasyTier/releases/download/v$(PKG_VERSION)/$(PKG_NAME)-linux-$(APP_ARCH)-v$(PKG_VERSION).zip -O $(PKG_BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION).zip; \
		unzip -o -j $(PKG_BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION).zip -d $(PKG_BUILD_DIR); \
		rm -f $(PKG_BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION).zip; \
	fi
endef

define Build/Compile
endef

define Package/$(PKG_NAME)/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(CP) $(PKG_BUILD_DIR)/easytier-core $(1)/usr/bin/
	chmod 0755 $(1)/usr/bin/easytier-core
	$(CP) $(PKG_BUILD_DIR)/easytier-cli $(1)/usr/bin/
	chmod 0755 $(1)/usr/bin/easytier-cli
ifdef CONFIG_EASYTIER_INCLUDE_WEBCONSOLE
	$(CP) $(PKG_BUILD_DIR)/easytier-web-embed $(1)/usr/bin/easytier-web
	chmod 0755 $(1)/usr/bin/easytier-web
endif
endef

$(eval $(call BuildPackage,$(PKG_NAME)))
