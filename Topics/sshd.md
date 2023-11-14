## SSH

# passphrase mentése ideiglenesen:
ssh-agent   /bin/bash      -- nyit egy új shellt, amit az ssh-agent birtokol 
ssh-add                    -- az új shellben felszedi a default kulcsokat és bekéri hozzájuk a passphrase-t 
                              lehet neki másik kulcsokat is mondani 

# sshd_config
PermitRootLogin  prohibit-password   -- root beléphet, de csak kulccsal
AllowUsers                           -- csak a felsorolt userek léphetnek be

TCPKeepAlive on/off                  -- TCP rétegben próbálja a kapcsolatot életben tartani üres csomagokkal. Tűzfalon keresztül nem biztos, hogy megy.
ClientAliveInternal és ClientAliveCount  -- ennyi másodpecenként pingeti a szerver a klienst, max ennyiszer. Ha addig sincs mozgás, akkor bont
ServerAliveInternal és ServerAliveCount  -- ugyanaz visszafelé, ezt a kliens állíthatja be

UseDNS   -- megpróbálja feloldani a kliens címét - lassú lehet 

MaxSessions    -- ennyi kapcs lehet EGY IP-ről


# nondefault port (2022) hozzáadása: sshd_configba még egy Port sor:  Port 2022 - és restart
semanage port --add --type ssh_port_t --proto tcp 2022        # SELinux
firewall-cmd --service=ssh --add-port 2022/tcp --permanent    # firewall
firewall-cmd --reload
