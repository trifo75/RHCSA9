# Getting exact time from remote servers

The time sync is done via NTP protocol. In place of the old fashioned `ntpd` we have lighter, but fully functional `chronyd`. It's main feature to slowly adjust time and keep near to the time provided by remote time servers.

Most probably `chronyd` is enabled in systemd by default. 

## Configuring chronyd
The main config file is `/etc/chrony.conf`:

Config elements to care about:
- `pool 2.rhel.pool.ntp.org` - a server pool to choose time source from
- `server my_timeserver.example.com` - a single time source 
These two can coexist. You just have to be sure that at least 1 server is accessible. (there is a minimum number of sources in the config `minsources`, but that can be 1)

If you want to provide time to additional servers, you shoud have these setting too:
- `stratum 8` - the "exactness" of time: hardware atomic clocks and the like have `stratum 1` and theyr clients get smaller and smaller values. You can get time from a server with smaller stratum. Stratum 10 and above considered unreliable time.
- `allow 192.168.0.0/16` - the network domain from which clients are accepted

To serve time, you should also allow inbound `ntp` on the [firewall](firewall.md) 

After changing reread it: `systemctl restart chronyd`

On the client host add this server to the chrony.conf as `server my_new_timeserver.example.conf` and restart chronyd.

## Manage chronyd

Chronyd has an interactive client: `chronyc`. It has internal subcommands
- `chronyc sources` - list and status of configured time servers (pool and server lines)
- `chronyc tracking` - status of time synchronization
- `chronyc makestep ...` - jump to server time

## timedatectl and Setting time zone

`timedatectl` is an universal tool to manage time and timezone related stuff.
- set / query time and timezone
- set / query timesync status 
- list timezones

To have local time you have to set time zone. There are 3 ways:
- `timedatectl set-timezone Europe/Budapest` 
- `tzselect` interactive tool 
- symlinking timezone info file from /usr/share/zoneinfo/... to `/etc/localtime`

`timedatectl show` to display TZ info, NTP sync status and many more

## Setting time

There is *system time* kept by running kernel and *hardware clock* which keeps time when the machine is off.

Set local time with `date` command like:
`date -s 16:04` - sets time to 16:04PM
`date --date '@12345678'` - converts epoch tumestamp to human readable form

`hwclock --systohc` - sets hardware clock according to system time
`hwclock --hctosys` - sety system time by hardware clock

