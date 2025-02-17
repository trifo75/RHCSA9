## fs kezelés

mkfs -t ext4  /dev/devicefile     -- ext4 FS létrehozása
        xfs|vfat                  -- ugyanaz xfs, vagy vfat

dev lehet disk, partíció, LV, VDO volume, stratis

# boot-kor mounthoz /etc/fstab-ba kell írni: 6 mező
1: dev|LABEL=label|UUID=uuid
2: /mountpt
3: fs_type (ext4|xfs|...)
4: mount_opts
5: kell_fs_dump (alapban 0)
6: fsck_order   (alapban 0)


# mount opts
vdo esetén        x-systemd.requires=vdo.service   (vagy _netdev)
stratis esetén    x-systemd.requires=stratisd.service   (vagy _netdev)

!! vdo és stratis esetén a df parancs nem ad vissza jó értéket!

findmnt --verify   -- syntax check az összes automanat mountra (fstab és systemd is)
mount -a           -- mindent mountol, mellesleg sytax check az fstab-ra

tune2fs     -- fs paraméterezés, UUID/LABEL kezelés
xfs_admin

/proc/mounts    -- kb amit a "mount" parancs is ad

Label lekérdezés:   blkid
