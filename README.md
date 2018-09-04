# gentoo-b2-kernel-patches
Patchset for Linux kernel 4.9.16 / 4.9.72 (from [gentoo-sources](https://wiki.gentoo.org/wiki/Kernel/Overview#General_purpose:_gentoo-sources)), for use with the Excito B2 miniserver

## Description

<img src="https://raw.githubusercontent.com/sakaki-/resources/master/excito/b2/Excito_b2.jpg" alt="Excito B2, aka Bubba|TWO" width="250px" align="right"/>
This project contains a set of patches for the Linux kernel, enabling it to be used with the Excito B2 (aka Bubba|TWO) miniserver.

Patches are currently provided for (LTS) Linux version **4.9.16** (I may add support for further kernel versions in the future). These can also successfully be applied to (LTS) **4.9.72**. Patches 0001, 0004 and 0005 are derived from those in Mouette's [community-b3-kernel](https://github.com/Excito/community-b3-kernel/tree/master/debian/patches) (thank you!), updated where necessary. Patch 0002 was created by running the device tree compiler (in reverse mode) on the `8313E21.dtb` file in the B2's `/boot` directory. Patch 0003 is my own.

The individual patches provided are listed below, together with their purpose:

Patch | Function
:--- | :---
[0001-Excito-B2-board.patch](https://github.com/sakaki-/gentoo-b2-kernel-patches/blob/master/0001-Excito-B2-board.patch) | This contains Tor's original B2 kernel driver, for access to the B2's power button, LED and reboot functionality. It also contains kconfig changes, providing the new item `CONFIG_BUBBA2` (which you need to select when building your kernel), and a few other device-specific tweaks (without which you won't have USB access, for example, which is needed when mounting the root directory from a USB key).
[0002-Excito-B2-device-tree-source.patch](https://github.com/sakaki-/gentoo-b2-kernel-patches/blob/master/0002-Excito-B2-device-tree-source.patch) | The B2, being a [PowerPC](https://en.wikipedia.org/wiki/PowerPC)-based device, uses a [flattened device tree blob](http://events.linuxfoundation.org/sites/events/files/slides/petazzoni-device-tree-dummies.pdf) (DTB) to pass most of the hardware specific information from the bootloader to the kernel. Two device tree source files are provided by this patch: bubba.dts (which is required for "release 1" B2s), and 8313E21.dts (which is required for "release 2" B2s).
[0003-Excito-B2-UBoot-workaround.patch](https://github.com/sakaki-/gentoo-b2-kernel-patches/blob/master/0003-Excito-B2-UBoot-workaround.patch) | The B2's 'stock' settings for its [U-Boot](http://www.denx.de/wiki/U-Boot) bootloader will *not* allow the use of a standard-issue 4.0 kernel (even with most components modularized), as it is too large to fit in the load area given. This patch provides the necessary assembly-code shims to work around this restriction, *without* requiring users to flash new settings to their device (which can be dangerous). An appropriate [flattened image tree](http://www.denx.de/wiki/pub/U-Boot/Documentation/multi_image_booting_scenarios.pdf) source file is also provided, for use when booting from USB. For more details, please refer to my [gentoo-on-b2](https://github.com/sakaki-/gentoo-on-b2) project (where scripts to compile the shimmed kernel may also be found).
[0004-Ath-regd-optional.patch](https://github.com/sakaki-/gentoo-b2-kernel-patches/blob/master/0004-Ath-regd-optional.patch) | I do not have a WiFi variant of the B2, so am unsure if I am [cargo-culting](https://en.wikipedia.org/wiki/Cargo_cult_programming) by including this patch from the [community-b3-kernel](https://github.com/Excito/community-b3-kernel/tree/master/debian/patches) set. Nevertheless, for safety, here it is. Its purpose is to make the Atheros wireless drivers respect the user's regulatory domain setting, in preference to the version stored on the adaptor's EEPROM (which can unnecessarily lock out certain frequencies, power levels etc.). Provides the kconfig setting `CONFIG_ATH_USER_REGD`, which you must select to turn this functionality on.
[0005-vsc8601-and-clockskewfix.patch](https://github.com/sakaki-/gentoo-b2-kernel-patches/blob/master/0004-Ath-regd-optional.patch) | Contains a modification for the Vitesse Ethernet phy driver, to allow it to support the vsc8601 component used on the B2. Also provides a kconfig item, `VITESSE_PHY_8601_SKEW`, which you need to enable to turn skew timing on. I have reworked the original patch in the [community-b3-kernel](https://github.com/Excito/community-b3-kernel/tree/master/debian/patches), which targets a 3.2.63 kernel, as the underlying file `drivers/net/phy/vitesse.c` has been significantly altered since then (with the result that the 3.2.63 patch will no longer apply).

> Please note that this patchset is provided without warranty. Use at your own risk.

## Installation

This patchset is most easily installed via its custom Gentoo ebuild, for which please see my [gentoo-b2-overlay](https://github.com/sakaki-/gentoo-b2-overlay) project.

> It has also been applied to the kernel used in my Gentoo live-USB for the B2 (available [here](https://github.com/sakaki-/gentoo-on-b2)).

However, there is nothing Gentoo-specific about this patchset, which may easily be applied to a vanilla 4.9.16 kernel source tree if you like, by means of the `patch` utility (see [these instructions](http://superuser.com/questions/324968/how-do-i-apply-a-patch-to-my-linux-kernel), for example).

> If you do this, please note that patches 0002 and 0003 should be applied at patchlevel 0 (this is for compatibility with Gentoo's `unipatch()`).

The patches here should also apply to later versions of the kernel, unless some of underlying code structure changes significantly.

## Feedback Welcome!

If you have any problems, questions or comments regarding this project, feel free to drop me a line! (sakaki@deciban.com)

