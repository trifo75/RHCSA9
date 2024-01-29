# using systemd  

**systemd** replaces the old SystemV init process - and does much more. Manages loading services, mounting filesystems and scheduling tasks and so on. The basic building blocks are **unit files**, which defines tasks to do and the order of them. systemd works in a parallel manner, which makes booting way faster.

## unit files

File locations:
* **/usr/lib/systemd/system** - factory default units
* **/etc/systemd/system** - custom system unit files (for root)
* **(user homedir)/.config/systemd/user** - custom units for nonprivileged users - needs *linger* option to start automatically

In the /etc/... you can override files from /usr/lib/...

You can use directory /etc/systemd/system/**some_unit.d** to override *just parts* from the unit file of the same name.

Socket unit type: **some_unit.socket** is the communication socket defined for the *service* unit of the same name.

## targets

They are the unit files with extension **.target**. Are similar to *runlevels*. They are a collection of units which need to run to reach a specific state of the system. Some can be triggered with the **systemctl isolate** command

* **systemctl -t target** - list all targets
* **systemctl get-default** - show the default system state. Used to be *multi-user* or *graphical*
* **systemctl set-default** - set the default system state.
* **systemctl isolate multi-user**   -- switch to another target like *graphical* vs *multi-user*. Only of **AllowIsolate=yes** is set in the unit file

## management commands

* **systemctl** - lists all *loaded* unit files
* **systemctl --all** - lists all units from everywhere (for root)
* **systemctl --failed** - lists units with *failed* status
* **systemctl list-dependencies  [service name]** - structured list of the serviced (full tree or just a specific service)
* **systemctl status [service]**  - service status info. Shows
   * actual unit file location
   * link to documentation
   * is running or not
   * PID
   * etc...

(**syslog** is just a link for **rsyslog**. If you disable, you can not enable on this name)

## controlling units

all entries below are used a *systemctl COMMAND UNIT*
* **enable** - creates a symlink into the *systemd target* directory, which is specified in the *WantedBy* parameter of the *install* section. Therefore the unit will be running before the target is reached.
* **disable** - deletes the symlink, so the unit will not run automatically. Unless it is defined as a requirement in another unit file 
* **is-enabled** - query if the unit is enabled
* **is-disabled** - query if the unit is disabled
* **is-active** - query if the unit is in running state (?)
* **mask** / **unmask** - if you mask an unit it will be fully hidden from other units, so they won't run, even if defined as requirement
* **start** / **stop** - start and stop an unit manually

## handling limits

When **systemd** starts a process/service, there is no proper login procedure thus */etc/security/limits.conf* is not respected. Systemd itself has default values to assign or you can set limits of your own.

System-wide limits to be used by systemd are in cat **/etc/systemd/system.conf** by the name of **DefaultLimitSOMETHING**. By default they are not set.

Per service limits can be defined in the unit file of the sevice. In the **\[service\]** stanza, you can use the following limit params (with matching ulimit params listed):
```
+------------------+-----------+--------------------------+------+
| systemd          | ulimit    | Description              | Used |
+------------------+-----------+--------------------------+------+
| LimitCORE=       | ulimit -c | core files size          | Yes  |
| LimitDATA=       | ulimit -d | data segment size        | No   |
| LimitNICE=       | ulimit -e | niceness level           | Yes  |
| LimitFSIZE=      | ulimit -f | file size                | No   |
| LimitSIGPENDING= | ulimit -i | queued signals           | Yes  |
| LimitMEMLOCK=    | ulimit -l | locked memory size       | Yes  |
| LimitRSS=        | ulimit -m | resident set size        | No   |
| LimitNOFILE=     | ulimit -n | open file descriptors    | No   |
| LimitMSGQUEUE=   | ulimit -q | POSIX message queue size | Yes  |
| LimitRTPRIO=     | ulimit -r | real-time priority       | Yes  |
| LimitRTTIME=     | ulimit -R | runtime before blocking  | Yes  |
| LimitSTACK=      | ulimit -s | stack size               | Yes  |
| LimitCPU=        | ulimit -t | CPU time                 | No   |
| LimitNPROC=      | ulimit -u | available processes      | -    |
| LimitAS=         | ulimit -v | process virtual memory   | No   |
| LimitLOCKS=      | ulimit -x | file lock count          | Yes  |
+------------------+-----------+--------------------------+------+
```
Default size measumeremt unit is *byte* but K, M, G (for Kilo, Mega, Giga) can be used. Similarly normal time measurement units can be used. 






