## Logical Volume Manager - LVM

pvs          -- PV-k listtája/infók
pvdisplay

vgs          -- VG-k listája
vgdisplay

lvs          -- Logical Volume-ok listája
lvdisplay

lvresize     -- LV méretezés a rajta lévő FS-sel együtt

pvcreate <disk vagy partition> [-v]    -- partition esetén elvileg kell LVM flag, diszk esetén nem.
                                          Valójában nem igényelte. Vagy ez csak "jegyzetnek" kell?
                                          A tejejébe RHEL9 esetén pvcreate nélkül is ment a VG létrehozás (hogy lehet ez?)

vgcreate -s 16 VGneve egyikPV masikPV         -- VG létrehozása 16MB-os PE mérettel



/etc/lvm/backup alá automatikusan készül mentés minden műveletnél 

lvcreate -L valamennyiMB -n lvneve vgneve   -- létrehozás megadott mérettel
lvcreate -l ennyiPE      -n lvneve vgneve   -- létrehozás PE darabszámra

# ha az LVM partició megnőtt, akkor 
    lvmdiskscan -> mutatja az új méretet
    pvresize DEVICE  -> hozzáigazítha az LVM-et az új mérethez


## VDO - virtual disk optimizer --  Már LVM-be van integrálva
     zero block elimination
     UDS - universal de-duplication service
     kvdo  - kernel modul a tárolt adatok tömörítsére

vdo -- parancs VDO kötetek kezelésére (létrehozás, indítás, leállítás, törlés)
vdostats -- statisztikák ugyanarról

vdo create --name vdo-vol1 --device /dev/sdf --vdoLogicalSize 16G --vdoSlabSize 128

    slab: olyan logical partition féle

kötetek lekérdezése:
vdostats --human-readable (vagy csak --hu)
-> látszik, hogy hova tette a mapper

vdo status --name vdo-vol1 
-> adott kötet részletes infói, dedup/compress állapot, hely nyereség tömörítés foka, stb

