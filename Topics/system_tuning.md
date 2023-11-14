# System performance tuning with `tuned`

# Software to install
`tuned` package has to obe installed and enabled using systemctl (it is not enabled by default):  
`dnf install tuned`  
and  
`systemctl enable --now tuned`  

# Components
- `tuned` is the system daemon managing the tuning profiles
- `tuned-adm` is the command to apply/query profiles
- tuned *profiles* are tuning parameter collections to various scenarios (like max performance, virtual guest or powersave)

# Applying profiles
Apply profiles using `tuned-adm` command.
`tuned-adm active`  -- profile in effect
`tuned-adm list`    -- list all profiles
`tuned-adm profile` -- apply profile -->  you can specify more profiles thus combining them
`tuned-adm off` -- turn of tuned

# Config
Location of *factory* profiles: **TODO**
Custom profiles: **TODO**
