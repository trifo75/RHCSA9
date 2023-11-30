# users and groups

User database:
* `/etc/passwd`  -- basic paramters of all users
* `/etc/group`  -- basic list of groups
* `/etc/shadow`  -- password hash storage
* `/etc/gshadow`  -- password hashes for group change

## create / modify users
* `useradd`  (adduser seems deprecated)
* `usermod`
* `userdel`

**UID ranges**  
0            root
1-200        system users, RedHat saját daemonjai  
201-999      system users, unprivileged, dynamic pool  
1000+        normal users  

These ranges can be set in `/etc/login.defs`

To ctreate *system* user or group, use `-r` ( `--system` ) switch  
`groupadd ... -r`  
`useradd ... -r`  

## Add multiple users at once
newusers command takes input from STDIN with these fields:  
`pw_name:pw_passwd:pw_uid:pw_gid:pw_gecos:pw_dir:pw_shell`

default sudo log: /var/log/secure

## Password hash

There are fields in the pwd hash, separated with **$** signs  
\$ hash_alg_type \$ salt \$ hash  

loke: $6$iX6QfU0C7NbPhxMa$JZ9dPMpoV4bb1a/sLhcCXGFkMSEvG81Aibsh68lhtRlemSlQH9XqXGmEwNCnATsE7Y0nU3dY/laejLluxFBUd. 
* hash_alg_type: 6   -> SHA512  
* salt: iX6QfU0C7NbPhxMa  
* rest is the hash itself  

To use LDAP / KERBEROS authentication
* sssd is needed (system security secices daemon) - need to be installed
* can be set manually, or using `authconfig` - there are tui and gtk versions with menus
* `krb5-workstation` package needed

( kerberos KDC = key distribution center )

IPA authentication
  * `authconfig` or `ipa-client-install`

AD authentication
  * `autconfig` and `winbind` or `realmd`, latter is better

You must use your full auth name (IPA: user@ipa.domain.com, AD: DOMAIN\user) but this may be disabled:
  sssd.conf:  use_fully_qualified_names = no

# container related

there are `/etc/subgid` and `/etc/subuid` files to assign ranges for user namespace (uid mapping) when running containers. Usually they exist. On user mapping errors you may chech them.