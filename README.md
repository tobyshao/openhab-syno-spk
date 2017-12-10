# openHAB Synology DiskStation Install Package

This project build a Synology DiskStation SPK install package for [openHAB](http://openhab.org) (Home Automation Server).

Comments, suggestions and contributions are welcome!

## Download

Download SPK package from [![Github Releases](https://img.shields.io/badge/download-releases-blue.svg)](https://github.com/openhab/openhab-syno-spk/releases) [![Github All Releases](https://img.shields.io/github/downloads/openhab/openhab-syno-spk/total.svg?maxAge=2592000)](http://www.somsubhra.com/github-release-stats/?username=openhab&repository=openhab-syno-spk)

### Logging

The openHAB log files can be found here:
`/volume1/@appstore/openHAB/userdata/logs/`.

From Version openHAB-2.2.0.006 the SPK generates two log files located at `/var/log/`.

The first log file `openHAB-install.log` will be generated during the installation. If you can't install the openHAB SPK, please have a look inside the file.

The second log `openHAB-start-stop.log` will be generated by starting and stopping openHAB inside the Package Manager.

## Documentation

Installation und Prerequisite Install Java Documentation: [Official openHAB Usage Manual](http://docs.openhab.org/installation/synology.html)

For usage of serial devices you need to check if openhab user has the appropriate rights to access the device in `/dev` and to create lock files in `/run/lock`

To do so the installer tries to add the `openhab` user to the groups `dialout` and `uucp`. But these groups are not present on all synology diskstations.

`/run/lock` for instance is created as this on a ds214play:

`drwxr-xr-x 5 root        root             160 Dec  9 20:11 lock`

and `/dev/ttyUSB0` is per default also created for root only:

`crw------- 1 root root 188,  0 Dec  4 12:55 /dev/ttyUSB0`

The result in nrjavaserial / RXTX to access this port would be a no port found exception.

To correct this you need three steps (login as admin on diskstations ssh login):

a) create a group and assign openhab user to it

`sudo synogroup --add uucp openhab`

b) setup udev rule so that access rights in `/dev/` are permanent and reload udev rules

example for pl2303 usb to serial converter:

`sudo echo 'SUBSYSTEM=="tty", ATTRS{idVendor}=="067b", ATTRS{idProduct}=="2303", GROUP="uucp", MODE="0660"' >>/usr/lib/udev/rules.d/50-yourrules`

example for general ttyUSB rule:

`sudo echo 'KERNEL=="ttyUSB*", ACTION=="add", MODE="0660", GROUP="uucp"' >>/usr/lib/udev/rules.d/50-yourrules`

reload rules now:

`sudo udevadm control --reload`

c) enable access for openhab user for lock file creation

`chmod 0775 /run/lock`

`chown root.uucp /run/lock`

edit `/etc/init/root-file-system.conf` to persist this change.

`sudo vi /etc/init/root-file-system.conf`

change line

`/bin/mkdir -p /run/lock || true`

to

`/bin/mkdir -m 0775 -p /run/lock || true`

`chown root.uucp /run/lock || true`

## Forum

Official community: [![Github Releases](https://img.shields.io/badge/openhab-forum-orange.svg)](https://community.openhab.org/t/synology-diskstation/1446)

## Chat

Discussion chat: [![Join the chat at https://gitter.im/openhab/openhab-syno-spk](https://badges.gitter.im/openhab/openhab-syno-spk.svg)](https://gitter.im/openhab/openhab-syno-spk?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

## Build

Travis build state: [![Build state](https://travis-ci.org/openhab/openhab-syno-spk.svg?branch=master)](https://travis-ci.org/openhab/openhab-syno-spk)

## Contributing

[![GitHub issues](https://img.shields.io/github/issues/openhab/openhab-syno-spk.svg)](https://github.com/openhab/openhab-syno-spk/issues) [![Issue Stats](http://www.issuestats.com/github/openhab/openhab-syno-spk/badge/issue?style=flat)](http://www.issuestats.com/github/openhab/openhab-syno-spk) [![GitHub forks](https://img.shields.io/github/forks/openhab/openhab-syno-spk.svg)](https://github.com/openhab/openhab-syno-spk/network) [![Issue Stats](http://www.issuestats.com/github/openhab/openhab-syno-spk/badge/pr?style=flat)](http://www.issuestats.com/github/openhab/openhab-syno-spk) [![GitHub stars](https://img.shields.io/github/stars/openhab/openhab-syno-spk.svg)](https://github.com/openhab/openhab-syno-spk/stargazers)

[Contribution guidelines](https://github.com/openhab/openhab-syno-spk/blob/master/CONTRIBUTING.md)

## License

When not explicitly set, files are placed under [![GitHub license](https://img.shields.io/badge/license-Eclipse-blue.svg)](https://raw.githubusercontent.com/openhab/openhab-syno-spk/master/LICENSE).
