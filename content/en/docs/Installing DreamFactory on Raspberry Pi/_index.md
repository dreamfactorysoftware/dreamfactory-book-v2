---
title: "Installing DreamFactory on a Raspberry Pi"
linkTitle: "Raspberry Pi Installation"
weight: 11
---

For this tutorial we used Raspberry Pi's official [Raspbian](https://www.raspberrypi.org/downloads/) operating system. Raspbian is Debian-based, and although it doesn't include all of the requisites required by DreamFactory, as you'll see those can be installed easily enough.

Incidentally, we use the [balenaEtcher](https://www.balena.io/etcher/) to flash images onto SD cards. It includes a very friendly user interface, and is supported on macOS, Windows, and Linux.

After firing up your Raspberry Pi and completing the initial configuration process, you'll need to install NGINX, PHP, and a few other required libraries. This isn't as easy as just running `apt install` a few times, because at the time of this writing Raspbian did not yet ship with PHP 7.1 or newer. DreamFactory 2.14.2 requires at least PHP 7.1, and this requirement will again be raised in an upcoming release now that PHP 7.3 has been released.

To install a supported PHP version, you'll need to add the Raspbian testing branch to your sources list. To do so, create this file:

	$ sudo nano /etc/apt/sources.list.d/10-buster.list

Once opened inside the nano text editor (feel free to substitute nano with vim or similar), add this line:

	deb https://mirrordirector.raspbian.org/raspbian/ buster main contrib non-free rpi

Save these changes and then create another file:

	$ sudo nano /etc/apt/preferences.d/10-buster

Once opened, add the following contents to it:

	Package: *
	Pin: release n=stretch
	Pin-Priority: 900

	Package: *
	Pin: release n=buster
	Pin-Priority: 750

Save these changes, and run the following command:

	$ sudo apt-get update

With these changes in place, you'll be able to install PHP 7.1 or newer and therefore satisfy DreamFactory's minimum PHP requirements.

## Installing DreamFactory

Next we'll install the DreamFactory and its prerequisite software, beginning with the latter. Rather than create a redundant set of instructions, we'll instead point you to [the DreamFactory wiki](https://wiki.dreamfactory.com/DreamFactory/APT/Ubuntu_16.04/Installation). We followed the NGINX-specific instructions and everything worked perfectly.

## Home Automation Ideas

There are a number of great open source home automation libraries which could be easily integrated into your DreamFactory / Raspberry Pi environment:

* [PHP TP-Link Smartplug](https://github.com/jonnywilliamson/tplinksmartplug): This PHP library supports a variety of TPLink Smartplug devices.
* [Python TP-Link WiFi SmartPlug Client](https://github.com/softScheck/tplink-smartplug): This Python library also supports a number of TPLink devices

Be sure to let us know if you use these or other libraries in your DreamFactory projects!