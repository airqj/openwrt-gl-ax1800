include $(TOPDIR)/rules.mk

PKG_NAME:=wstunnel
PKG_VERSION:=10.4.4
PKG_RELEASE:=1
PKG_MAINTAINER:=Your Name <your.email@example.com>
PKG_LICENSE:=BSD-3-Clause

# Architecture detection
ifeq ($(ARCH),x86_64)
  PKG_ARCH:=x86_64-unknown-linux-musl
endif
ifeq ($(ARCH),i386)
  PKG_ARCH:=i686-unknown-linux-musl
endif
ifeq ($(ARCH),arm)
  PKG_ARCH:=linux_arm
endif
ifeq ($(ARCH),aarch64)
  PKG_ARCH:=linux_arm64
endif
ifeq ($(ARCH),mips)
  PKG_ARCH:=linux_mips
endif
ifeq ($(ARCH),mipsel)
  PKG_ARCH:=linux_mipsel
endif

PKG_SOURCE:=wstunnel_$(PKG_VERSION)_$(PKG_ARCH).tar.gz
PKG_SOURCE_URL:=https://github.com/erebe/wstunnel/releases/download/v$(PKG_VERSION)/
PKG_HASH:=skip

# 修正：移除 BUILDONLY，添加正确的构建依赖
# PKG_BUILD_DEPENDS:=upx/host

include $(INCLUDE_DIR)/package.mk

define Package/wstunnel
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Web Servers/Proxies
  TITLE:=Tunnel over websocket protocol
  URL:=https://github.com/erebe/wstunnel
  DEPENDS:=
endef

define Package/wstunnel/config
  # UPX compression options removed due to build issues
  # Binary is already optimized as static build
endef

define Package/wstunnel/description
  Tunnel TCP/UDP traffic over websocket protocol.
  This tool allows you to tunnel TCP and UDP traffic over 
  websocket connections, which can be useful for bypassing
  firewalls or NAT restrictions.
endef

define Package/wstunnel/conffiles
/etc/config/wstunnel
endef

define Build/Prepare
	$(INSTALL_DIR) $(PKG_BUILD_DIR)
	$(TAR) -xzf $(DL_DIR)/$(PKG_SOURCE) -C $(PKG_BUILD_DIR)
	# 查找wstunnel可执行文件并复制到标准位置
	if [ -f "$(PKG_BUILD_DIR)/wstunnel" ]; then \
		chmod +x $(PKG_BUILD_DIR)/wstunnel; \
	else \
		find $(PKG_BUILD_DIR) -name "wstunnel*" -type f -executable -exec cp {} $(PKG_BUILD_DIR)/wstunnel \; && \
		chmod +x $(PKG_BUILD_DIR)/wstunnel; \
	fi
	# 验证文件是否存在
	if [ ! -f "$(PKG_BUILD_DIR)/wstunnel" ]; then \
		echo "错误: 找不到wstunnel可执行文件"; \
		ls -la $(PKG_BUILD_DIR)/; \
		exit 1; \
	fi
endef

define Build/Compile
	# Check if system UPX is available
	if command -v upx >/dev/null 2>&1; then \
		echo "Using system UPX to compress binary..."; \
		upx --best --lzma $(PKG_BUILD_DIR)/wstunnel || echo "UPX compression failed, continuing..."; \
	else \
		echo "UPX not available, skipping compression"; \
	fi
	# Display binary size information
	@echo "Binary size information:"
	@ls -lh $(PKG_BUILD_DIR)/wstunnel
	@echo "Binary type:"
	@file $(PKG_BUILD_DIR)/wstunnel
endef

define Package/wstunnel/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/wstunnel $(1)/usr/bin/
	
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/wstunnel.config $(1)/etc/config/wstunnel
	
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/wstunnel.init $(1)/etc/init.d/wstunnel
endef

$(eval $(call BuildPackage,wstunnel))
