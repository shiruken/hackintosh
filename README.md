# Hackintosh v3
Installation guide for my dual-boot Hackintosh v3 build running macOS Catalina and Windows 10.

* `EFI`: Copy of EFI directory from the EFI partition of boot drive

## The Build

* **CPU:** Intel Core i7-9700K
* **CPU Cooler:** Corsair H100i PRO
* **Motherboard:** Gigabyte Z390 AORUS PRO WIFI
* **Memory:** Corsair Vengeance RGB Pro 16 GB DDR4-3600
* **Storage (macOS):** Samsung 970 Evo 1 TB M.2 NVME SSD
* **Storage (Windows):** Intel 660p Series 1 TB M.2 NVME SSD
* **Video Card:** Gigabyte Radeon RX 5700 XT 8 GB GAMING OC
* **Power Supply:** Corsair RM650 80+ Gold
* **Case:** NZXT H510
* **Monitor:** Dell S2719DGF 27" LED QHD FreeSync Monitor
* **Keyboard:** Das Keyboard Model S Professional
* **Mouse:** Logitech G603

View the build on PCPartPicker: https://pcpartpicker.com/list/kBK7TC

## Prepare Install Media

1. Download the [macOS Catalina installer](https://apps.apple.com/us/app/macos-catalina/id1466841314?mt=12) (v10.15.1) from the Mac App Store
2. Format target USB drive [using Disk Utility](https://support.apple.com/en-us/HT208496) as 'Mac OS Extended (Journaled)':

    `diskutil partitionDisk /dev/disk# GPT JHFS+ "USB" 100%`
    
3. [Create the bootable macOS installer](https://support.apple.com/sl-si/HT201372): 

    `sudo /Applications/Install\ macOS\ Catalina.app/Contents/Resources/createinstallmedia --volume /Volumes/USB`

## Install Clover

* Download [Clover Install Package](https://github.com/Dids/clover-builder/releases) (v2.5k_r5097) and [Clover Configurator Global Edition](http://mackie100projects.altervista.org/download-clover-configurator/) (v5.7.0.0)
* Install Clover to the USB device and customize with the following options:
  * Clover for UEFI booting only
  * Install Clover in the ESP
  * UEFI Drivers
    * Recommended drivers
      * ApfsDriverLoader
      * HFSPlus
    * Memory fix drivers
      * OsxAptioFix3Drv
    * FileVault 2 UEFI Drivers
      * AppleImageCodec
      * AppleKeyAggregator
      * AppleKeyFeeder
      * AppleUITheme
      * FirmwareVolume
    * Additional drivers
      * EmuVariableUefi
 
## Gather Kexts

* [AppleALC.kext](https://github.com/acidanthera/AppleALC/releases) (v1.4.3)
* [IntelMausiEthernet.kext](https://onedrive.live.com/?authkey=%21APjCyRpzoAKp4xs&id=FE4038DA929BFB23%21455134&cid=FE4038DA929BFB23) (v2.5.0)
* [Lilu.kext](https://github.com/acidanthera/Lilu/releases) (v1.3.9)
* [USBInjectAll.kext](https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads/) (v0.7.1)
* [VirtualSMC.kext](https://github.com/acidanthera/VirtualSMC/releases) (v1.0.9)
  * SMCProcessor.kext
  * SMCSuperIO.kext
* [WhateverGreen.kext](https://github.com/acidanthera/WhateverGreen/releases) (v1.3.4)

Unzip the archives and copy the .kexts to [`EFI/CLOVER/kexts/Other/`](EFI/CLOVER/kexts/Other/) on the EFI partition of the USB device.

Copy `VirtualSmc.efi` to [`EFI/CLOVER/drivers/UEFI/`](EFI/CLOVER/drivers/UEFI/).

## Configure Clover



## BIOS Settings (Version F10)

* Save & Exit
  * **Load Optimized Defaults**
* M.I.T.
  * Advanced Frequency Settings
    * Extreme Memory Profile (X.M.P.) → **Profile 1**
  * Smart Fan 5 Settings
    * Set AIO fan control to 'Full Speed' in 'Voltage' mode
* BIOS
  * Windows 8/10 Features → **Windows 8/10**
  * CSM Support → **Disabled**
* Peripherals
  * Initial Display Output → **IGFX**
  * Intel Platform Trust Technology (PTT) → **Disabled**
  * USB Configuration
    * Legacy USB Support → **Enabled**
    * XHCI Hand-off → **Enabled**
  * Network Stack Configuration
    * Network Stack → **Disabled**
* Chipset
  * Vt-d → **Disabled**
  * Internal Graphics → **Enabled**
  * DVMT Pre-Alloc → **64M**
  * DVMT Total Gfx Mem → **256M**
  * Audio Controller → **Enabled**
  * Above 4G Decoding → **Enabled**
* Power
  * ErP → **Disabled**
  * Soft-Off by PWR-BTTN → **Delay 4 Sec.**
  * RC6 (Render Standby) → **Enabled**
* Save & Exit
  * Choose **Save and Exit** to save BIOS settings and reboot

## Prepare for macOS Installation

1. Disconnect PCIe power cables from the graphics card (or completely remove the card)
2. Connect HDMI cable to integrated graphics output on motherboard
3. Insert macOS Installer USB drive into the USB 3.0 port adjacent to Ethernet connector
4. Connect keyboard and mouse to USB 2.0 ports

![Installation Connections](https://i.imgur.com/fODnRKV.png)

## Install macOS Catalina

1. Restart computer and select the USB drive as the default BIOS boot device 
2. Select `Install macOS Catalina` as the Clover boot volume 
3. Launch Disk Utility and format the destination drive to `Mac OS Extended (Journaled)`
    * Required for the installer to see an uninitialized drive (will be reformatted as APFS during installation)
2. Launch Install macOS and select the drive as the destination
    * As the system restarts, keep selecting `Boot macOS Install from {Drive}` from the Clover menu
    * If the system freezes, use the power button to shut down the computer and turn off the power supply. Wait a few minutes before restarting to continue the installation process.
4. Once the installation is complete, select `Boot macOS from {Drive}` from the Clover menu
    * Proceed through the normal macOS setup
    * If prompted, **do not sign into iCloud**
    
    
## References

* [u/corpnewt's Vanilla Guide for Intel](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/)
* [Glasgood's macOS Mojave [SUCCESS][GUIDE] for Aorus Z390 Pro](https://www.insanelymac.com/forum/topic/337837-glasgoods-macos-mojave-successguide-for-aorus-z390-pro/)
* [[SUCCESS] Gigabyte Designare Z390 (Thunderbolt 3) + i7-9700K + AMD RX 580](https://www.tonymacx86.com/threads/success-gigabyte-designare-z390-thunderbolt-3-i7-9700k-amd-rx-580.267551/)

https://www.reddit.com/r/hackintosh/comments/djh98y/hackintosh_gigabyte_z390_pro_i99900k_rx_590/
https://www.reddit.com/r/hackintosh/comments/bb3hy5/build_i9_9900k_gigabyte_z390_aorus_pro_wifi/
https://www.reddit.com/r/hackintosh/comments/dpu4by/general_z390_catalina_guide_or_why_you_should/
https://www.tonymacx86.com/threads/an-idiots-guide-to-lilu-and-its-plug-ins.260063/
