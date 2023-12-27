# partitioning

## partition table formats

**MBR**: Master Boot Record. Legacy stuff. 4 primary, 11 logical (occupying 1 primary) - can handle maximum 14 partitions. Has size limitations: can not handle disk space above 2TB   
**GPT**: GUID partition table. Can handle 128 partitions, extended disks above 2TB. Default block size: 4k

## partitioning tools
### parted - MBR és GPT is
First you have to put a *label* on the disk. This defines the partition table version: MBR os GPT
* `parted /dev/sdb mklabel msdos` - create new MBR partition table
* `parted /dev/sdb mklabel gpt` - create new GPT partition table
* `parted /dev/sdb mkpart primary 1 101` - create MBR primary partition spaning from 1MB to 101MB (using default unit)
* `parted /dev/sdb rm 1` - delete partition 1

### gdisk - GPT only
For GPT `gdisk` is better to use. Can be feed with command line params or use its menu. When creating GPT partition table it creates a placeholder for *protective MBR* structure thus preventing MBR only tools to ruin GPT disk.  
All editing is done in memory, you have to write changes to disk before exiting.

`gdisk /dev/sdc` - enter menu to handle /dev/sdc
* `?` - print help
* `o` - create new GPT table (with protective MBR)
* `n` - create new partition. Asks for input: start, end/size, FS type (defaults to 8300: Linux fs) 
* `w` - write changes to disk and exit

