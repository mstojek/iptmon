# iptmon: Simple iptables bandwidth monitor

`iptmon` makes several assumptions:
* a file called `/tmp/dhcp.leases` stores DHCP lease information
* you are using `luci-app-statistics` to collect and process metrics


## Installation
Copy files from host:
```
$ scp etc/collectd/conf.d/iptables.conf ${OPENWRT_HOST}:/etc/collectd/conf.d/
$ scp usr/sbin/iptmon ${OPENWRT_HOST}:/usr/sbin/
```

### on OpenWRT

Generate firewall rules:
```
# iptmon init
```

Set include dir (should be the default but just in case):
```
# mkdir -p /etc/collectd/conf.d
# uci set luci_statistics.collectd.Include='/etc/collectd/conf.d'
# uci commit
# service luci_statistics restart
```

Cron job to periodically flush and re-populate firewall rules:
```
*/30 * * * * /usr/sbin/iptmon update
```

## removal
To uninstall, run `iptmon remove`. This will restore your `mangle` table to the state it was in before you ran `iptmon init`.