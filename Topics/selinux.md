## SELinux

policy - maga a szabálygyűjtemény

Módok:
disabled   --   ha a kernel cmdline-nél volt "selinux=0", akkor teljesen off, amúgy csak inaktív
enforcing  --   a szabálygyűjtemény érvényesítése
permissive --   szabálysértés esetén csak logolás. Hibakeresésre nagyon jó. NE HAGYD ÍGY!!!

log:  /var/log/audit/audit.log    -->   type=AVC  kezdetű sorok érdekesek


config: /etc/sysconfig/selinux   -- itt van beállítva az SELINUX mód és a SELINUXTYPE üzemmód
                                    disabled/enforcing/permissive         targeted/minimum/mls

grubby-val kapcsolható, hogy az egyes kerneleknél legyen "selinux=0" vagy ne


getenforce    -- megmondja, hogy él-e a SElinux
setenforce    -- ideiglenes üzemmód váltás (Enforcing/Permissive)
sestatus -v   -- részletesebb státusz infó (az aktuális környezetről)

sepolicy generate   -- policy összeállítása még nem létező konfighoz 

# file context lekérdezése
sok parancs támogatja a -Z kapcsolót, ami megmutatja a context-et

[root@lab-repo audit]# ls -dZ /var
system_u:object_r:var_t:s0 /var      ==> ebből a _t végű érdekes. A _u (user) és _r (role) résszel most nem dolgozunk

context beállítása:

                  add
                     type
                                                                   a file/könyvtár, amire vonatkozik (regex)
                                                                   itt a /mydir és alatta minden
semanage fcontext -a -t <file context, amit már tudunk valahonnan> "/mydir(/.*)?"
restorecon -v -R /mydir
# a semanage csak a policy-t állítja be a fájlokra azt érvényesíteni kell, erre van a restorecon
# .autorelabel fájl ha van, akkor boot-kor a policy alapján minden contextet felülír

man semanage-fcontext   -- sok jó példa

chcon kerülendő!!!  (csak az fs-be íg, a policyba nem, így relabel után felülíródik)

### ha a webroot context-je nem jó, akkor hogyan szolgál ki az eredeti webroot alól???

## webroot áthelyezése
# webroot előkészítés
mkdir /new_webroot
semanage cfontext -a -t httpd_sys_content_t "/new_webroot(/.*)?"
restorecon -R -v /new_webroot

# httpd konfig
DocumentRoot "/new_webroot"
<Directory "/webcontent">
    AllowOverride None
    Require all granted       # enélkül nem lehet olvasni
</Directory>

systemctl enable --now httpd    (vagy csak apachectl graceful, ha már fut)


## httpd listen port áthelyezés
#httpd config 
Listen 87
# aztán graceful

semanage port --add --type http_port_t --proto tcp 87   # nem kell restorecon
firewall-cmd --permanent --service=http --add-port 87/tcp
firewall-cmd --reload                                   # itt kell restart

ss és netstat paranccsal látszik ,hogy figyel a 87-en 

# Selinux boolean beállítások
# akár sok rule-ra érvényes kapcsolók

getsebool -a            -- az összes lekérdezése
setsebool param on/off  -- kapcsoló állítása NEM PERMANENS, csak a -P kapcsolóval

semanage boolean -l     -- összes érték listázása aktív és policy is



# logelemzéshez segítség:
sealert      (lehet, hogy telepíteni kell: setroubleshoot-server)

daemon, ami olvassa az audit.log-ot és jól (jobban) olvasható bejegyzéseket készít 
belőle a journalctl alá
aztán sealert -l {id} parancs generál részletes útmutatót

