# SPDX-License-Identifier: GPL-2.0-only
#
# Copyright (C) 2016-2025 Entware

include $(TOPDIR)/rules.mk

PKG_NAME:=go-src
PKG_VERSION:=1.25.5
PKG_RELEASE:=1

PKG_SOURCE:=go$(PKG_VERSION).src.tar.gz
PKG_SOURCE_URL:=\
		https://dl.google.com/go/ \
		https://mirrors.ustc.edu.cn/golang/ \
		https://mirrors.nju.edu.cn/golang/
PKG_HASH:=22a5fd0a91efcd28a1b0537106b9959b2804b61f59c3758b51e8e5429c1a954f

HOST_BUILD_DIR:=$(STAGING_DIR_HOST)/go

# broken: make -j$(nproc): `bash: line 1: Entering: command not found`
#BOOTSTRAP_VERSION:= \
#	$(shell make --no-print-directory -C ../go-bootstrap/ val.BOOTSTRAP_VERSION V=s)
BOOTSTRAP_VERSION:= \
	$(shell grep '^PKG_VERSION' ../go-bootstrap/Makefile | cut -b14-17 )

include $(INCLUDE_DIR)/host-build.mk

GO_BINS:=$(HOST_BUILD_DIR)$(BOOTSTRAP_VERSION)/bin/go

export GOENV=$(STAGING_DIR_HOST)/etc/env

define Host/Configure
	$(GO_BINS) env -w GOCACHE="$(TMP_DIR)/go-build"
	$(GO_BINS) env -w GOMODCACHE="$(DL_DIR)/go-mod-cache"
	$(GO_BINS) env -w GOPATH="$(DL_DIR)/go-mod-cache"
	$(GO_BINS) env -w GOTMPDIR="$(TMP_DIR)"
endef

define Host/Compile
	( cd $(HOST_BUILD_DIR)/src; \
		GOTELEMETRY=off \
		CC="$(HOSTCC_NOCACHE)" \
		CXX="$(HOSTCXX_NOCACHE)" \
		GOROOT_BOOTSTRAP=$(HOST_BUILD_DIR)$(BOOTSTRAP_VERSION) \
		./make.bash; \
	)
	( cd $(STAGING_DIR_HOST)/bin; \
		for bins in go gofmt; do ln -nsf ../go/bin/$$$$bins $$$$bins; done \
	)
	$(STAGING_DIR_HOST)/bin/go telemetry off
endef

define Host/Install
endef

define Host/Clean
	rm -f $(STAGING_DIR_HOST)/bin/go{,fmt}
	rm -rf $(HOST_BUILD_DIR)
endef

$(eval $(call HostBuild))
