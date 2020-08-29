# building dropbear for pldthomefibr an5506-04-fa

NOTE: This is done under Ubuntu Xenial
## Prepare buildroot

```
sudo apt install build-essential libncurses5-dev bison flex gettext texinfo python unzip

sudo apt remove gcc-5 g++-5

sudo apt install gcc-4.7 g++-4.7

sudo ln -s /usr/bin/gcc-4.7 /usr/bin/gcc

wget https://github.com/buildroot/buildroot/archive/2012.08.tar.gz

tar xf 2012.08.tar.gz

cd 2012.08
```

## Configure buildroot
```
make menuconfig
```

1. set target architecture ARM (little endian)
2. set toolchain uClibc C library version 0.9.32.x
3. exit > save config

```
make MAKEINFO=true
```

## Build dropbear

_optional step: configure dropbear first on make menuconfig_

```
make dropbear
```

move output/target/usr/sbin/dropbear to your device at /bin/dropbear.

setup symlinks

```
# on router
ln -s /bin/dropbear /bin/dropbearconvert
ln -s /bin/dropbear /bin/dropbearkey
```

create authorized_keys and hostkey file at /bin

### optional add banner

Prepare a banner at `/fh/extend/pldt-banner.txt`

Make a script `### /fh/extend/start_dropbear.sh`
```
#!/bin/sh

#open dropbear port
iptables -I INPUT -p tcp --dport 2222 -j ACCEPT

#run ssh server
dropbear -r /bin/hostkey -p 2222 -b /fh/extend/pldt-banner.txt
```

at the bottom of the file add `/fh/extend/start_dropbear.sh` to `initialize.sh`

reboot

## connect to your device

```
ssh root@192.168.1.1 -p 2222 -oKexAlgorithms=+diffie-hellman-group1-sha1
```