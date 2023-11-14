## firewall konfig

A kernel megvalósítás neve: netfilter
ez közvetlen macerálható az nftables paranccsal, de a 
firewalld sokkal jobb

firewall-cmd  


# zóna: szabálygyűjetemény bejövő csomagokra

firewall-cmd  --get-default-zone   -- az alap zónát megmondja
              --get-zones          -- az összes zóna listája
              --get-active-zones   -- az épp aktív zónák listája

              --get-services       -- az előre definiált szolgáltatások listája (xml-ek)
                                      /usr/lib/firewalld/services/ és /etc/firewalld alatt
              --info-service=http  -- a http serice rszletei (portok, protokollok, miegymás)
              --service=http --add-port 8080/tcp --permanent     -- a http service-ez listen port engedélyezése
                                                                    a konfigba felvésve permanensen
              --reload             -- a konfig betöltése a kernelbe
                         * hogy derül ki, hogy mi az, ami nincs még betöltve?

              --runtime-to-permanent  -- az aktuális élő konfig kitolása XML-be

              --list-all              -- aktív zóna paraméterei
              --list-all --zone=xxx   -- xxx zóna paraméterei

              --get-XXX   -- tárolt konfig elemei 
              --list-XXX  -- épp aktív zóna elemei (vagy nem, de talán ilyesmi)

