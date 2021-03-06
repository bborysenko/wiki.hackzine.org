#############################
GuruPlug: uBoot upgrade guide
#############################

See also the full guide here: :doc:`guruplug-misc`

.. note::
    This is a mirror copy of `Upgrading SheevaPlug's U-Boot`_

.. _Upgrading SheevaPlug's U-Boot: http://www.cyrius.com/debian/kirkwood/sheevaplug/uboot-upgrade.html

.. note::
    To access the uBoot console: connect the GuruPlug serial port to your
    computer and start console, then power up the plug.
    You'll see a message saying to press a key to enter uboot console: do that.

This page describes how to install a new version of U-Boot on your plug computer.
U-Boot is the boot loader used on plug computers.

First of all, check which version of U-Boot is installed on your plug computer
to find out whether you have to perform this upgrade.

Connect to U-Boot using the serial console and type::

    version

This will show the version of U-Boot on your plug computer.
Two different (and slightly incompatible) variants of U-Boot are commonly
used on plug computers. When Marvell introduced the first plug computers a few
years ago, they created their own variant of U-Boot to add support for plug
computers.
This work has been integrated into the official U-Boot variant in the meantime.
The advantages of the official U-Boot variant (sometimes known as DENX) is that
it is much more rapidly developed and therefore offers features and bug fixes
not found in the Marvell variant. We will use the DENX variant.

When you type version in U-Boot, you can easily spot if you're using the
Marvell version. It shows the U-Boot version 1.1.4 plus a separate Marvell
version, as in this example::

    U-Boot 1.1.4 (Dec 27 2009 - 22:03:21) Marvell version: 3.4.27

On the other hand, the version string of the DENX variant looks like this::

    U-Boot 2011.12 (Mar 11 2012 - 18:59:46)

In other words, U-Boot is followed by a date and there is no Marvell version.

If your plug computer is using the Marvell variant of U-Boot, you have to
upgrade since the installation guide assumes that you're using the DENX variant.
If you're already using the DENX variant, you can upgrade in case your U-Boot
is older than the one above (i.e. older than 2011.12).

If you're upgrading from the Marvell to the DENX variant, issue the following
command and write down your MAC address. This step is important because all
U-Boot configuration data (including your MAC address) is lost when upgrading
from the Marvell to the DENX U-Boot.

::

    print ethaddr

Obtain the U-Boot binary for your device and save it as u-boot.kwb.
Please note that since the boot loader interacts with the hardware, you have
to use the correct u-boot binary for your device. If your device is not listed
on this page, it is not safe to use another u-boot binary listed here!

* SheevaPlug_ (the original SheevaPlug, the eSATA SheevaPlug and the Ionics Nimbus 100)
* GuruPlug_ (GuruPlug Server Standard and GuruPlug Server Plus)
* `Seagate FreeAgent DockStar`_

.. _SheevaPlug: http://people.debian.org/~tbm/u-boot/2011.12-3/sheevaplug/u-boot.kwb
.. _GuruPlug: http://people.debian.org/~tbm/u-boot/2011.12-3/guruplug/u-boot.kwb
.. _Seagate FreeAgent DockStar: http://people.debian.org/~tbm/u-boot/2011.12-3/dockstar/u-boot.kwb


There are different ways to upgrade U-Boot on your plug computer:

**USB:** First of all, copy the U-Boot binary u-boot.kwb to a USB stick
formated with the FAT filesystem. Then plug the USB stick into your plug
computer, connect the serial console and type the following commands::

    usb start
    fatload usb 0:1 0x0800000 u-boot.kwb
    nand erase 0x0 0x60000
    nand write 0x0800000 0x0 0x60000

**TFTP:** If you have a TFTP server, copy the file u-boot.kwb to your TFTP
server. Then start your plug computer, connect the serial console and type the
following commands::

    setenv serverip 192.168.1.2 # IP of your TFTP server
    setenv ipaddr 192.168.1.200
    tftpboot 0x0800000 u-boot.kwb
    nand erase 0x0 0x60000
    nand write 0x0800000 0x0 0x60000

Regardless of how you installed U-Boot, you now have to restart your machine
to load the new version of U-Boot::

    reset

If you upgraded from the Marvell to the DENX variant, you now have to set your
MAC address (replace the MAC address in the example with the one you wrote down
before)::

    setenv ethaddr 00:50:43:01:c0:ab
    saveenv
    reset

Finally, if you have an eSATA SheevaPlug, you have to change some settings in
u-boot so the device will boot the correct kernel for your device.
This step is only required on the eSATA SheevaPlug and not on other plug
computers. If you have an eSATA SheevaPlug, type::

    setenv machid a76
    saveenv
    reset

These commands will put in the correct settings and then restart the device
so the changes will take effect.

Go back to my `Debian on Plug Computer`_ page.

.. _Debian on Plug Computer: http://www.cyrius.com/debian/kirkwood/sheevaplug/index.html
