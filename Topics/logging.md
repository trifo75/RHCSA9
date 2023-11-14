## rsyslog
conf:
  /etc/rsyslog.conf és /etc/rsyslog.d

alap cél:
  /var/log/...

modulok:
  imjournal  -- input module journalctl
  imuxsock    -- input module unix socket (hagyományos syslog)
  omfile     -- output module FILE
  omusrmsg   -- output module user message - mindenkinek üzenet

input selection:
  facility.severity;facility.severity...

vannak még parser, modification, string generator, meg más modulok is

ss : Socket Statistics
  -n   -- don't resolve services (port names)

anacron naponta futtatja a logrotate-t (/etc/logrotate.conf és .d alapján)
-> a /var/log alatt a definiált cuccok rotálódnak

## systemd.journald
konfig: /etc/systemd/journald.conf 
  storage: auto  -- ha van perm. stor. könyvtár, akkor oda is ír
  alapban elvileg havonta rotál (?) de van maxfilesize meg rokonai 
journalctl   -- system log lekérdezése

/run/.../journal alatt gyűlik alapban csak memóriában

journalctl -o verbose    -- összes mezőt kiírja. Mező nevekre is lehet keresni:
journalctl _SYSTEMD.UNIT=sshd.service   -- csak az sshd üzenetei


## dmesg ??? a /var/log/boot.log fájlt ki olvassa?
