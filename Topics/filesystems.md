# Managing filesystems

`mkfs -t ext4 /dev/devicefile` -- create *ext4* filesystem structures on the selected device (format).
Other filesystem types can be used too, like `xfs` or `vfat`

## Automountig FS at boot

Put an entry into `/etc/fstab` with 6 felds:
1: device file name or `LABEL=..label..` or `UUID=..device uuid..`
2: /mountpoint
3: fs type like: ext4, xfs, ...  reflecting the FS structure of the device
4: mount options
5: Is fs dump needed? Default is `0`, meaning *no dump needed*
6: FSCK order - default is `0`

## Mount options

* When using **VDO** device, `x-systemd.requires=vdo.service` is needed az mount option (or _netdev) 
* When using **stratis** device, `x-systemd.requires=stratisd.service` is needed az mount option (or _netdev)

*Warning:* for **vdo** and **stratis** devices the `df` command reports false values. Use the appropriate fs command. 

## Getting info

* `findmnt --verify` - run syntax check for all the automated mounts (fstab and systemd too)
* `mount -a` - tries to mount all fs-es configured to automount. Fails on syntax errors.
* `/proc/mounts` file contains info about mounted filesystems
* `blkid` command can query labels

## Filesystem params 

* `tune2fs` - adjusting `ext2`, `ext3` or `ext4` filesystems, set/get LABELs, UUIDs and so on
* `xfs_admin` - adjusting `xfs` filesystems
