# Basic kernel management

## kernel threads

*kernel threads* show up in `ps aux` list just like processes, having theyr own PID. Differences:
* they have sqare brackets around the name, like `[jbd2/dm-10-8]`
* they can *not* be killed or reniced


## kernel messages

Kernel messages can be read from various sources:
* `dmesg` - kernel ring buffer - the messages begin with boot, but are overwritten when the buffer fills
* `journalctl --dmesg` or `journalctl -k` (they are the same)

## birth of device 

System devices are handled by *udevd* - discovers hardware stuff and creates representation devices under /dev

On systemd enabled OS-es, like RHEL - this is integrated into systemd: `systemd-udevd`. It creates devices
according to rules under 
* `/usr/lib/udev/rules.d` - default rules
* `/etc/udev/rules.d` - for custom rules

`udevadm monitor` can be used to see messages between kernel and udevd

## kernel module management
* `lsmod` -- show loaded modules
* `modprobe <module>` -- load module along with its *dependencies* - `insmod` should be avoided because doesn't handle dependencies
* `modprobe -r <module> -- unload a module - and unused dependencies - `rmmod` should be avoided
* `modinfo <module>` -- display additional info, parameters, options, etc. 

Configuration files can be found in
* `/usr/lib/modules-load.d` -- system default options for modules
* `/etc/modules-load.d` -- setting custom parameters

# Introducing new kernel
You can NOT update a kernel. You do install a new kernel, keeping the old one intact. The system default is to keep 4 versions.
If there is a fresh kernel, use `dnf install kernel` or `dnf update kernel`. They are equivalent. 

New kernel and initramfs goes to `/boot` and the kernels are automatically are listed in GRUB boot menu - by `grubby` run at kernel installation. 

# Repairing damaged initramfs
When the *initramfs* image gets broken, you can repair it using `dracut` command. 
* `dracut` -- called without paramters, it packages the current running kernel an its loaded modules. Does *not* owerwrite the file in /boot
* `dracut --force` -- forces the overwrite of existing initramfs file
* `lsinitrd` -- can be used to see initrd contents

# törött initramfs helyreállítása:
dracut    -- így param nélkül az akuális futó kernelt és moduljait csomagolja - de csak a --force kapcsolóval írja felül a jelenlegit 
lsinitrd  -- listázza az élő initrd fájlt 

# linux kernel boot parameters
You can set some parameters in the `/etc/default/grub` file. 
In the `GRUB_CMDLINE_LINUX` option you should remove the `rhgb` and `quiet` options, as they hide boot messages which might be
useful during the exam for debugging boot process
* `rhgb` -- RedHat graphical boot - Plymouth boot splash screen
* `quiet` -- hide lot of bootup messages for a clean boot experience
* `rd.lvm.lv` -- activate a logical volume during boot, mostly root fs in LVM based installs - can be multiple

`man dracut.cmdline` and man `kernel-cmdline` for more info


