# Package management with DNF
`dnf` is used to manage software packages/groups/modules

## subscriptions
In RHEL you need to have a valid subscription to be able to access RedHat repositories. To introduce your server to RedHat, you have
to use `subscription-manager register` then provide username and password of you RedHat subscription. This automatically
sets up repositories available to you. If it is not needed, you can `unregister` your host. 

RHEL will complain about the need of registration. To avoid this you can disable the warning with `subscription-manager config --rhsm.auto_enable_yum_plugins=0`
or put `enabled=0` into `/etc/dnf/plugins/subscription-manager.conf`

## repositories
Repos are software packages collected in a bucket. They are represented by entries in `/etc/yum.repos.d/*.repo` files. The `repo` sufix is important. They can reside on disk (local, optical, network, ...) or accessible through http(s) protocol. The basic elements of a repository definition are like this:  

`[BaseOS]`  -- repo ID  
`name=Red Hat Enterprise Linux 9.0 BaseOS RPMs (DVD)`  -- repo name  
`metadata_expire=-1`  -- metadata validity time  
`enabled=1`  -- will be seen in default list or not  
`gpgcheck=1`  -- will try to check packages for integrity or not  
`baseurl=file:///var/www/html/repo/BaseOS/`  -- location/dierctory of repository data structure  
`gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release`

If you want to check your packages you may need to get gpg keys (most probably they are installed in `/etc/pki/rpm-gpg` dir) and import them using `rpmkeys --import ...` command

There are mostly 2 types of RHEL repos: **BaseOS** and **AppStream**, holding core OS elements and auxiliary software respectively.

Quick way to define a repository:
`dnf config-magager --add-repo "http://......location...."`  -- sets up a repo with ID/name/BaseURL as the URL provided and gets enabled.

* `dnf repolist [--all|--enabled|disabled]`  -- lists defined repositories (default is *all*)
* `dnf config-manager <--enable|--disable> <repo_id>`  -- enable or disable a repo (changes entry to *enabled=1* or *enabled=0*)

* `dnf repoquery`  -- list every package from every enabled repo (can be filtered with *--repo ...repo_name...* to a single repo)

* `dnf reposync ...`   -- downloads full contents from a repo to use locally or create own repo server. With `--download-metadata` it gets *repomd.xml*, thus no need to run createrepo. And also gets *comps.xml* containing group/module information, if available in that repo. 
* `createrepo ...`  -- builds repo metadata for downloaded packages


## packages
RHEL mostly uses RPM package format. For individual package management, there is `rpm` command. This *does not* handle dependency issues. Alternatively you can use `dnf localinstall` or in recent RHEL simply `dnf install` to install locally available RPM packages. 

* `dnf download *<pkg_name>*`  -- download a package into local storage
* `dnf install *<pkg_name>*`  --  get package from repository and install - with deps
* `dnf localinstall *<pkg_name>*`  -- install locally available package (deprecated)
* `dnf remove *<pkg_name>*`  -- uninstall package - with deps
* `dnf reinstall *<pkg_name>*`  -- install an already installed package, keeping its current config (refresh)

A `--repo ...` switch can be used to specify the source of installation.

## groups
Package groups are bundles which are part of a specific setup.
Environement groups are like **Server with gui** or **minimal install**. Other groups bundle software pieces around a specific topic, like **Legacy UNIX Compatibility**.

* `dnf group list [--hidden]`  -- list all [and more] groups
* `dnf group install ...`  -- install all packages for a group
* `dnf group update ...`  -- update packages for a group
* `dnf group remove ...`  -- get rid of a group

A `--repo ...` switch can be used to specify the source of installation.

## modules
Modules are like software components of multiple packages bound together, like *postgresql*. A module can have multiple *streams*, which are like major versions. You have to select which one to install. Under streams, there may be *profiles* which are like environement groups, just on module level. Like *client/server/minimal*

  statuses:  [d]default  [e] enabled  [x] disabled  [i] installed

* `dnf module list nodejs`  -- list all streams/profiles of nodejs
* `dnf module list nodejs:18/minimal`      -- format:  app_name:stream_name/profile_name

* `dnf module reset / enable / disable`  -- select stream to install from  

default: if nothing is enabled, this will be selected - and also enabled automatically - on install (repo default)
enabled: thist is selected to install from (only *one* stream can be enabled at a time)

## logs and history

Mainly the dnf operations are logged into `/var/log/dnf.log`  

Also executed operations can be listed/managed using `dnf history` command.
* `dnf history`  -- list of operations
* `dnf history info 9`  -- details on the 9-th operation
* `dnf history undo/redo 9`  -- undo/redo 9-th operation

## searching and metadata

* `dnf clean  _metadata_fajta_`  -- invalidate metadata cache
* `dnf makecache`  -- download and build package version cache
* `dnf check-update [package spec]`  -- searches for available update, optionally for specific packages/modules/groups

* `dnf list [installed|available]`  -- list installed packages (like `rpm -qa`) or installable packages
* `dnf provides "filename or pgk_name"`  -- which package this belongs to? Like `rpm -qf ...` but smarter. For files you should give path or wildcards: `*/policycoreulils`   

* `dnf update kernel`  -- updtate kernel packages only
* `dnf update -x kernel`  -- exclude kernel packages from update


## additional repositories 
* **EPEL** - Extra Packages for Enterprise Linux (Fedora mainly)
* **COPR** - COmmunity PRojects