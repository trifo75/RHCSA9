## STRATIS

telepítendő: stratis-cli stratisd
engedélyezendő/elindítandó: stratisd.service

Alapfogalmak / stratis alparancsok:
* pool  - kb mint a VG számára a PV-k    
* blockdev - a pool-t felépítő device-ek 
* filesystem - igen, pont az.   FS létrehozás és kezelés

stratis pool create mypool /dev/sdg    -- pool létrehozása "mypool" néven az sdg diszkre
stratis pool help                      -- subcommand rövid help
             add-data ...              -- data device hozzáadása
             list
             rename

stratis blockdev list                  -- pool alatti diskek listázása


