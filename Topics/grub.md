GRUB2
alap konfigok:
  * /etc/default/grub - konstansok
  * /etc/grub.d       - snippetek az éles konfig összeállításához

#grub2-mkcoonfig készíti a véglegest:
grub2-mkconfig -o /boot/grub2/grub.cfg 
#Almalinux 8-ban NINCS update-grub

BIOS esetén
/boot alól kernel betöltés

UEFI esetén
/boot/efi alatt van a loader kód
/boot/efi/EFI/redhat/grub.efi fájlban a konfig

bootloader után initrd kicsiomagolás és mount >> /sysroot
a szükségem modulok betöltése (diszk és miegymás) után igazi root mountolása rw és pivot_root 

ezután indul a systemd 1-es PID-del

## boot hibaelhárítás

linux sor végére:   rd.break   >> rescue módban megáll, a valódi root /sysroot néven RO van mountolva
módosítás esetén: touch /.autorelabel     (van gyorsabb is, restorecon /etc/shadow,  meg még valami)
linux sor végére:   systemd.target=multi-user.target    >> multi userbe bootol, akármi is van beállítva


# turbo megoldás:
boot command line edit (a linux sor végére): systemd.unit=multi-user.target rd.break enforcing=0

passwd módosítás: remount rw - chroot - passwd - exit - remount  ro - exit

boot után context helyreállítás és enforce visszakapcs: restorecon /etc/shadow ;  setenforce 1
# ez így valamiért csinált magától egy autorelabeling-et is...

selinux=1 enforcing=0 init=/bin/bash systemd.unit=multi-user
# így végül sikerült egy menetben az egész. Boot után permissive módban van a rendszer, vissza 
# kell állítani enforcing módba



