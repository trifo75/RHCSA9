PACKAGE MANAGEMENT

database: /var/lib/rpm

rpm -> yum -> dnf

list rpm dependencies
-q --whatprovides
-q --whatrequires

-qip (-q --info --package) csomagméret, verzió, aláírás, satöbbi lekérdezése  - rpm csomagról
-qi  (--query --info) mindenféle infó lekérés telepített csomagról 

rpm2cpio csomag.rpm | cpio -dium   - csomagból fájlok kivétele

telepített fájlok (csomagok) ellenőrzése
rpm -V csomag      - telepített fájlok attributumainak ellenőrzése

repo definícióban elérés:
baseurl=file:///mnt/mittomen      - // utan még egy /
baseurl=http://example.com/...    - normál URL

DNF konfig:
/etc/dnf/dnf.con

EPEL - Extra Packages for Enterprise Linux (Fedora főleg)
COPR - COmmunity PRojects

Repo file dir:
/etc/yum.repos.d

