## swap kezelés

mkswap /dev/device    -- lehet partíció, diszk, LV
                      -- lehet hozzá adni UUID-et vagy LABEL-t

swapon      -- lekérdezi a bekapcsolt swap(ok) állapotát

swapon  /dev/device   -- swap bekapcsolása
swapoff /dev/device   -- swap kikapcsolása

#boot-kor bekapcsolandó SWAP az fstab-ba kerüljön, kb mint egy fs: 6 mező
1: dev|LABEL=label|UUID=uuid
2: none  (mert ugye nincs mount. "swap" is lehet)
3: swap  (ez az fs_type)
4: defaults  vagy "prio=XXX"  nagyobb szám, nagyobb prioritás. Egyenlő számok esetén round-robin
5: kell_fs_dump (mindig 0)
6: fsck_order   (mindig 0)

swap -a    -- összes fstab-ban definiált swap felhúzása (és ezzel fstab syntax check)

mem állapot lekérdezés még: 
free
