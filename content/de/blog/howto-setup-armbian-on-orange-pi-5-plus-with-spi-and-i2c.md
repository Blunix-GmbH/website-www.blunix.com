---
title: "Howto Setup armbian on Orange PI 5 Plus with SPI and i2c Interfaces"
description: "This blog post describes howto install and configure armbian on an Orange PI 5 Plus board including setting up and using the SPI and i2c interfaces"
date: 2024-02-01
image: "/images/blog/orange-pi-5-plus-board.webp"
image_alt: "Howto install and configure armbian on an Orange Pi 5 Plus and using the i2c and SPI interfaces"
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

**IMPORTANT SECURITY NOTICE!** The website orangepi.org does not provide httpS. This means that all traffic from your computer to this website is not encrypted and can potentially be manipulated by a [man in the middle attack](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) . This blogpost contains multiple links to this website.

### [Introduction](#introduction)

The [Orange PI 5 Plus](http://www.orangepi.org/html/hardWare/computerAndMicrocontrollers/details/Orange-Pi-5-plus.html) is a faster and stronger competitor to the Raspberry PI 5 - it has more ressources and ports with up to 32 GB RAM, an the octa core ARM CPU Rockchip RK3588, up to 256 GB eMMC storage and two 2.5G ethernet ports it overpowers the Raspberry Pi 5s performance with ease. The downside in comparison to the Raspberry Pi 5 is that it has much less public content, from forum questions and answers to blog posts, online chat rooms and github libraries and tools. It is hence a bit for advanced users. The [operating system provided by the company Orange Pi](https://orangepi.dev/downloads/orangepi-5-plus/), which to our pleasure can be downloaded with httpS, is based on arch Linux and we did not investigate it further. For a Debian Linux based solution we chose [armbian](https://www.armbian.com/).

### [Installing armbian](#install-armbian)

![armbian website screenshot for Orange Pi 5 Plus download link](/images/blog/howto-setup-armbian-on-orange-pi-5-plus-with-spi-and-i2c/orange-pi-5-plus-armbian-website-download-link.webp)

Armbian provides [images specifically for the Orange Pi 5 Plus](https://www.armbian.com/orangepi-5/). The download link on top of the page labeled "Armbian 23.11.1 Bookworm CLI" however is for the Orange Pi 5 and not 5 Plus version of the board. To get the correct image for the 5 Plus board you have to scroll down a bit and click the link that says "For Orange Pi 5 Plus model, images can be downloaded here": The image can be downloaded and written to the micro SD card using the command line like follows.

Download the image using wget and verify the checksum:

```bash
wget https://mirror-eu-de1.armbian.airframes.io/dl/orangepi5-plus/archive/Armbian_23.11.1_Orangepi5-plus_bookworm_legacy_5.10.160.img.xz
```

Compare the checksum:

```bash
wget https://mirror-eu-de1.armbian.airframes.io/dl/orangepi5-plus/archive/Armbian_23.11.1_Orangepi5-plus_bookworm_legacy_5.10.160.img.xz.sha

user@workstation:~$ sha256sum Armbian_23.11.1_Orangepi5-plus_bookworm_legacy_5.10.160.img.xz
3b8f8ace0c4ca62d87fad4f83392ebb7d0412a45891d7c2e4f5d15e6a8c22e5c

user@workstation:~$ cat Armbian_23.11.1_Orangepi5-plus_bookworm_legacy_5.10.160.img.xz.sha
3b8f8ace0c4ca62d87fad4f83392ebb7d0412a45891d7c2e4f5d15e6a8c22e5c Armbian_23.11.1_Orangepi5-plus_bookworm_legacy_5.10.160.img.xz
```

Extract the .xz compressed archive:

```bash
unxz Armbian_23.11.1_Orangepi5-plus_bookworm_legacy_5.10.160.img.xz
```

Write the image to the SD card:

```bash
sudo dd if=Armbian_23.11.1_Orangepi5-plus_bookworm_legacy_5.10.160.img of=/dev/mmcblk0 status=progress && sudo sync
```

We noticed that the OpenSSH server did not start on boot, which requires an external monitor that can be connected via one of two available HDMI ports. The four USB ports, or the single USB-C port allow for attaching a mouse and a keyboard. The default Login credentials for armbian are: Username: root Password: 1234

### [Fixing kernel panic occuring after a few minutes](#kernel-panic-fix)

We noticed that the fresh installation was often crashed with a kernel panic a few minutes after booting the Orange Pi. The solution to this is described in [this Github issue](https://github.com/Joshua-Riek/ubuntu-rockchip/issues/502#issuecomment-1889730590):

```bash
dd if=/dev/zero of=/dev/mtdblock0 count=4096 bs=512
```

We found that the [Operating System provided by Orange PI](https://orangepi.dev/downloads/orangepi-5-plus/) based on Arch Linux runs without stability issues out of the box. You can run the dd command from this distribution before installing armbian.

The ROCK 5 chip features an onboard SPI flash device that is presented as /dev/mtdblock0:

```bash
user@orange-pi-5-plus:~$ sudo fdisk -l /dev/mtdblock0
Disk /dev/mtdblock0: 16 MiB, 16777216 bytes, 32768 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

This [MTD (Memory Technology Devices) block device](https://www.oreilly.com/library/view/mastering-embedded-linux/9781787283282/64271306-bd52-47d8-8118-6b618630d307.xhtml) stores a bootloader for auxiliary boot purposes. This enables the device to boot from media types not directly supported by the SoC's maskrom mode, including NVMe or USB 3. Using this, a fingernail sized NVMe SSD with up to 256 GB size can be attached to the Orange Pi 5 Plus board directly:

```bash
user@orange-pi-5-plus:~$ sudo fdisk -l /dev/mmcblk0
Disk /dev/mmcblk0: 232.96 GiB, 250139901952 bytes, 488554496 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

More information about managing the SPI can be found at [wiki.radxa.com/Rock5/install/spi](https://wiki.radxa.com/Rock5/install/spi).

### [First steps and basic configuration](#basic-configuration)

After the first login we recommend to perform the following basic tasks. First replace all default apt repositories configured for http with httpS:

```bash
sed -i 's@http://@https://@g' /etc/apt/sources.list
sed -i 's@http://@https://@g' /etc/apt/sources.list.d/*list
apt update
apt upgrade
```

The configure automatic security upgrades:

```bash
apt install unattended-upgrades
dpkg-reconfigure unattended-upgrades
```

Additionally you should take a look at armbians version of Raspberry PIs raspi-config:

```bash
armbian-config
```

### [Configuring i2c devices](#configure-i2c)

![Orange Pi 5 Plus 40 PIN description](/images/blog/howto-setup-armbian-on-orange-pi-5-plus-with-spi-and-i2c/orange-pi-5-plus-pins-description.webp)

The Orange Pi 5 Plus comes with a 40 Pin header to attach all sorts of devices and sensors:

The board has four groups of i2c buses, but I2C2_M0 and I2C2_M4 are connected to the same bus and can not be used at the same time. Luckily you can attach multiple devices to one i2c bus. In order to activate the first i2c bus open armbian-config and select System -> Hardware -> rk3588-i2c2-m0, save and reboot the device. These changes are saved in /boot/armbianEnv.txt, for example overlays=rk3588-i2c2-m0. The i2c-dev module also has to be loaded on boot:

```bash
user@orange-pi-5-plus:~$ cat /etc/modules
i2c-dev
user@orange-pi-5-plus:~$ modprobe i2c-dev
```

To run i2cdetect against all devices matching /dev/i2c\* use the following command:

```bash
ls /dev/i2c* | while read line; do
    id="$(echo $line | cut -d '-' -f 2)"
    echo -e "\n## Detecting i2c ID: $id"
    sudo i2cdetect -y $id
done
```

On our device, two i2c devices are connected and detected - in our example 68 is a BME 280 thermometer and 76 a MPU 5060 gyroscope:

```bash
## Detecting i2c ID: 2
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:                         -- -- -- -- -- -- -- --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- 68 -- -- -- -- -- -- --
70: -- -- -- -- -- -- 76 --
```

You can then address the sensors in python using [smbus](https://pypi.org/project/smbus2/) for example:

```bash
import smbus

bus = smbus.SMBus(2)
Device_Address = 0x68
```

More information on configuring i2c is available in the [Orange Pi Wiki Page](http://www.orangepi.org/orangepiwiki/index.php?title=Orange_Pi_5_Plus&mobileaction=toggle_view_desktop#40_pin_I2C_test).

### [Configuring the SPI interface](#configure-spi)

First the apt package spi-tools is required:

```bash
sudo apt install spi-tools
```

Add the following lines to /boot/armbianEnv.txt:

```bash
overlays=rk3588-i2c2-m0 rk3588-spi4-m2-cs0-spidev
param_spidev_spi_bus=0
param_spidev_max_freq=100000000
```

Load the spidev kernel module:

```bash
sudo modprobe -r spidev
sudo modprobe spidev
```

Create udev rules inside /etc/udev/rules.d/50-spi.rules to create devices devices files for SPI devices like /dev/spidev4.0:

```bash
SUBSYSTEM=="spidev", GROUP="spiuser", MODE="0660"
```

And reload the udev rules:

```bash
sudo udevadm control --reload-rules
```

Then add a Linux group to grant access for managing SPI devices and add your current Linux user to it. Make sure not to execute this command as the root user but as your regular user:

```bash
sudo groupadd spiuser
sudo adduser "$USER" spiuser
```

Note that you have to open a new login shell (logout and log back in again) for the group changes to take effect. You can then list available SPI buses like so:

```bash
user@orange-pi-5-plus:~$ ls /dev/spi*
/dev/spidev4.0
```

The github repository [ github.com/mcgurk/Arduino-WS2811-rotary-RGB-led-strip/](https://github.com/mcgurk/Arduino-WS2811-rotary-RGB-led-strip/blob/master/OrangePi.md) gives further examples on how to setup a WS2811 RGB LED strip on an Orange Pi.
