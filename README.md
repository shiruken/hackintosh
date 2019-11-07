# Hackintosh v3
Installation guide for my dual-boot Hackintosh v3 build running macOS Catalina and Windows 10. View [the build on PCPartPicker](https://pcpartpicker.com/list/kBK7TC).

* Intel Core i7-9700K
* Corsair H100i PRO
* Samsung 970 Evo 1 TB M.2 NVME SSD (macOS)
* Intel 660p Series 1 TB M.2 NVME SSD (Windows)
* Gigabyte Z390 AORUS PRO WIFI
* Corsair Vengeance RGB Pro 16 GB
* AMD Radeon RX 5700 XT
* Corsair RM (2019) 650 W 80+ Gold
* NZXT H510
* Dell S2719DGF 27" LED QHD FreeSync Monitor

## Prepare Install Media

1. Download the [macOS Catalina installer](https://apps.apple.com/us/app/macos-catalina/id1466841314?mt=12) (v10.15.1) from the Mac App Store
2. Format target USB drive [using Disk Utility](https://support.apple.com/en-us/HT208496) as `Mac OS Extended (Journaled)`
3. [Create the bootable macOS installer](https://support.apple.com/sl-si/HT201372): 

    `sudo /Applications/Install\ macOS\ Catalina.app/Contents/Resources/createinstallmedia --volume /Volumes/{Drive}`

## Install Clover

* Download [Clover Install Package](https://github.com/Dids/clover-builder/releases) (v2.5k_r5097)
* Download [Clover Configurator Global Edition](http://mackie100projects.altervista.org/download-clover-configurator/) (v5.7.0.0)
* Install Clover to the USB device and customize with the following options:
  * Clover for UEFI booting only
  * Install Clover in the ESP
  * UEFI Drivers
    * Recommended drivers
      * ApfsDriverLoader
      * AptioMemoryFix
      * HFSPlus
    * FileVault 2 UEFI Drivers
      * AppleImageCodec
      * AppleKeyAggregator
      * AppleKeyFeeder
      * AppleUITheme
      * FirmwareVolume
    * Additional drivers
      * EmuVariableUefi
 
 _Note: Clover should install to `EFI/CLOVER/drivers/UEFI/`_

## Gather Kexts

* [AppleALC.kext](https://github.com/acidanthera/AppleALC/releases) (v1.4.3)
* [IntelMausiEthernet.kext](https://onedrive.live.com/?authkey=%21APjCyRpzoAKp4xs&id=FE4038DA929BFB23%21455134&cid=FE4038DA929BFB23) (v2.5.0)
* [Lilu.kext](https://github.com/acidanthera/Lilu/releases) (v1.3.9)
* [USBInjectAll.kext](https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads/) (v0.7.1)
* [VirtualSMC.kext](https://github.com/acidanthera/VirtualSMC/releases) (v1.0.9)
  * SMCProcessor.kext
  * SMCSuperIO.kext
* [WhateverGreen.kext](https://github.com/acidanthera/WhateverGreen/releases) (v1.3.4)

Unzip the archives and copy the .kexts to `EFI/CLOVER/kexts/Other/` the EFI partition of the USB device.

Copy VirtualSmc.efi to `EFI/Clover/Drivers/`.

## BIOS Settings

* Load Optimized Defaults
* Disable VT-d
* Disable CFG-Lock
* Disable Secure Boot Mode
* Set OS Type to `Windows 8/10`
* Set XHCI Handoff to Enabled
* Disable Serial Port (if available)

## Install macOS Catalina

1. Restart computer and select USB drive as boot drive
2. Install macOS to desired internal drive
3. As system restarts, keep selecting `Boot macOS Install from﻿ Macintosh` from Clover menu
4. Once installation has completed, boot again from the USB drive and install Clover to the internal system drive and copy the EFI folder from the USB drive.

## References

* [u/corpnewt's Vanilla Guide for Intel](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/)
* [Glasgood's macOS Mojave [SUCCESS][GUIDE] for Aorus Z390 Pro](https://www.insanelymac.com/forum/topic/337837-glasgoods-macos-mojave-successguide-for-aorus-z390-pro/)
* [[SUCCESS] Gigabyte Designare Z390 (Thunderbolt 3) + i7-9700K + AMD RX 580](https://www.tonymacx86.com/threads/success-gigabyte-designare-z390-thunderbolt-3-i7-9700k-amd-rx-580.267551/)

https://www.reddit.com/r/hackintosh/comments/djh98y/hackintosh_gigabyte_z390_pro_i99900k_rx_590/
https://www.reddit.com/r/hackintosh/comments/bb3hy5/build_i9_9900k_gigabyte_z390_aorus_pro_wifi/
