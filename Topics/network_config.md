## IP konfig

# valid if names:
enp0s3 -> ethernet port 0 socket 3 
wl...  -> wlan

eth0-ra vagy p0s3-ra lehet fallback

ifconfig MÁR LEJÁRT

ip addr show   (ip a)   -- összes iface ip konfiggal együtt  --   -s kapcsolóval statisztikákat is mutat
ip link show   (ip l)   -- összes iface link status
ip route show  (ip r)   -- routing tábla
    (minden ip parancshoz a "show" a default, ha nem mondasz mást)

# temporary mods
ip link dev DEVICENAME set up     -- link státuszt UP-ba teszi

?? hogyan veszek fel aliast?


ss parancs a netstat utódja (socket statistics)

ss -lt    -- Listen és TCP socketek


ipv6 loopback:   [::1]
::  -- nulla tartalmú blokkos sorozata az ipv6 cím közepén


#static ip konfigurálása nmcli-vel
nmcli conn add type ethernet con-name kapcs1 ifname enp0s8 ip4 192.168.55.72/24 gw4 192.168.55.1
   Kötelező elemek: 
      type ethernet
      ifname   kártya neve
   Opcionális elemek
      con-name kapcsolatneve (alias a connection.id-re,  ezen látja a conn show)
      ip4 192.168.55/24      (alias az ipv4.addresses-re,  CIDR formában kell megadni)
      gw4 192.168.55.1       (alias az ipvg.gateway-re)
      method manual          (alias az ipv4.method-ra  auqto=DHCP, disabled=letiltva)
    automatán UP-ba is teszi az interfészt (connecion.autoconnect yes   és   onboot yes)

nmcli general permissions    -- listázza, hogy mit tehet az adott user a network beállításokkal 

?? ip address add... paranccsal felrakott IP mikor tűnik el?

# nmcli doksi
man 5 nm-settings
man nmcli-examples

nmcli con add con-name kapcsolatneve type ethernet ifname enp0s8 ipv4.method auto ip4 10.128.2.39/24
   --> ip4 helyett használható "ipv4.addresses" és akkor több cím is felsorolható
   --> módosításnál   +ipv4.addresses   a meglévőkhöz adja.   dns-t is, satöbbi.


# hostname beállítás  -- illik FQDN-t használni 
/etc/hostname
hostrnamectl hostname prod-server-12.example.com
nmcli general hostname prod-server-12.example.com

vagy: nmtui (mindenre IS)

#névfeloldás
/etc/nsswitch.conf
  hosts:      files dns myhostname    # files kell a /etc/hosts-hoz

-> a hostnév nem kell a /etc/hosts-ba, mert ott az nsswitch-ben a "myhostname"

# DNS beállítás
a /etc/resolv.conf-ban vannak a szerverek, de EBBE NEM NYÚLUNK BELE - NetworkManager felülírja

# DNS szerver felvétele conn-hoz 
nmcli conn mod <conn_id> [+]ipvddns <dns ip>

