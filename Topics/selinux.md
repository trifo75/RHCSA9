# SELinux

**policy** - The set of rules to be enfoced  

Modes of operation:
* *enforcing*  --   this is the normal mode, the rules in policy are forced, violations are prohibited and reported
* *permissive* --   in this mode policy rules are checked and violations are reported. Good for troubleshooting. DO NOT LEAVE SYSTEM IN THIS MODE
* *disabled*   --   not enforcing and not logging policy violations, but the system itself is active in the kernel and loaded. If you need to switch off SELinux completely, use `selinux=0` on kernel cmdline (and/or `enforcing=0`). 

## seting modes
SELinux config file is `/etc/sysconfig/selinux`. Two main option is:  
`SELINUX=mode` - where mode can be enforcing/permissive/disabled  
`SELINUXTYPE=type` - where type can be targeted/minimum/mls

Also there are kernel cmdline options:  
`selinux=0` - do not even load selinux into kernel  
`enforcing=0` - set permissive mode during boot - good for root password recovery  
You can use `grubby` to set kernel cmdline options permanently, or edit cmdline during boot in GRUB phase.

In the running system you have these commands  
`getenforce` -- query operating mode (enforcing/permissive/disabled)  
`setenforce` -- set mode temporarily (Enforcing/Permissive)  
`sestatus -v` -- get detailed SELinux status info

`sepolicy generate`   -- build policy files for not yet existing config 

## logging
Main log is `/var/log/audit/audit.log` check lines starting with `type=AVC`

There are also entries about policy violations in `/var/log/messages`

Extra info can be obtained using `sealert` - you have to install `setroubleshoot-server` which will monitor policy violations and give detailed info, along with repair advices. You will find lines like below in `/var/logmessages`:  
```
Dec  8 08:39:13 localhost setroubleshoot[7802]: SELinux is preventing /usr/bin/dash from read access on the file /usr/lib/x86_64-linux-gnu/libc.so.6. For complete SELinux messages run: sealert -l 9e61dd45-1ffa-4a53-a265-82cfa096a81c
```  

Every policy violation will get a UUID and you can use `sealert -l 9e61dd45-1ffa-4a53-a265-82cfa096a81c` to show specific advices and info. (The command and UUID are from the log line)

## managing file context
Every file has a secirity label which can be queryed using `ls -Z file`. (Also a lot of commands support `-Z` to handle labels)
```
[root@lab-repo audit]# ls -dZ /var
system_u:object_r:var_t:s0 /var
```
It is like `system_u` user, `object_r` role and `var_t` type. Now, we concentrate on *types*.

File context is mainly stored in the policy and are applied to files by *autorelabel* process, *restorecon* command, or file creation (inheritance)

Auto relabeling occurs during boot, if the system finds a hidden file called `.autorelabel` in the FS root. In this case SELinux labels are restored to all files in the FS from the policy. There mingh be some circumstances when the system initiates autorelables by itself, that's why using `chcon` is heavily discouraged. 

`restorecon` command can be used to apply labels to individual files according to the policy settings.

Creating new files also creates new labels on them, labels are inherited from the directory containing the new file. Copying a file sets the label according to the target dir. *Warning*: moving a file keeps it's original labels which might cause problems (like moving files into web content dir - the webserver will be prohibited to serve them.)

## setting file context
You should first set labels in the policy using `semanage fcontext` and then apply them to the files using `restorecon`. You can find good examples in `man semanage-fcontext` man page.

`semanage fcontext -a -t <file context> "/mydir(/.*)?"` 
`-a` is for *add*  
`-t` is for *type* - you can check on other files
the end part is a regex to select files to apply rule to. In this case *"/mydir" and everything below*

When the policy is ready, you can apply the rules to the files like  
`restorecon -vR /mydir`  
`-v` is for *verbose*, every file is listed along with the applied label  
`-R` is for *recursive*  

## fcontext example: create non-standard webroot directory
Preparation: look up the type to set. Check the label on the original webroot directory (most probably /var/www/html):  
```
[root@server2 ~]# ls -ldZ /var/www/html
drwxr-xr-x. 2 root root system_u:object_r:httpd_sys_content_t:s0 6 Mar 29  2023 /var/www/html
```
So you have to use *httpd_sys_content_t* type. Now create a new target directory and apply the label
```
[root@server2 ~]# mkdir /mywebroot
[root@server2 ~]# semanage fcontext -a -t httpd_sys_content_t "/mywebroot(/.*)?"
[root@server2 ~]# restorecon -vR /mywebroot
Relabeled /mywebroot from unconfined_u:object_r:default_t:s0 to unconfined_u:object_r:httpd_sys_content_t:s0
```
Now you Apache httpd can use this directory to serve content from. Use this snippet in the *httpd.conf* 
```
DocumentRoot "/mywebroot"
<Directory "/mywebroot">
    AllowOverride None
    Require all granted       # give read permission
</Directory>
```
and then reload httpd config.

## managing selinux ports
SELinux also applies security labels on network ports. The current settings can be listed using `semanage port -l`. If you want to run your service on non-standard ports, you wil need to label that port with the appropriate type, which can be get from the port list. 

**QUESTION** -- is this for privileged ports? Why not applied to containers? Can I run httpd on an arbitrary high port without labeling?

## port example: setting non-standard port for Apache httpd
Let's configure Apache to serve on port 87. Edit *httpd.conf* to include `Linten 87` directive. 

Look up the port type to be set (port 80):
```
[root@server2 ~]# semanage port --list | grep -w 80
http_port_t                    tcp      80, 81, 443, 488, 8008, 8009, 8443, 9000
```

Apply the appropriate label to the target port:  
`semanage port --add --type http_port_t --proto tcp 87`  
This happens immediately, no need to restore, like fcontext. 

Now reload httpd config (graceful). The server will be able to bind to the new port. 

Finally set firewall rules:  
`firewall-cmd --service=http --add-port tcp/87 --permanent`  
`firewall-cmd --reload`

Now `ss` or `netstat` commands show 

## SELinux boolean settings
There are boolean switches which can have effect on many rules in the policy. You can list them using
* `semanage boolean -l` including default (policy) value, current value and a short description
* `getsebool -a` for just the options and theyr current value

Setting values:
* `setsebool param on/off` setting value temporarily. For permanent change use `-P` switch as well. 
* `semanage boolean --modify ...` permanent change in the policy
