## partitioning
MBR: 4 primary, 11 logical (az egyik primary területén) - összesen 14 part lehet így
GPT: 128 part lehet, 2TB-nél nagyobb diszkek is mennek, 4k blokkméret

# parted - MBR és GPT is

üres diszkre először label kell
   parted /dev/sdb mklabel msdos    -- MBR parttábla lesz
   parted /dev/sdb mklabel gpt      -- GPT parttábla lesz
                   mkpart primary 1 101   -- primary partíció létrehozása 1MB-től 101MB-ig (ha az UNIT az MB) 
                   rm 1             --  1-es part. törlése

GPT-t inkább intézzünk gdisk paranccsal
   gdisk /dev/sdc
      o     -- új GPT parttábla létrehozása (protective MBR-rel)
      n     -- új partíció létrehozása. Kérdezget: start, end/méret (+200M az 200MB a starttól), Linux FS type: 8300
      w     -- adatok FELÍRÁSA és exit 

