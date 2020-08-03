Steps to install facetime hd camera drivers
===========================================

1.  Firmware extraction

``` bash

sudo apt-get install curl xzcat cpio
git clone https://github.com/patjak/facetimehd-firmware.git
cd facetimehd-firmware
make
sudo make install
```

2.  Extract sensor calibration files

``` bash

wget "https://download.info.apple.com/Mac_OS_X/031-30890-20150812-ea191174-4130-11e5-a125-930911ba098f/bootcamp5.1.5769.zip"
unzip bootcamp5.1.5769.zip
cd BootCamp/Drivers/Apple
unrar x AppleCamera64.exe
dd bs=1 skip=1663920 count=33060 if=AppleCamera.sys of=9112_01XX.dat
dd bs=1 skip=1644880 count=19040 if=AppleCamera.sys of=1771_01XX.dat
dd bs=1 skip=1606800 count=19040 if=AppleCamera.sys of=1871_01XX.dat
dd bs=1 skip=1625840 count=19040 if=AppleCamera.sys of=1874_01XX.dat
sudo cp *.dat /lib/firmware/facetimehd/
```

3.  Run installation script for kernel module

``` bash

git clone https://github.com/patjak/bcwc_pcie.git
cd bcwc_pcie
make
sudo make install
sudo depmod
sudo modprobe -r bdc_pci
sudo modprobe facetimehd
```

The three steps above will install the driver on an Ubuntu system.
However to auto-compile on each kernel update do the following.

Building Linux kernel modules
=============================

In this article we describe how to build a dynamic kernel module using
dynamic kernel module support (DKMS) system for Linux. The example used
here will be for Ubuntu Linux installed on Macbook pro. This guide uses
the installation of the facetimehd driver as an example.

-   First install the requisite system packages

``` bash
apt install debhelper dkms
```

-   Remove old package if installed

``` bash
dpkg -r bcwc-pcie
```

-   Make a source directory for the module

``` bash
mkdir /usr/src/facetimehd-0.1
```

-   Change into the git repo dir

``` bash
cd bcwc_pcie
```

-   Copy files over

``` bash
cp -r * /usr/src/facetimehd-0.1/
```

-   Change into that dir

``` bash
cd /usr/src/facetimehd-0.1/
```

-   Remove any previous debs and backups

``` bash
rm backup-*tgz bcwc-pcie_*deb
```

-   Clear out previous compile

``` bash
make clean
```

-   Register the new module with DKMS

``` bash
dkms add -m facetimehd -v 0.1
```

-   Build the module

``` bash
dkms build -m facetimehd -v 0.1
```

-   Build a Debian source package

``` bash
dkms mkdsc -m facetimehd -v 0.1 --source-only
```

-   Build a Debian binary package

``` bash
dkms mkdeb -m facetimehd -v 0.1 --source-only
```

-   Copy deb locally

``` bash
cp /var/lib/dkms/facetimehd/0.1/deb/facetimehd-dkms_0.1_all.deb ~/
```

-   Get rid of the local build files

``` bash
rm -r /var/lib/dkms/facetimehd/
```

-   Install the new deb package

``` bash
sudo dpkg -i /root/facetimehd-dkms_0.1_all.deb
```
