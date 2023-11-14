DNF csomagkezelés - dependenciák, stream, group, module

dnf check-update      # van-e frissitenivaló
dnf clean  _metadata_fajta_   # metadata cache érvénytelenítése
dnf download _csomagneve_     # egy csomag letöltése 
dnf install ...
dnf remove ...
dnf reinstall ...            # csomag újratelepítése a custom config megtartásával
dnf localinstall ...         # helyben lévő RPM ből telepít

dnf config-magager --add-repo "http://......satöbbi...."     # a /etc/yum.repo.d alá csinál új repo fájlt és enabled lesz 

dnf profides "fileneve vagy csomagneve"    # melyik csomaghoz tartozik a cucc?  kb mint az rpm -qf csak okosabban 

dnf history     # kiadott parancsok
dnf history undo 9    # a 9. parancs visszavonása
dnf history info 9    # a 9. parancs részletei 

/var/log/dnf.log    -  itt a DNF log 

dnf list [ installed / available ]     # telepitett / telepíthető csomagok listaja. (ha egyik se, akkor minden csomagot listáz)

dnf update -x kernel      # update mindent, kivéve kernel 

dnf repolist [--all --enabled --disabled]  # repo lista (default: csak enabled)
dnf config-manager  --set-enabled reponeve   --set-disabled masikneve # repository be/kikapcsolás
dnf repoquery             # minden csomag mindenhonnan
dnf repoquery --repo reponeve   # adott repo csomaglistaja - akkor is, ha disabled

dnf reposync    -- le lehet tölteni egy teljes repo aktuális állapotát helyben felhasználásra - saját repo szerver készítés, vagy ilyesmi célra

dnf group       - nagyobb csomagcoportok, pl GUI
dnf group list   - csoportok listája
dnf group list hidden  - a csoportok listája TÉNYLEG

dnf group install
dnf group update    - install/update/remove package groups
dnf group remove


dnf module            - egyes szoftverekhez tartozó csomagok gyűjteménye
  státuszok:  [d]default  [e] enabled  [x] disabled  [i] installed

dnf module list perl    - perl összes stream összes profile listája
dnf module list perl:5.24/common      - formátum:  app_neve:stream_neve/profile_neve

           reset / enable / disable   - melyik stream-ból települjön a cucc
     default: ha semmi se enabled, akkor a köv telepítéskor ez lesz (repo default)
     enabled: ebből telepítünk (csat egy stream lehet enabled)

## Honnan tudom, hogy egy csomag melyik modullal települt?  -- sehonnan
## Honnan tudom, hogy egy csomag melyik modulnak része?     -- sehonnan



## LIST DEPENDENCIES


## postgresql:10 - két verzió van, mindkettő "installed" és egyben jelentek meg. Akkor ez most mi?

