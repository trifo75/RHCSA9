# Configuring IP networking

## nmcli documentation and examles
man 5 nm-settings  
man nmcli-examples

## valid if names:
*udevd* is trying to set permanent device names during boot. If it succeeds, the device names will reflect physical connections or similar, like  
`enp0s3` -> ethernet port 0 socket 3   
`wl...`  -> wlan

If *udevd* fails to set permanent naming, it falls back to traditional `eth0` or `p0s3`

## temporary config tool
`ifconfig` is deprecated, not available any more - it can not set some newer options.

`ip` command is the new substitute, can handle everything, but the settings are **not permanent**

`ip addr show` (`ip a`) -- displays all iface with its ip config. Adding `-s` also gives statistics  
`ip link show` (`ip l`) -- link status of every iface 
`ip route show` (`ip r`)   -- display routing table  
For every `ip` commands the default is sub `show`. 

## temporary mods
`ip link dev DEVICENAME set up` -- set link status *UP* for the device

`ip address add...` -- add an extra IP address

`ip` command should be used mostly for querying stats.

## permanent network config
In RHEL9 the network is managed by NetworkManager service (systemd). Config is under `/etc/NetworkManager/...` but should not be edited manually. 

You have to think in "connections" which have a *device* attached on one hand and the *config entries* on the other hand.

Basic tool is `nmcli` which can set everything. Most important is to switch networking *on* if it is not: `nmcli networking on`. Whithout this every device shows as *unmanaged* and cannot be manipulated. 

### setting up a static IP using nmcli
`nmcli conn add type ethernet con-name kapcs1 ifname enp0s8 ip4 192.168.55.72/24 gw4 192.168.55.1`
* mandatory items
   * `type` ethernet
   * `ifname` device_name
* optional items
   * `con-name` name_of_connection
   * `ip4` 192.168.55.71/24 - you have to give the netmask bits as well in CIDR format. The mask defaults to /16 - alias for `ipv4.addresses`
   * `gw4` 192.168.55.1 - alias for `ipv4.gw` 
   * `method` manual - omitting this causes to be *auto*, which gets params from DHCP
   * `ipv4.dns` 8.8.8.8 - sets up `/etc/resolv.conf` - do not edit that file by hand.

This command changes connection state to UP as well.  
On *modify* you have to use the long name. 

### DNS settings
DNS servers are listed in `/etc/resolv.conf`, but that is managed by NetworkManager. DO NOT EDIT BY HAND

Change DNS options for a connection:
`nmcli conn mod <conn_id> [+]ipv4.dns <dns ip>`  
This puts the ip into /etc/resolv.conf.

### Get general permissions list
`nmcli general permissions` - lists all network related permissions for the given user. 

## IPv6
ipv6 loopback:   [::1]  
::  -- null value blocks in the middle of an ipv6 address

If IPv6 is not completely disabled on an interface, there is always a *link-local* auto-generated address. That's ok. 

## Setting up hostname - please use FQDN

Possible solutions:
* `hostrnamectl hostname prod-server-12.example.com`
* `nmcli general hostname prod-server-12.example.com`
* `nmtui` - text based menu driven tool for *everything*

## Name resolution
`/etc/nsswitch.conf`
```
hosts:      files dns myhostname    # "files" directive is needed to use /etc/hosts
                                    # "myhostname" directive is for resolving own  hostname to own IP
```
That's why the hostname is not needed to be listed in /etc/hosts

## Viewing network sockets
`ss` command is kind of `netstat` was earlier.

`ss -l -t -n` -- list Listening Tcp sockets without Name resolution. 










ss : Socket Statistics
  -n   -- don't resolve services (port names)
