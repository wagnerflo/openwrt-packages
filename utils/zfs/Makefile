include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=zfs
PKG_VERSION:=2.4.1
PKG_RELEASE:=1

PKG_LICENSE:=CDDL
PKG_LICENSE_FILES:=LICENSE

PKG_SOURCE:=zfs-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/openzfs/zfs/releases/download/zfs-$(PKG_VERSION)
PKG_HASH:=c17b69770f0023154f578eb8c7536a70f07d6a3bb0bd38f04fa0e8811c3c1390

PKG_MAINTAINER:=Florian Wagner <florian@wagner-flo.de>

PKG_BUILD_DEPENDS:= \
  libtirpc \
  libudev-zero \
  util-linux \
  zlib

include $(INCLUDE_DIR)/package.mk

define Package/zfs/Default
  URL:=https://zfsonlinux.org
endef

define Package/zfs-utils
  $(call Package/zfs/Default)
  TITLE:=ZFS Userspace Utilities
  SUBMENU:=Filesystem
  SECTION:=utils
  CATEGORY:=Utilities
  DEPENDS+= \
    +libblkid \
    +libopenssl \
    +libtirpc \
    +libudev \
    +libuuid \
    +zlib
endef

define Package/zfs-utils/description
ZFS is an advanced file system and volume manager which was
originally developed for Solaris and is now maintained by the
OpenZFS community.

This package contains the userspace utilities (zpool, zfs, ...).
endef

define Package/zfs-utils/conffiles
/etc/hostid
endef

define KernelPackage/fs-zfs
  $(call Package/zfs/Default)
  TITLE:=ZFS
  SUBMENU:=Filesystems
  DEPENDS+=+zfs-utils
  AUTOLOAD:=$(call AutoLoad,99,spl zfs)
  FILES:= \
    $(PKG_BUILD_DIR)/module/zfs.ko \
    $(PKG_BUILD_DIR)/module/spl.ko
endef

define KernelPackage/fs-zfs/description
ZFS is an advanced file system and volume manager which was
originally developed for Solaris and is now maintained by the
OpenZFS community.

This package contains the required kernel modules.
endef

define KernelPackage/fs-zfs/conffiles
/etc/modules.d/99-zfs
endef

MAKE_FLAGS += \
  $(KERNEL_MAKE_FLAGS)

CONFIGURE_ARGS += \
  KERNEL_ARCH=$(LINUX_KARCH) \
  KERNEL_CROSS_COMPILE=$(TARGET_CROSS) \
  --disable-pyzfs \
  --disable-systemd \
  --disable-sysvinit \
  --without-libunwind \
  --with-linux=$(LINUX_DIR)

define Package/zfs-utils/install
	$(INSTALL_DIR) $(1)/usr/lib $(1)/usr/bin $(1)/etc/init.d
	$(CP) $(PKG_BUILD_DIR)/.libs/*.so* $(1)/usr/lib/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/zgenhostid $(1)/usr/bin/
	for i in zfs zpool zstream zfs_ids_to_path \
	         zed zdb mount.zfs ; do \
	  $(INSTALL_BIN) $(PKG_BUILD_DIR)/.libs/$$$$i $(1)/usr/bin/ ; \
	done
	$(INSTALL_BIN) ./files/zfs.init $(1)/etc/init.d/zfs
endef

$(eval $(call BuildPackage,zfs-utils))
$(eval $(call KernelPackage,fs-zfs))
