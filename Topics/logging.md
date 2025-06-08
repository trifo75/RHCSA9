## rsyslog
The replacement service of traditional **syslog**

conf:  
  `/etc/rsyslog.conf` and `/etc/rsyslog.d`

mostly writes logs under `/var/log/...`

modules (*im...* - input module and *om...* - output module):  
  `imjournal`  -- input module journalctl  
  `imuxsock`    -- input module unix socket (traditional syslog)  
  `omfile`     -- output module FILE  
  `omusrmsg`   -- output module user message - message to everyone  

input selection (fac.sev pairs, delimited with semicolons):  
  `facility.severity;facility.severity...`

There are also modules for parsing, modification, string generation and so on

## systemd.journald
`journald` collects system logs. Can be queryed using `journalctl` command

config: `/etc/systemd/journald.conf`   
*  storage: auto  -- if the permanent storage dir - `/var/log/journal` exists, writes logs there too. There is a monthly logrotate and max filesize.
* default in-memory log storage: `/run/.../journal` 

`journalctl -o verbose` -- displays ALL fields from log. You can apply filters, like

`journalctl _SYSTEMD.UNIT=sshd.service` -- entries from *sshd* only

### For unprivileged users
By default a normal user can not use `journalctl` to view logs. 
If you want them enable, then  you can
* add user to the `systemd-journal` or `adm` groups. This way they can read *all* logs.
* use `journalctl --user-unit SERVICENAME` - jopurnal storage option has to be **persistent**
* on older systemd versions use `journalctl --user --user-unit=SERVICENAME` 

**QUESTION** dmesg ??? Who's reading /var/log/boot.log file?

*anacron* should run logrotate daily (according to `/etc/logrotate.conf` and `/etc/logrotate.conf.d`)
This rotates logs under `/var/log`
