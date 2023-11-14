### systemctl 

systemctl                  -- az aktuális betöltött unitok listája
systemctl --all            -- az ÖSSZES unit listája
systemctl --failed         -- hibára futott unitok

valami.socket              -- ugyanilyen nevű valami.service vezérlő socketje
valami.d                   -- ugyanilyen nevű unitból csak snippeteket felülíró kis részletek

systemctl list-dependencies  [service name] -- hierarchikus lista a service-ekről (teljes fa vagy csak az adott service)
systemctl status [service]  -- státusz infó. Ebben szerepel a tényleges unit file, doc link, fut/nem, pid, stb

## a syslog sercice csak egy link az rsyslog-ra. Ha disable-olom, akkor törli, de enable onnan nincs. WTF? 
   --> a syslog egy alias az rsyslog-ra 

          enable    -- az [install] szekció "WantedBy" paraméterében meghatározott target-be csinál symlinket
          disable
          is-enabled
          is-disabled
          is-active
          stop / start
          mask / unmask     -- ott marad, csak elrejtve

.target-ek : gyűgtemény, mint az INIT-nél a runlevel-ek
systemctl -t target     -- target-ek listája
           get-default  -- igen, az. 
           set-defailt  -- csinál egy linket a megadott targetre. Ebbe bootolunk innentől 

systemctl isolate multi-user   -- átváltás másik targetre - graphical vs multu-user. Csak ha AllowIsolate=yes


