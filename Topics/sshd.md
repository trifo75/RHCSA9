# SSH

SecureShell 

## sshd_config
Contains server-side config
* `PermitRootLogin prohibit-password`  -- root user *can* login, but using key auth only. This is the default. Can be set to `yes` or `no` too.
* `AllowUsers`  -- restricts access to the listed users only. Defaults to all users.
* `TCPKeepAlive on/off`  -- Tries to keep the connection on in the TCP layer using empty packages. Some firewalls do not respect this. 
* `UseDNS`  -- tries to check back client IP/name for extra security. Sometimes can be slow.
* `MaxSessions`  -- controls the man number of sessions *from a single IP*
* `ClientAliveInterval` and `ClientAliveCountMax`  -- on an idle connection the server sends *NOOP* commands  through SSH channel by *Interval* seconds until *CountMax* is reached. 

## ssh client config
Users can put theyr own client settings to `~/.ssh/config` file
* `ServerAliveInterval` and `ServerAliveCountMax`  -- the same behavior *ClientAlive...*, but from client side

## key based authentication
For security reasons it is good idea to use SSH keys for authentication, not password. For this you need to generate a key-pair (public and private keys).  
`ssh-keygen` command generates a key pait to the default place: `~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub` . Along the generation process you may specify a *passphrase* for the private key. Not mandatory, but enhances security.  

To use this key for authentication, the pub key must be put to the target user's `authorized_keys` file. The simplest way to do this is using `ssh-copy-id remote_user@remote_server`. You will need an enabled password authentication for this. 

`ssh` by default will try to use key auth before falling back to passwd. Thus `ssh remote_user@remote_server` will use your newly generated and copied key. It the key has a passphrase, you will have to provide that on every connection.

OR...

## saving passphrase temporarily for passwordless connections
You can use `ssh-agent` to keep your passphrases in memory, thus you do not have to type them every time.

`eval $(ssh-agent -s)`  -- sets up an ssh agent environement in your current shell
`ssh-add`  -- asks for the passphrase of your default key and puts in the *ssh-agent*

Ater this the passphrase will be provided by ssh-agent, no need to type for every connection. 

This setup lives as long as your shell - everything is stored in memory only.

You can add multiple passphrases for multiple keys as needed. 

## using non-default port for ssh daemon
Sometimes it is a good practice not to run ssh daemon on the well-known port (22). To achieve this you need to modify 3 configs:
* In the `sshd_config` you must specify the new listen port using `Port 2022`
* You have to introduce the new port to SELinux, using `semanage port --add --sype ssh_port_t --proto tcp 2022`
* Configure firewall for the new port: `firewall-cmd --service=ssh --add-port 2022/tcp --permanent` and then `firewall-cmd --reload`
