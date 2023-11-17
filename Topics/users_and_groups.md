chapter 3 - users and groups
============================

default sudo log: /var/log/secure

useradd inkább adduser kevésbé
usermod
userdel

UID tartományok
0            root
1-200        system users, RedHat saját daemonjai
201-999      system users, unprivileged, dynamic pool
1000+        norma users

A tartományok a /etc/login.defs fájlban állíthatók

system user/group létrehozásához -r ( --system ) kapcsoló kell
groupadd ... -r 
useradd ... -r

# Add multiple users at once
newusers command takes input from STDIN with these fields:  
`pw_name:pw_passwd:pw_uid:pw_gid:pw_gecos:pw_dir:pw_shell`



Password hash - $ jelekket elválaszott mezők
  $ hash_alg_type $ salt $ hash

  pl: $6$iX6QfU0C7NbPhxMa$JZ9dPMpoV4bb1a/sLhcCXGFkMSEvG81Aibsh68lhtRlemSlQH9XqXGmEwNCnATsE7Y0nU3dY/laejLluxFBUd.
  hash_alg_type: 6   -> SHA512
  salt: iX6QfU0C7NbPhxMa
  a hash a maradék

LDAP / KERBEROS authentikációhoz
  * sssd daemon kell (system security secices daemon) - külön telepítendő
  * beállítás kézzel, vagy authconfig paranccsal. Ebből van authconfig-tui és authconfig-gtk verzió is
    azokban menüből lehet állítgatni
  * krb5-workstation is utólag telepítendő

    kerberos KDC = key distribution center

IPA authentikációhoz
  * authconfig vagy ipa-client-install

AD authentikációhoz
  * autconfig és winbind vagy realmd, de inkább az utóbbi

Alapban teljes névvel kell belépni (IPA esetén user@ipa.domain.com, AD esetén DOMAIN\user) de kikapcsolható:
  sssd.conf:  use_fully_qualified_names = no
