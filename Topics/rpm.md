# RPM package management

RPM is the basic package management solution under YUM/DNF. It uses `rpm` command.    
Database is stored under `/var/lib/rpm`

Rpm by itself does not resolve dependencies, just checks them. 

## querying info

Querying is done with `-q` switch

* list rpm dependencies
    * `-q --whatprovides`
    * `-q --whatrequires`

* get info on packages
    * `-qip` (-q --info --package) get info on RPM package size, version, signature, etc - from *installable package files*
    * `-qi`  (--query --info) get detailed info on *installed* package 

## manipulating packages

* copy files from an RPM package file
    * `rpm2cpio my-package.rpm | cpio -dium`

* Checking attributes of installed packages
    * `rpm -V csomag`

## installing packages

TODO

## removing packages

TODO