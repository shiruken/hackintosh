# Hackintosh v3

[![OpenCore Version](https://img.shields.io/badge/OpenCore-1.0.2-blue)](https://github.com/acidanthera/OpenCorePkg/releases/tag/1.0.2) [![macOS Version](https://img.shields.io/badge/macOS-15.0-blue)](https://support.apple.com/en-us/120283) [![ocvalidate status](https://img.shields.io/github/actions/workflow/status/shiruken/hackintosh/ocvalidate.yml?branch=master&label=ocvalidate)](https://github.com/shiruken/hackintosh/actions/workflows/ocvalidate.yml)

Installation guide for my Hackintosh v3 build dual-booting macOS Sequoia and Windows 10. This build is based on [Dortania's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/). The previous version of this guide using the Clover bootloader can be found [here](https://github.com/shiruken/hackintosh/tree/clover-final). The version numbers reported in this guide were the releases available at the time of installation and more than likely can be replaced with the latest iteration.

* [EFI](EFI/): Copy of current EFI directory from macOS boot drive
* [EFI_install](EFI_install/): Copy of EFI directory from the USB drive used during installation

![About This Mac](https://github.com/user-attachments/assets/5ef42c94-0dee-4df4-ac47-47d44a17325c)

[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/T6T21SSLE)

BTC: 3ELvsExgq8S24FdGtm4mupQvb3BwiHwWuB

## Table of Contents <!-- omit in toc -->

* [The Build](#the-build)
* [Installation](#installation)
  * [USB Creation](#usb-creation)
  * [OpenCore Configuration](#opencore-configuration)
  * [BIOS Settings (Version F12k)](#bios-settings-version-f12k)
  * [Install macOS](#install-macos)
* [Post Installation](#post-installation)
  * [Make macOS Drive Bootable](#make-macos-drive-bootable)
  * [Enable FileVault](#enable-filevault)
  * [Map USB Ports](#map-usb-ports)
  * [Enable Bluetooth and Wi-Fi](#enable-bluetooth-and-wi-fi)
  * [Enable OpenCore GUI](#enable-opencore-gui)
  * [Enable LauncherOption](#enable-launcheroption)
  * [Disable verbosity and debugging](#disable-verbosity-and-debugging)
* [Dual-Boot Windows](#dual-boot-windows)
  * [Install Boot Camp Utilities](#install-boot-camp-utilities)
* [Final BIOS Settings](#final-bios-settings)
* [Final OpenCore Configuration](#final-opencore-configuration)
* [Benchmarks](#benchmarks)
* [Issues](#issues)
* [Upgrade Log](#upgrade-log)
* [References](#references)
* [Resources](#resources)
* [Star History](#star-history)

## The Build

* **CPU:** Intel Core i7-9700K
* **CPU Cooler:** Corsair H100i PRO (Connected to `CPU_FAN` and `F_USB2`)
* **Motherboard:** Gigabyte Z390 AORUS PRO WIFI
* **Memory:** Corsair Vengeance RGB Pro 16 GB DDR4-3600
* **Storage (macOS):** Samsung 970 Evo 1 TB M.2 NVME SSD (`M2A` slot)
* **Storage (Windows):** Intel 660p Series 1 TB M.2 NVME SSD (`M2M` slot)
* **Video Card:** Gigabyte Radeon RX 5700 XT 8 GB GAMING OC
* **Power Supply:** Corsair RM650 80+ Gold
* **Case:** NZXT H510
* **Monitor:** Dell S2719DGF 27" LED QHD FreeSync Monitor
* **Keyboard:** Keychron K8
* **Mouse:** Logitech G603

View the build on PCPartPicker: https://pcpartpicker.com/list/kBK7TC

## Installation

### USB Creation

Follow the OpenCore Install Guide to [create the macOS installer](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/mac-install.html), [prepare the base OpenCore system](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/opencore-efi.html), and [gather the necessary files to boot macOS](https://dortania.github.io/OpenCore-Install-Guide/ktext.html). The drivers, kexts, and SSDTs I used are as follows:

* Drivers
  * [HfsPlus.efi](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus.efi)
  * OpenRuntime.efi (Included with OpenCore)
* Kexts
  * [AppleALC.kext](https://github.com/acidanthera/AppleALC/releases) (v1.5.8)
  * [IntelMausi.kext](https://github.com/acidanthera/IntelMausi/releases) (v1.0.5)
  * [Lilu.kext](https://github.com/acidanthera/Lilu/releases) (v1.5.1)
  * [USBInjectAll.kext](https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads/) (v0.7.1)
  * [VirtualSMC.kext](https://github.com/acidanthera/VirtualSMC/releases) (v1.2.1)
    * SMCProcessor.kext
    * SMCSuperIO.kext
  * [WhateverGreen.kext](https://github.com/acidanthera/WhateverGreen/releases) (v1.4.8)
* SSDTs (Prebuilt for Intel Desktop Coffee Lake)
  * [SSDT-PLUG-DRTNIA](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-PLUG-DRTNIA.aml)
  * [SSDT-EC-USBX-DESKTOP](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-EC-USBX-DESKTOP.aml)
  * [SSDT-AWAC](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-AWAC.aml)
  * [SSDT-PMC](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-PMC.aml)

### OpenCore Configuration

Follow the OpenCore Install Guide to [setup the initial config.plist file](https://dortania.github.io/OpenCore-Install-Guide/config.plist/) and [configure for Intel Desktop Coffee Lake](https://dortania.github.io/OpenCore-Install-Guide/config.plist/coffee-lake.html).

* To enable the iGPU (UHD 630) for headless compute tasks, set `AAPL,ig-platform-id=0300913E` and exclude the `framebuffer-patch-enable` and `framebuffer-stolenmem` properties.
* The Gigabyte Z390 AORUS PRO WIFI motherboard has supported disabling CFG-Lock since BIOS version F12j, which means you can disable the `AppleXcpmCfgLock` and `AppleXcpmCfgLock` quirks.
* If you already know the MAC address of your ethernet adapter, enter it under `PlatformInfo > Generic > ROM`. If you don't, this can be updated during post installation using System Preferences > Network > Ethernet > Advanced > Hardware > MAC Address to identify the correct value.

A sanitized version of my USB drive config file can be found [here](EFI_install/OC/config.plist).

### BIOS Settings (Version F12k)

Enter **Advanced Mode** and **Load Optimized Defaults** to reset the default BIOS settings. Modify the following settings (may vary depending on motherboard model and BIOS version):

* Tweaker
  * Advanced CPU Settings
    * Vt-d → **Disabled**
* Settings
  * IO Ports
    * Initial Display Output → **PCIe 1 Slot**
    * Internal Graphics → **Enabled**
    * DVMT Pre-Allocated → **64MB**
    * Above 4G Decoding → **Enabled**
    * USB Configuration
      * XHCI Hand-off → **Enabled**
    * SATA and RST Configuration
      * SATA Mode Selection → **ACHI**
  * Miscellaneous
    * Intel Platform Trust Technology (PTT) → **Disabled**
    * Software Guard Extensions (SGX) → **Disabled**
  * Smart Fan 5
    * CPU_FAN (or whichever header was used for the AIO CPU cooler)
      * Speed Control → **Full Speed**
* Boot
  * CFG Lock → **Disabled**
  * Fast Boot → **Disabled**
  * Windows 8/10 Features → **Windows 8/10**
  * CSM Support → **Disabled**
  * Secure Boot
    * Secure Boot Enable → **Disabled**

Select **Save and Exit** to save the new BIOS settings

### Install macOS

_I performed the installation with the USB drive, keyboard, and mouse plugged into the USB 2.0 ports at the top of the motherboard. My display was connected to the graphics card via DisplayPort._

1. Restart computer and set the USB drive as the default BIOS boot device
2. Select the `Install macOS (external)` option from the OpenCore Boot Menu
3. Launch Disk Utility and format the destination drive (Samsung 970 Evo)
    * Name: `Macintosh SSD`
    * Format: `APFS`
    * Scheme: `GUID Partition Map`
4. Launch Install macOS and select the `Macintosh SSD` drive as the destination
    * As the system restarts, keep selecting `macOS Installer` from the OpenCore Boot Menu
5. Once the installation is complete, select `Macintosh SSD` from the OpenCore Boot Menu and proceed through the normal macOS setup

## Post Installation

Based heavily on [Dortania's OpenCore Post-Install Guide](https://dortania.github.io/OpenCore-Post-Install/).

### Make macOS Drive Bootable

1. Mount the EFI partition of `Macintosh SSD` and copy over the entire EFI directory from the USB drive
2. Restart the computer and select the internal drive (Samsung 970 Evo) as the default BIOS boot device
3. Select `Macintosh SSD` from the OpenCore Boot Menu
4. You should now have a bootable macOS installation!

_Note: You can now remove the USB drive but keep it handy for debugging issues with your Hackintosh._

### Enable FileVault

[FileVault](https://support.apple.com/en-us/HT204837) is used to encrypt the startup disk on your Hackintosh. Enabling it is entirely optional, but probably a good idea for the security conscious. Before turning on the feature, you will need to make sure that OpenCore is properly configured to interact with the encrypted drive. Follow the OpenCore Post-Install Guide to [prepare your config.plist file for use with FileVault](https://dortania.github.io/OpenCore-Post-Install/universal/security/filevault.html).

The following (optional) changes were made:

* `Misc > Boot > PollAppleHotKeys` → `True`
* `Misc > Security > AuthRestart` → `True`

You can now enable FileVault in Security & Privacy in System Preferences like on a real Mac. Once the encryption process is complete, your account password will be required to decrypt the startup disk every time your Hackintosh starts up.

_Note: You should also make these changes to your USB drive OpenCore configuration so that it can properly boot your system if the `Macintosh SSD` EFI partition gets messed up. If you don't update the configuration, then the OpenCore bootloader may not be able to properly handle the FileVault-encrypted drive._

![FileVault Enabled](https://user-images.githubusercontent.com/867617/111683525-fb719c80-87fb-11eb-8cfd-ecef06eb7ff3.png)

### Map USB Ports

Follow the OpenCore Post-Install Guide to [map USB on your system](https://dortania.github.io/OpenCore-Post-Install/usb/#macos-and-the-15-port-limit). The complete USB port layout for the Gigabyte Z390 AORUS PRO WIFI motherboard is detailed in the image below with the ports I enabled indicated in red. If you have the same motherboard and want to use this _exact_ USB port mapping, you can download my [`USBMap.kext`](https://github.com/shiruken/hackintosh/files/6159668/USBMap.kext.zip).

* No ACPI patches were necessary to rename mappings
* Disabling the internal USB 2.0 headers (`HS11`) can prevent sleep issues caused by the AIO controller
* If you don't need Bluetooth/Wi-Fi, you can disable `HS14` and enable one of the disabled ports
* You can disable the `XhciPortLimit` quirk in your OpenCore configuration once complete

*Note: I [modified](https://github.com/shiruken/hackintosh/commit/c377e06a82345abf259e6632ad519ca6edc3a19e) the USB mapping on 2024-08-14 after the front panel Type-A port was damaged. I disabled HS10 and SS10 and enabled HS01 to allow for full functionality of the front panel Type-C port.*

![Gigabyte Z390 AORUS PRO WIFI USB Port Map](https://github.com/user-attachments/assets/6b24c2d7-8eb1-4f3f-a17d-d879a3ae9ee3)

### Enable Bluetooth and Wi-Fi

The Intel CNVi modules that provide integrated Bluetooth and Wi-Fi functionality on motherboards are not natively supported by macOS but can be enabled using [IntelBluetoothFirmware](https://github.com/OpenIntelWireless/IntelBluetoothFirmware) and [itlwm](https://github.com/OpenIntelWireless/itlwm) on supported devices. The Gigabyte Z390 AORUS PRO WIFI contains a compatible [Intel Wireless-AC 9560](https://www.intel.com/content/www/us/en/products/wireless/wireless-products/dual-band-wireless-ac-9560.html) CNVi (Vendor ID: `0x8087`, Device ID: `0x0AAA`). Hackintool can be used to determine the specific model on your motherboard (System > Peripherals > Bluetooth).

In order to use these kexts, you _must_ must enable the internal USB port used by the CNVi module during [USB mapping](#map-usb-ports). Download the latest [`IntelBluetoothFirmware.kext`](https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases) and [`itlwm.kext`](https://github.com/OpenIntelWireless/itlwm/releases), place in your `EFI/OC/Kexts` directory, and add to your OpenCore configuration. Reboot and you should be able to use both Bluetooth and Wi-Fi on your Hackintosh without any additional hardware.

* `IntelBluetoothInjector.kext` seems necessary to turn Bluetooth on/off
* `itlwm` shows up as an Ethernet adapter and requires use of the standalone app [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases) to connect/disconnect from Wi-Fi networks. You can Option-click on the menu bar icon and select "Launch at Login" to have it automatically start.

![BluetoothWiFi](https://user-images.githubusercontent.com/867617/111395470-491ec580-8693-11eb-9c03-c5d85888c4b4.png)

### Enable OpenCore GUI

Follow the OpenCore Post-Install Guide to [set up the GUI for the bootloader](https://dortania.github.io/OpenCore-Post-Install/cosmetic/gui.html#setting-up-opencore-s-gui). I also removed auxiliary entries (macOS Recovery and Reset NVRAM) from the picker.

* `Misc > Boot > HideAuxiliary` → `TRUE`

_Note: The auxiliary entries can still be accessed from the GUI by pressing the spacebar key_.

![OpenCore Bootloader GUI](https://user-images.githubusercontent.com/867617/111563406-4ba21d80-876e-11eb-9dcf-58ce0e8d0d3d.png)

### Enable LauncherOption

Follow the OpenCore Post-Install Guide to [run OpenCore directly from firmware without requiring a launcher](https://dortania.github.io/OpenCore-Post-Install/multiboot/bootstrap.html). This will add OpenCore to the motherboard boot menu and prevent issues where Windows or Linux could overwrite `EFI/BOOT/BOOTx64.efi`. _This file can now be safely removed._ Be sure to select OpenCore as the default boot option in your BIOS.

![OpenCore Selected as Default BIOS Boot Option](https://user-images.githubusercontent.com/867617/111656480-f0f5d980-87e0-11eb-8229-43c4277a67ca.png)

### Disable verbosity and debugging

Once your installation is complete and/or stable, you can disable verbose output and debug logging during booting. Follow the OpenCore Post-Install Guide to [declutter macOS and OpenCore](https://dortania.github.io/OpenCore-Post-Install/cosmetic/verbose.html).

* `Misc > Debug > AppleDebug` → `FALSE`
* `Misc > Debug > Target` → `3`
* Remove `-v` from `NVRAM > Add > 7C436110-AB2A-4BBB-A880-FE41995C9F82 > boot-args`

If you installed using the DEBUG version of OpenCore, [replace all the drivers]((https://github.com/acidanthera/OpenCorePkg/releases)) with the RELEASE versions.

## Dual-Boot Windows

I used [an existing installation of Windows 10](https://github.com/shiruken/hackintosh/tree/clover-final/#install-windows-10) on the Intel 660p Series NVMe drive. The steps taken during that installation process (removing macOS drive and placing Windows drive in `M2M` motherboard slot) should be unnecessary when [LauncherOption is enabled](#enable-launcheroption) since Windows will not be able to mess up the OpenCore EFI. For more information, check out the [Multiboot with OpenCore Guide](https://dortania.github.io/OpenCore-Multiboot/).

One of the benefits of OpenCore is that you can now use Startup Disk to reboot your system directly into Windows without further user input, just like on a real Mac.

![Startup Disk](https://user-images.githubusercontent.com/867617/111683602-147a4d80-87fc-11eb-8782-6b6ca1809126.png)

### Install Boot Camp Utilities

In order to return to macOS from Windows without requiring user input during boot, Apple's [Windows Support Software](https://support.apple.com/en-us/HT204923) must be installed. The OpenCore Post-Install Guide includes [instructions on using a third-party tool to download these drivers](https://dortania.github.io/OpenCore-Post-Install/multiboot/bootcamp.html). However, they can also be directly downloaded on macOS using Boot Camp Assistant.

1. Launch Boot Camp Assistant and select the `Action > Download Windows Support Software` menu item

2. Copy the downloaded files to a Windows-compatible USB drive and reboot to Windows

3. Remove all unnecessary Boot Camp drivers:
   * All folders in `$WinPEDriver$` (keep the parent folder)
   * All folders  in `BootCamp/Drivers/` **except** `Apple/`
   * All folders/files in `BootCamp/Drivers/Apple/` **except** `BootCamp.msi`
   
   <br/>
   
   ![Remaining Boot Camp Files](https://user-images.githubusercontent.com/867617/111693344-e3534a80-8806-11eb-8442-510c9500b299.png)

4. Run `BootCamp/Setup.exe` to install the Boot Camp software.

5. You can now use the Boot Camp Control Panel on the taskbar to restart directly into macOS without requiring further user input, just like on a real Mac running Boot Camp.

![Boot Camp Assistant and Control Panel](https://user-images.githubusercontent.com/867617/111683749-44c1ec00-87fc-11eb-8932-c747264443bc.png)

## Final BIOS Settings

Screenshots of my current BIOS settings on my working system

<details><summary>Tweaker</summary>
  <img src="https://user-images.githubusercontent.com/867617/111695882-f3b8f480-8809-11eb-9d3e-0c7eec4c5dc2.png">

  Advanced CPU Settings
  <img src="https://user-images.githubusercontent.com/867617/111695996-1c40ee80-880a-11eb-9275-208e580bb897.png">
  
</details>

<details><summary>Settings</summary>

  Platform Power
  <img src="https://user-images.githubusercontent.com/867617/111696154-57432200-880a-11eb-90f9-28fd7be001e8.png">

  IO Ports
  <img src="https://user-images.githubusercontent.com/867617/111696210-6e820f80-880a-11eb-9443-f5274866cb9c.png">

  USB Configuration
  <img src="https://user-images.githubusercontent.com/867617/111696305-8ce80b00-880a-11eb-8d23-fa0d3067d0c3.png">

  SATA and RST Configuration
  <img src="https://user-images.githubusercontent.com/867617/111696420-ab4e0680-880a-11eb-8044-d4bbb67862aa.png">

  Miscellaneous
  <img src="https://user-images.githubusercontent.com/867617/111696524-c882d500-880a-11eb-9040-375805b380c5.png">

</details>

<details><summary>Boot</summary>
  <img src="https://user-images.githubusercontent.com/867617/111696792-1992c900-880b-11eb-8d6f-e890584882da.png">
</details>

<details><summary>Smart Fan 5 Settings</summary>
  <img src="https://user-images.githubusercontent.com/867617/111696688-fd8f2780-880a-11eb-9851-eda0642ca126.png">
</details>

## Final OpenCore Configuration

A sanitized version of my working config file can be found [here](EFI/OC/config.plist).

## Benchmarks

_All values are the average of three runs_

* [Geekbench 5.4](https://www.geekbench.com/)
  * Single Core: 1253
  * Multicore: 7598
  * OpenCL: 66013
  * Metal: 71480
* [LuxMark v3 LuxBall HDR](http://luxmark.info/): 27444
* [Cinebench R23 (Multi-core)](https://www.maxon.net/en/cinebench): 9205
* [BruceX (Final Cut Pro 10.5.2)](https://blog.alex4d.com/2013/10/30/brucex-a-new-fcpx-benchmark/): 6.40 seconds
* [Blackmagic Disk Speed Test](https://apps.apple.com/us/app/blackmagic-disk-speed-test/id425264550) (Samsung 970 Evo)
  * Read: 2996 MB/s
  * Write: 2547 MB/s

## Issues

[See the GitHub repository issues tracker](https://github.com/shiruken/hackintosh/issues)

## Upgrade Log

* 2024-10-08: Updated to [OpenCore 1.0.2](https://github.com/acidanthera/OpenCorePkg/releases/tag/1.0.2), [Lilu 1.6.9](https://github.com/acidanthera/Lilu/releases/tag/1.6.9), [WhateverGreen 1.6.8](https://github.com/acidanthera/WhateverGreen/releases/tag/1.6.8), [VirtualSMC 1.3.4](https://github.com/acidanthera/VirtualSMC/releases/tag/1.3.4), [AppleALC 1.9.2](https://github.com/acidanthera/AppleALC/releases/tag/1.9.2), and `BlueToolFixup.kext` from [BrcmPatchRAM 2.6.9](https://github.com/acidanthera/BrcmPatchRAM/releases/tag/2.6.9)
* 2024-09-29: Updated to macOS 15.0
* 2024-08-15: Updated to macOS 14.6.1, [OpenCore 1.0.1](https://github.com/acidanthera/OpenCorePkg/releases/tag/1.0.1), [Lilu 1.6.8](https://github.com/acidanthera/Lilu/releases/tag/1.6.8), [WhateverGreen 1.6.7](https://github.com/acidanthera/WhateverGreen/releases/tag/1.6.7), [VirtualSMC 1.3.3](https://github.com/acidanthera/VirtualSMC/releases/tag/1.3.3), and [AppleALC 1.9.1](https://github.com/acidanthera/AppleALC/releases/tag/1.9.1)
* 2024-08-14: Modified the [USB mapping](#map-usb-ports) after the front panel Type-A port was damaged. HS10 and SS10 were disabled and HS01 was enabled to allow for full functionality of the front panel Type-C port.
* 2024-06-03: Updated to macOS 14.5 and and [itlwm 2.3.0](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.3.0)
* 2024-05-15: Updated to [OpenCore 1.0.0](https://github.com/acidanthera/OpenCorePkg/releases/tag/1.0.0), [AppleALC 1.9.0](https://github.com/acidanthera/AppleALC/releases/tag/1.9.0), and [itlwm 2.3.0-alpha](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.3.0-alpha)
* 2024-04-02: Updated to macOS 14.4.1, [OpenCore 0.9.9](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.9.9), and [itlwm 2.3.0-alpha](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.3.0-alpha)
* 2024-02-27: Updated to [itlwm 2.3.0-alpha](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.3.0-alpha) and [IntelBluetoothFirmware 2.4.0](https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases/tag/v2.4.0)
* 2024-02-16: Updated to macOS 14.3.1, [OpenCore 0.9.8](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.9.8), [AppleALC 1.8.9](https://github.com/acidanthera/AppleALC/releases/tag/1.8.9), and [itlwm 2.3.0-alpha](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.3.0-alpha)
* 2024-02-01: Updated to macOS 14.3
* 2024-01-31: Replaced thermal paste on GPU after increasingly encountering stability issues while gaming. Stress testing with [OCCT](https://www.ocbase.com/) revealed the card was immediately and continuously thermal throttling under load and eventually (hard) crashing the system. Used [Noctua NT-H2](https://noctua.at/en/nt-h2-3-5g) as the replacement thermal paste. Follow-up OCCT stress testing showed the GPU was no longer thermal throttling and performing as expected.
* 2023-12-19: Updated to macOS 14.2.1, [OpenCore 0.9.7](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.9.7), and [AppleALC 1.8.8](https://github.com/acidanthera/AppleALC/releases/tag/1.8.8)
* 2023-12-02: Updated to macOS 14.1.2
* 2023-11-27: Updated to macOS 14.1.1, [OpenCore 0.9.6](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.9.6), and [AppleALC 1.8.7](https://github.com/acidanthera/AppleALC/releases/tag/1.8.7)
* 2023-11-02: Updated to macOS 14.1, [AppleALC 1.8.6](https://github.com/acidanthera/AppleALC/releases/tag/1.8.6), and [itlwm 2.3.0-alpha](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.3.0-alpha)
* 2023-10-13: Updated to macOS 14.0
* 2023-10-02: Updated to macOS 13.6, [OpenCore 0.9.5](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.9.5), [AppleALC 1.8.5](https://github.com/acidanthera/AppleALC/releases/tag/1.8.5), and [itlwm 2.3.0-alpha](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.3.0-alpha)
* 2023-09-08: Updated to macOS 13.5.2
* 2023-09-05: Updated to macOS 13.5.1, [OpenCore 0.9.4](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.9.4), [Lilu 1.6.7](https://github.com/acidanthera/Lilu/releases/tag/1.6.7), [WhateverGreen 1.6.6](https://github.com/acidanthera/WhateverGreen/releases/tag/1.6.6), [AppleALC 1.8.4](https://github.com/acidanthera/AppleALC/releases/tag/1.8.4), [itlwm 2.3.0-alpha](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.3.0-alpha), [IntelBluetoothFirmware 2.3.0](https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases/tag/v2.3.0), and `BlueToolFixup.kext` from [BrcmPatchRAM 2.6.8](https://github.com/acidanthera/BrcmPatchRAM/releases/tag/2.6.8)
* 2023-07-03: Updated to macOS 13.4.1, [OpenCore 0.9.3](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.9.3), [Lilu 1.6.6](https://github.com/acidanthera/Lilu/releases/tag/1.6.6), [WhateverGreen 1.6.5](https://github.com/acidanthera/WhateverGreen/releases/tag/1.6.5), [VirtualSMC 1.3.2](https://github.com/acidanthera/VirtualSMC/releases/tag/1.3.2), [AppleALC 1.8.3](https://github.com/acidanthera/AppleALC/releases/tag/1.8.3), and `BlueToolFixup.kext` from [BrcmPatchRAM 2.6.7](https://github.com/acidanthera/BrcmPatchRAM/releases/tag/2.6.7)
* 2023-06-06: Updated to macOS 13.4 (required [this fix](https://github.com/OpenIntelWireless/IntelBluetoothFirmware/issues/435#issuecomment-1562560909) for Bluetooth issues) and [itlwm 2.2.0](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.2.0)
* 2023-05-19: Updated to [OpenCore 0.9.2](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.9.2), [Lilu 1.6.5](https://github.com/acidanthera/Lilu/releases/tag/1.6.5), [AppleALC 1.8.2](https://github.com/acidanthera/AppleALC/releases/tag/1.8.2), [itlwm 2.2.0-alpha](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.2.0-alpha), and `BlueToolFixup.kext` from [BrcmPatchRAM 2.6.6](https://github.com/acidanthera/BrcmPatchRAM/releases/tag/2.6.6)
* 2023-04-15: Updated to macOS 13.3.1, [OpenCore 0.9.1](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.9.1), [AppleALC 1.8.1](https://github.com/acidanthera/AppleALC/releases/tag/1.8.1), and `BlueToolFixup.kext` from [BrcmPatchRAM 2.6.5](https://github.com/acidanthera/BrcmPatchRAM/releases/tag/2.6.5)
* 2023-03-29: Updated to [OpenCore 0.9.0](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.9.0), [Lilu 1.6.4](https://github.com/acidanthera/Lilu/releases/tag/1.6.4), [VirtualSMC 1.3.1](https://github.com/acidanthera/VirtualSMC/releases/tag/1.3.1), and [AppleALC 1.8.0](https://github.com/acidanthera/AppleALC/releases/tag/1.8.0)
* 2023-02-18: Updated to macOS 13.2.1, [OpenCore 0.8.9](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.8.9), [WhateverGreen 1.6.4](https://github.com/acidanthera/WhateverGreen/releases/tag/1.6.4), [AppleALC 1.7.9](https://github.com/acidanthera/AppleALC/releases/tag/1.7.9), and [itlwm 2.2.0-alpha](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.2.0-alpha)
* 2023-01-04: Updated to [OpenCore 0.8.8](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.8.8), [Lilu 1.6.3](https://github.com/acidanthera/Lilu/releases/tag/1.6.3), [WhateverGreen 1.6.3](https://github.com/acidanthera/WhateverGreen/releases/tag/1.6.3), [AppleALC 1.7.8](https://github.com/acidanthera/AppleALC/releases/tag/1.7.8), and [itlwm 2.2.0-alpha](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.2.0-alpha)
* 2022-12-17: Updated to macOS 13.1, [OpenCore 0.8.7](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.8.7), [WhateverGreen 1.6.2](https://github.com/acidanthera/WhateverGreen/releases/tag/1.6.2), [AppleALC 1.7.7](https://github.com/acidanthera/AppleALC/releases/tag/1.7.7), and [itlwm 2.2.0-alpha](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.2.0-alpha)
* 2022-11-12: Updated to macOS 13.0.1, [OpenCore 0.8.6](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.8.6) and [AppleALC 1.7.6](https://github.com/acidanthera/AppleALC/releases/tag/1.7.6)
* 2022-10-15: Updated to macOS 12.6, [OpenCore 0.8.5](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.8.5), [itlwm 2.2.0-alpha](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.2.0-alpha), and `BlueToolFixup.kext` from [BrcmPatchRAM 2.6.4](https://github.com/acidanthera/BrcmPatchRAM/releases/tag/2.6.4)
* 2022-09-08: Updated to [OpenCore 0.8.4](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.8.4), [AppleALC 1.7.5](https://github.com/acidanthera/AppleALC/releases/tag/1.7.5), [itlwm 2.2.0-alpha](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.2.0-alpha), and [IntelBluetoothFirmware 2.2.0](https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases/tag/v2.2.0)
* 2022-08-06: Updated to macOS 12.5, [OpenCore 0.8.3](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.8.3), [Lilu 1.6.2](https://github.com/acidanthera/Lilu/releases/tag/1.6.2), [WhateverGreen 1.6.1](https://github.com/acidanthera/WhateverGreen/releases/tag/1.6.1), and [AppleALC 1.7.4](https://github.com/acidanthera/AppleALC/releases/tag/1.7.4)
* 2022-07-10: Updated to [OpenCore 0.8.2](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.8.2), [Lilu 1.6.1](https://github.com/acidanthera/Lilu/releases/tag/1.6.1), [WhateverGreen 1.6.0](https://github.com/acidanthera/WhateverGreen/releases/tag/1.6.0), [VirtualSMC 1.3.0](https://github.com/acidanthera/VirtualSMC/releases/tag/1.3.0), [AppleALC 1.7.3](https://github.com/acidanthera/AppleALC/releases/tag/1.7.3), and `BlueToolFixup.kext` from [BrcmPatchRAM 2.6.3](https://github.com/acidanthera/BrcmPatchRAM/releases/tag/2.6.3)
* 2022-06-12: Updated to macOS 12.4, [OpenCore 0.8.1](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.8.1), [WhateverGreen 1.5.9](https://github.com/acidanthera/WhateverGreen/releases/tag/1.5.9), [AppleALC 1.7.2](https://github.com/acidanthera/AppleALC/releases/tag/1.7.2), and `BlueToolFixup.kext` from [BrcmPatchRAM 2.6.2](https://github.com/acidanthera/BrcmPatchRAM/releases/tag/2.6.2)
* 2022-05-03: Updated to [OpenCore 0.8.0](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.8.0) and [AppleALC 1.7.1](https://github.com/acidanthera/AppleALC/releases/tag/1.7.1)
* 2022-04-12: Updated to macOS 12.3.1
* 2022-03-10: Updated to macOS 12.2.1, [OpenCore 0.7.9](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.7.9), [WhateverGreen 1.5.8](https://github.com/acidanthera/WhateverGreen/releases/tag/1.5.8), [VirtualSMC 1.2.9](https://github.com/acidanthera/VirtualSMC/releases/tag/1.2.9), and [AppleALC 1.7.0](https://github.com/acidanthera/AppleALC/releases/tag/1.7.0)
* 2022-02-15: Updated to [OpenCore 0.7.8](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.7.8), [Lilu 1.6.0](https://github.com/acidanthera/Lilu/releases/tag/1.6.0), [WhateverGreen 1.5.7](https://github.com/acidanthera/WhateverGreen/releases/tag/1.5.7), and [AppleALC 1.6.9](https://github.com/acidanthera/AppleALC/releases/tag/1.6.9)
* 2022-01-13: Updated to [OpenCore 0.7.7](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.7.7), [Lilu 1.5.9](https://github.com/acidanthera/Lilu/releases/tag/1.5.9), [WhateverGreen 1.5.6](https://github.com/acidanthera/WhateverGreen/releases/tag/1.5.6), and [AppleALC 1.6.8](https://github.com/acidanthera/AppleALC/releases/tag/1.6.8)
* 2022-01-07: Updated to macOS 12.1, [IntelBluetoothFirmware 2.1.0](https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases/tag/v2.1.0), and [itlwm 2.1.0](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.1.0)
* 2021-12-10: Updated to [OpenCore 0.7.6](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.7.6), [Lilu 1.5.8](https://github.com/acidanthera/Lilu/releases/tag/1.5.8), [VirtualSMC 1.2.8](https://github.com/acidanthera/VirtualSMC/releases/tag/1.2.8), and [AppleALC 1.6.7](https://github.com/acidanthera/AppleALC/releases/tag/1.6.7)
* 2021-12-02: Updated to [OpenCore 0.7.5](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.7.5), [Lilu 1.5.7](https://github.com/acidanthera/Lilu/releases/tag/1.5.7), [WhateverGreen 1.5.5](https://github.com/acidanthera/WhateverGreen/releases/tag/1.5.5), and [AppleALC 1.6.6](https://github.com/acidanthera/AppleALC/releases/tag/1.6.6)
* 2021-11-02: Updated `BlueToolFixup.kext` from [BrcmPatchRAM 2.6.1](https://github.com/acidanthera/BrcmPatchRAM/releases/tag/2.6.1)
* 2021-10-29: Updated to macOS 12.0.1, [OpenCore 0.7.4](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.7.4), [WhateverGreen 1.5.4](https://github.com/acidanthera/WhateverGreen/releases/tag/1.5.4), and [AppleALC 1.6.5](https://github.com/acidanthera/AppleALC/releases/tag/1.6.5). Removed `IntelBluetoothInjector.kext` and added `BlueToolFixup.kext` from [BrcmPatchRAM 2.6.0](https://github.com/acidanthera/BrcmPatchRAM/releases/tag/2.6.0) to [resolve Bluetooth issues](https://openintelwireless.github.io/IntelBluetoothFirmware/FAQ.html#what-additional-steps-should-i-do-to-make-bluetooth-work-on-macos-monterey).
* 2021-09-24: Updated to macOS 11.6, [OpenCore 0.7.3](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.7.3), [Lilu 1.5.6](https://github.com/acidanthera/Lilu/releases/tag/1.5.6), [WhateverGreen 1.5.3](https://github.com/acidanthera/WhateverGreen/releases/tag/1.5.3), [VirtualSMC 1.2.7](https://github.com/acidanthera/VirtualSMC/releases/tag/1.2.7), [AppleALC 1.6.4](https://github.com/acidanthera/AppleALC/releases/tag/1.6.4), and [IntelBluetoothFirmware 2.0.1](https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases/tag/v2.0.1)
* 2021-08-11: Updated to macOS 11.5.2, [OpenCore 0.7.2](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.7.2), [Lilu 1.5.5](https://github.com/acidanthera/Lilu/releases/tag/1.5.5), [WhateverGreen 1.5.2](https://github.com/acidanthera/WhateverGreen/releases/tag/1.5.2), [VirtualSMC 1.2.6](https://github.com/acidanthera/VirtualSMC/releases/tag/1.2.6), [AppleALC 1.6.3](https://github.com/acidanthera/AppleALC/releases/tag/1.6.3), [IntelMausi 1.0.7](https://github.com/acidanthera/IntelMausi/releases/tag/1.0.7), [itlwm 2.0.0](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.0.0), and [IntelBluetoothFirmware 2.0.0](https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases/tag/v2.0.0)
* 2021-06-26: Updated to macOS 11.4, [OpenCore 0.7.0](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.7.0), [WhateverGreen 1.5.0](https://github.com/acidanthera/WhateverGreen/releases/tag/1.5.0), [VirtualSMC 1.2.4](https://github.com/acidanthera/VirtualSMC/releases/tag/1.2.4), [AppleALC 1.6.1](https://github.com/acidanthera/AppleALC/releases/tag/1.6.1), and [IntelBluetoothFirmware 1.1.3](https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases/tag/1.1.3)
* 2021-05-16: Updated to macOS 11.3.1, [OpenCore 0.6.9](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.6.9), [Lilu 1.5.3](https://github.com/acidanthera/Lilu/releases/tag/1.5.3), [VirtualSMC 1.2.3](https://github.com/acidanthera/VirtualSMC/releases/tag/1.2.3), [AppleALC 1.6.0](https://github.com/acidanthera/AppleALC/releases/tag/1.6.0), and [IntelMausi 1.0.6](https://github.com/acidanthera/IntelMausi/releases/tag/1.0.6)
* 2021-05-01: Updated to macOS 11.3
* 2021-04-24: Updated to [OpenCore 0.6.8](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.6.8), [Lilu 1.5.2](https://github.com/acidanthera/Lilu/releases/tag/1.5.2), [WhateverGreen 1.4.9](https://github.com/acidanthera/WhateverGreen/releases/tag/1.4.9), [VirtualSMC 1.2.2](https://github.com/acidanthera/VirtualSMC/releases/tag/1.2.2), [AppleALC 1.5.9](https://github.com/acidanthera/AppleALC/releases/tag/1.5.9), and [itlwm 1.3.0](https://github.com/OpenIntelWireless/itlwm/releases/tag/v1.3.0)

## References

* [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
* [OpenCore Post-Install Guide](https://dortania.github.io/OpenCore-Post-Install/)
* [WhateverGreen Intel® HD Graphics FAQs](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md)
* [DarkWake on macOS Catalina | boot args darkwake=8 & darkwake=10 are obsolete](https://www.insanelymac.com/forum/topic/342002-darkwake-on-macos-catalina-boot-args-darkwake8-darkwake10-are-obsolete/)
* [How to recreate Windows 10 EFI partition](https://www.tenforums.com/installation-upgrade/52837-moving-recreating-efi-partition.html#post698505)

## Resources

* [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg)
* [ProperTree](https://github.com/corpnewt/ProperTree)
* [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS)
* [USBMap](https://github.com/corpnewt/USBMap)
* [OCConfigCompare](https://github.com/corpnewt/OCConfigCompare)
* [gfxutil](https://github.com/acidanthera/gfxutil)
* [Hackintool](https://github.com/headkaze/Hackintool)

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=shiruken/hackintosh&type=Date)](https://star-history.com/#shiruken/hackintosh&Date)
