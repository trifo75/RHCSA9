# LVM VDO

In RHEL9 VDO functionality became available under LVM, thus you can create thin provisioned LogicalVolumes on LV-backed phisycal pools

## requisite software
dnf install lvm2 kmod-kvdo vdo

## create hosting VG (sdb -> 16GBdisk)
vgcreate -s 16 thinvg /dev/sdb

## create VDO backed LV
lvcreate --type vdo --name thin_lv --size 12G --virtualsize 120G --config 'allocation/vdo_slab_size_mb=128' thinvg/my_vdo_pool
VDO slab size set to 128MB (minimum value)
Thin ratio (virtual/physical) set to 10
pool name set to my_vdo_pool (if not present, poollv0 is created)

## Format thin volume to XFS
mkfs -t xfs -K /dev/thinvg/thin_lv

   -K is needed - sparse block handling - without this formatting might take loooong time

## Format thin volume to EXT4 - same apply here
mkfs -t ext4 -E nodiscard /dev/thinvg/thin_lv 

## Check status of VDO compression and VDO deduplication (enabled/disabled)
lvs -o+vdo_compression,vdo_deduplication

## Check state of compression and index (online/offline)
lvs -o+vdo_compression_state,vdo_index_state

## turn compression and deduplication on and off for a VDO pool
lvchange --compression y|n  vg-name/vdopoolname
lvchange --deduplication y|n vg-name/vdopoolname