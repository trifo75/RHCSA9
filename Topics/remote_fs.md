# Mounting remote filesystems - NFS & AutoFS

- RHEL9 default NFS version: 4.2
- supported versions: v3, v4, v4.1 & v4.2
- v2 - no longer supported

-> can be specified with `nfsvers` mount option

## needed software components

For NFS install `nfs-utils` package. Contains client and server too.  
For AutoFS install `autofs` package. This is a special kind of NFS client. 

## NFS server side (not needed for RHCSA exam)

Main config file: `/etc/exports`. Contains directories to share and options/authorizations.  

Example:
`/nfsdata *(rw,no_root_squash)`
path to be shared  
allowed hosts - `*` means all. Hostnames, IP addresses and IP domains can be listed  
and mount options in parentheses. `ro_root_squash` means root id *not* to be remapped to nobody. You should only enable this if you manage server and client as well. 

NFS traffic has to be allowed in the [firewall](./firewall.md) config:
```
firewall-cmd --permanent --add-service nfs
firewall-cmd --permanent --add-service mountd      # for showmount
firewall-cmd --permanent --add-service rpc-bind    # for showmount
firewall-cmd --reload
```

## discovering remote exports
NFS v4 allows *root mount*: jou can mount `nfs_server:/` and there you will see every available exported directory  
In other cases you can use `showmount -e remote_server` command, where *remote_server* is the name of the NFS server to be queryed. *rpc-bind* and *mountd* services need to be enabled on server firevall. 

## mounting NFS manually

`mount nfsserver:/shared/directory ...options... /local/directory`

Like a normal FS mount. FS type can be specified as `nfs` but mount will find out for itself. Options can include for example
- `sync` - disable fs cache, make writes more secure on failure
- `rw / ro` - read-write or read-only
- `nfsvers` - protocol version
- `root_squash / no_root_squash` - enable / disable UID remapping for root user

# Automatic mount fstab alapján

You can put the filesystem mount entry to `/etc/fstab` like
```
#SRC                          DST      TYP    OPTS
192.168.55.47:/export/blah    /blah    nfs    sync      0 0
```

`sync` is optional, but good to have. Earlier RHEL versions needed  `_netdev` option too. Now it is optional. 

*Warning:* default is *hard* mount which means that an unreachable NFS server blocks the boot procedure.


## Automount / AutoFS

autofs service tud automatán mountolni NFS (és SMB) share-eket 
(systemd automount is tud, csak wildcard-ot nem tud, ezért nem szeretjük)

Fő konfig:
/etc/autofs.conf     --> ebben van a master map neve (default: auto.master)

auto.master  - master map - ebben van felsorolva a map konfigok neve

#master map-ban
/-   /etc/auto.direct   - direkt map, ami az auto.direct konfigban van kifejtve (mint egy sima mount)

/mountpt     /etc/auto.mountpt    - indirekt map. A /mountpt alatt hovatkozott könyvtár lesz a KEY, amit mountolni próbál
                                    az auto.mountpt konfig alapján 


# indirekt map konfig
key    server:/path/valami

ha a master map ilyen:   /home    /etc/auto.home  # user home-ok mountolása
akkor az auto.home ilyen:

*      192.168.55.47:/export/home/&
-->   a * a hivatkozott konkrét könyvtár lesz a /home alatt (key), a végén az & pedig megkapja a key értékét. 
      tehát az "ls /home/bill" parancs uptán a key "bill" lesz és a 192.168.55.47:/export/home/bill lesz mountolva a /home/bill helyre

*     -rw    server2:/users/&
-->   lehet a közepére mount opciókat rakni 


/net   -hosts
-->    Az "ls /net/bigyo" hivatkozáskor a /net/bigyo alá a "bigyo" hostról bemountolja az összes elérhető share-t (showmount -e bigyo alapján)



--> LDAP AUTH nem kell???

