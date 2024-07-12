include $(TOPDIR)/rules.mk

PKG_NAME:=iptmon
PKG_VERSION:=0.1.6.1
PKG_RELEASE:=1
PKG_MAINTAINER:=Marcin Stojek <mstojek@gmail.com>


include $(INCLUDE_DIR)/package.mk

define Package/iptmon
	SECTION:=net
	CATEGORY:=Network
	TITLE:=simple iptables-based network bandwidth monitor
	DEPENDS:=+luci-app-statistics +collectd-mod-iptables +dnsmasq
	MAINTAINER:=$(PKG_MAINTAINER)
	URL:=https://github.com/mstojek/iptmon
	PKGARCH:=all
endef

define Package/iptmon/description
	iptmon is a script used to create and update 
	iptables firewall rules to count transmit 
	and recieve traffic to/from each host.
endef

define Package/iptmon/conffiles
	/etc/collectd/conf.d/iptables.conf
endef

define Build/Compile
endef

define Package/iptmon/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/usr/sbin/iptmon $(1)/usr/sbin/iptmon
	$(INSTALL_DIR) $(1)/etc/collectd/conf.d
	$(INSTALL_CONF) $(PKG_BUILD_DIR)/etc/collectd/conf.d/iptables.conf $(1)/etc/collectd/conf.d/iptables.conf
endef

define Package/iptmon/postinst
	#!/bin/sh
	# check if we are on real system
	if [ -z "$${IPKG_INSTROOT}" ]; then
		## dnsmasq configuration
		uci set dhcp.@dnsmasq[0].dhcpscript=/usr/sbin/iptmon
		uci commit
		/etc/init.d/dnsmasq restart
		## firewall configuration
		echo '/usr/sbin/iptmon init' >> /etc/firewall.user
		/etc/init.d/firewall restart
		## luci_statistics/collectd configuration
		uci set luci_statistics.collectd.Include='/etc/collectd/conf.d'
		uci commit
		/etc/init.d/luci_statistics restart
		rm -rf /tmp/luci-modulecache/
		## rc.local configuration (needed for Openwrt 21.0)
		####echo '/usr/sbin/iptmon init' >> /etc/rc.local
		echo '/usr/sbin/iptmon init' >> /etc/rc.local
	fi
	exit 0
endef

define Package/iptmon/postrm
	#!/bin/sh
	# check if we are on real system
	if [ -z "$${IPKG_INSTROOT}" ]; then
		#!/bin/sh
		uci set dhcp.@dnsmasq[0].dhcpscript=''
		uci commit
		/etc/init.d/dnsmasq restart
		sed -i 's@/usr/sbin/iptmon init@@g' /etc/firewall.user
		/etc/init.d/firewall restart
		/etc/init.d/luci_statistics restart
		rm -rf /tmp/luci-modulecache/
	fi
	exit 0
endef

$(eval $(call BuildPackage,iptmon))



