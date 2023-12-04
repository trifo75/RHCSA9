# Configuring firewall

Name of firewall implementation in the kernel is `netfilter`
(earlier implementations included iptables and ipchains)

Native management command is `nftables`, but there is a much 
better alternative: `firewalld` and `firewall-cmd`.
This handles wee all inbound and outbound traffic and theyr dependencies. (It's like the relation between `rpm` and `dnf` commands)

## firewalld prerequisites
`firewalld` must be installed and enabled in systemd. When it is running, it loads the config from `/usr/lib/firewalld/...` and `/etc/firewalld/...`

## basic definitions

- **zone** is a group of interfaces with similar functions and a collection of rules that apply to the traffic on them. Like connecting to the internet, connecting to proxy, etc. When you have just one interface, most probably you will have just one zone active. There are multiple zones in the config and you can activate them a needed. 
- **service** is a connection of network traffic directions (inbound ports mostly) that are needed to serve a specific service. Like to serve `sshd` you need to let client connect to TCP port 22. You can add services to zones to simplify management.
- **port** is a TCP/UDP port. Can be enabled in zones by themselves, without adding them to services.

You can modify the live config but a good practice is to modify the config files and then reload them. Thus you should add `--permanent` option to every command that modifies the config to save the modification to files and then run `firewall-cmd --reload`

Alternatively you can run `firewall-cmd --runtime-to-permanent` to save the running state into the config files. 

**QUESTION** - how do I know the differences between the running system and the config files?

## Managing zones

- `firewall-cmd --get-default-zone` -- shows default zone. Most probably itt will be *public*
- `firewall-cmd --get-zones` -- shows all defined zones from the config files
- `firewall-cmd --get-active-zones` -- shows all zones in effect
- `firewall-cmd --info-zone=...` -- shows detailed info about the named zone. Interfaces, enabled services, ports, etc...
- `firewall-cmd --list-all` -- shows all info about the zones in effect
- `firewall-cmd --list-all --zone=...` -- shows all info about the specified zone 

## managing services

- `firewall-cmd --get-services` -- list name of all defined services from `/usr/lib/firewalld/services/` and `/etc/firewalld/services/`
- `firewall-cmd --info-service=...` -- detailed info about a specific service
- `firewall-cmd --service=http --add-port 8080/tcp --permanent` -- extend the port list of *http* service and saves the change into config.
- `firewall-cmd --` -- 

## Others

You can manage ports in similar ways. Most times modifiing services seems better for me. 

Most options are like this:
`--get-XXX` -- items from the config files
`--list-XXX` -- items from the running config

**TODO** info about logging 

