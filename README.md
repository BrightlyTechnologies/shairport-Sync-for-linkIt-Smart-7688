# Shairport Sync for LinkIt Smart 7688

### Build the Firmware from Source
This article describes how to build the firmware for LinkIt Smart 7688 and LinkIt Smart 7688 Duo from source codes.
<br />

## Host environment
The following operations are performed under a Ubuntu LTS 16.04.3 environment. For a Windows or a Mac OS X host computer, you can install a VM to have the same environment:

Download Ubuntu 16.04 LTS image from http://www.ubuntu.com
Install this image with VirtualBox (http://virtualbox.org) on the host machine. 50GB disk space reserved for the VM is recommanded
Steps
<br />

## Preconfigure Linkit Smart 7688 from OpenWrt source
In the Ubuntu system, open the Terminal application and type the following commands.
<br />

Install prerequisite packages for building the firmware:
```shell
$ sudo apt-get install git g++ libncurses5-dev subversion libssl-dev gawk libxml-parser-perl unzip 
```
<br />

Download OpenWrt CC source codes:
```shell
$ git clone git://git.openwrt.org/15.05/openwrt.git 
```
<br />

Prepare the default configuration file for feeds:
```shell
$ cd openwrt  
$ cp feeds.conf.default feeds.conf 
```
<br />

Add the LinkIt Smart 7688 feed:
```shell
$ echo src-git linkit https://github.com/MediaTek-Labs/linkit-smart-7688-feed.git >> feeds.conf 
```
<br />

Update the feed information of all available packages for building the firmware:
```shell
$ ./scripts/feeds update 
```
<br />

Install all packages:
```shell
$ ./scripts/feeds install -a 
```
<br />

Prepare the kernel configuration to inform OpenWrt that we want to build an firmware for LinkIt Smart 7688:
```shell
$ make menuconfig 
```

Select the options as below:

select `Target System > Ralink RT288x/RT3xxx`
select `Subtarget > MT7688 based boards`
select `Target Profile > LinkIt7688`

Save and exit (use the deafult config file without changing it)
<br />

## Preconfigure Shairport-Sync into OpenWrt source
if `audio/` not exists in `openwrt/`:
```shell
$ cd openwrt
$ mkdir -p audio/package
```
<br />

Clone the `Shairport Sync` package:
```shell
$ cd openwrt/audio/package/
$ git clone https://github.com/mikebrady/shairport-sync.git
```
<br />

Clone the `Shairport Sync for OpenWrt` package:
```shell
$ cd openwrt/audio/
$ git clone https://github.com/mikebrady/shairport-sync-for-openwrt.git
```
<br />

Perform the command
```shell
$./scripts/feeds install libavahi alsa-lib libdaemon libpopt libsoxr alsa-utils htop
```
This will install these packages into the OpenWrt build system if they are not already in place. Note that `alsa-utils` and `htop` are both very useful, but they are not actually needed for Shairport Sync to work.
<br />

Prepare the kernel configuration to inform OpenWrt that we want to build an firmware for Shairport Sync:
```shell
$ make menuconfig 
```

Select the options as below:

select `Sound > shairport-sync`
select `Kernel Modules > Sound Support > kmod-sound-core` and `kmod-usb-audio`
select `Utilities > alsa-utils` and `Administration > htop`


Save and exit (use the deafult config file without changing it)
<br />

## Compilation
Start the compilation process:
```shell
$ make V=99 
```

After the build process completes, the resulted firmware file will be under bin/ramips/openwrt-ramips-mt7688-LinkIt7688-squashfs-sysupgrade.bin. Depending on the H/W resources of the host environment, the build process may take more than 2 hours.
You can use this file to do the firmware upgrade through the Web UI. Or rename it to **lks7688.img** for upgrading through a USB drive.
<br />

## Running Shairport Sync for the First Time
Once you install the image on your device and restart it, (and assuming it has a soundcard), Shairport Sync should automatically start as a result of the `/etc/init.d/shairport-sync` initialisation script. The parameters it uses are in `/etc/config/shairport-sync`. If your device's IP number is within your network's subnet (see below), your device will show up in iTunes or iOS as an extra AirPlay device called "Shairport Sync on ...your computer's hostname...".

