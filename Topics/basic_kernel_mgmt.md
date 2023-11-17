## Basic kernel management

ps aux   -- show kernel threads too

dmesg   vagy    journalctl -k    vagy    journalctl --dmesg      -- kernel ring buffer - bot óta keletkezett log


systemd-udevd   -- device file-ok automata kezelése
                   ruleset: /usr/lib/udev/rules.d  ÉS /etc/udev/rules.d (custom rules)

udevadm monior   -- udev - kernel közti üzenetváltások megjelenítése

#kernel modulok kezelése:
lsmod   -- betöltött modulok listája - benne a dependenciák is 
modprobe     -- modul betöltése függőségekkel együtt -> insmod kerülendő
modprobe -r  -- betöltött modul eltávolítása mindenestül   --> rmmod kerülendő
modinfo      -- info az adott modulról, pl paraméterek 
         ==>   /etc/modules-load.d         mindig beállítandó custom paraméterekhez
         ==>   /usr/lib/modules-load.d     rendszer által beállított paraméterek  -- valami.conf

lspci     -- pci busz device listázása
lspci -k  -- az eszközökhöz a kernel drivereket is mutatja

# kernel frissítés: csak kernel telepítés van, a legutóbbi 4 verziót tartja a rendszer
  -> a GRUB2 automatokusan csinál boot entry-t belőlük
dnf install kernel   --ezek ugyanazok
dnf update kernel

# törött initramfs helyreállítása:
dracut    -- így param nélkül az akuális futó kernelt és moduljait csomagolja - de csak a --force kapcsolóval írja felül a jelenlegit 
lsinitrd  -- listázza az élő initrd fájlt 

# linux boot paraméterek:
rhgb  -- RedHat graphical boot (splash és spinner)
quiet -- elrejt egy csomó boot üzenetet
rd.lvm.lv   -- aktiválandó logical volume, több is lehet (dracut paraméter)



