## getting info on swap

Normaly there is at least 1 swap partition in the system. You can check it with `swapon` without options. 

```
[root@server2 ~]# swapon
NAME      TYPE      SIZE USED PRIO
/dev/dm-1 partition 2.1G   0B   -2
```

## creating swap
It is similar to creating a file system:  
`mkswap /dev/device` -- where device can be partition, disk, LV partíció, diszk, LV  

You can specify a `-L` *LABEL* and/ot `-U` *UUID* to be used in fstab for convenience.

## turning swap on and off
swapon  /dev/device   -- enable the partition - also can be referred as UUID or LABEL  
swapoff /dev/device   -- disable (turn off) swap partition

You can have more than one swaps enabled at a time, they can be handled independently from each other. 

## enabling swap automatically on boot
You can turn on swap spaces at boot just like mount normal FS-es: 6 fields in `fstab`:
```
/dev/mapper/rhel-swap   none                    swap    defaults        0 0
```
The *mount point* field van be `none` or `swap`, the *FS type* field is `swap` and the rest is `defaullt 0 0`  
The device can be specified - like normal FS-es - as device, UUID or LABEL.

1: dev|LABEL=label|UUID=uuid  
2: none  (Because there is no mount. Can be "swap" as well)  
3: swap  (this is fs_type) 
4: defaults  or "prio=XXX"  bigger number, higher priority. On equal numbers, round-robin  
5: fs_dump_needed (always 0)  
6: fsck_order   (always 0) 

To turn on all the swaps defined in fstab, use `swapon -a`

## checking memory states

`free`
