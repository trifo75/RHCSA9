# LVM VDO

In RHEL9 VDO functionality became available under LVM, thus you can create thin provisioned LogicalVolumes on LV-backed phisycal pools

## requisite software
`dnf install lvm2 kmod-kvdo vdo`

## create hosting VG (sdb -> 16GBdisk)
Just any normal VG will do with some free space, nothing special is needed.
`vgcreate -s 16 thinvg /dev/sdb`

## create VDO backed LV
`lvcreate --type vdo --name thin_lv --size 12G --virtualsize 120G --config 'allocation/vdo_slab_size_mb=128' thinvg/my_vdo_pool`  
VDO slab size set to 128MB (minimum value) using `--config`  
Thin ratio (virtual/physical) set to 10 - the ratio between  *size* and *virtualsize*   
pool name set to my_vdo_pool (if not present, poollv0 is created)

## Creating filesystem on the thin LV
Formatting to xfs:  XFS
`mkfs -t xfs -K /dev/thinvg/thin_lv`  
   -K is needed - sparse block handling - without this formatting might take loooong time

Formatting thin volume to EXT4 - same apply here  
`mkfs -t ext4 -E nodiscard /dev/thinvg/thin_lv` 

## Check status flags of VDO backed LV
`lvs` command can query data from thin LV, like  
`lvs -o+vdo_compression,vdo_deduplication` to see if compressio/deduplication are enabled

`lvs -o+vdo_compression_state,vdo_index_state` to see if indexing service is working

Also `vdostats` command can query detailes stats.

## turn compression and deduplication on and off for a VDO pool
`lvchange --compression y|n  vg-name/vdopoolname` turn compression on/off  
`lvchange --deduplication y|n vg-name/vdopoolname` turn deduplicatio on/off