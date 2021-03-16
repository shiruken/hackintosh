# Hackintosh v3

Installation guide for my Hackintosh v3 build dual-booting macOS Big Sur and Windows 10. This build is based on [Dortania's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/). The previous version of this guide using the Clover bootloader can be found [here](). The version numbers reported in this guide were the releases available at the time of installation and more than likely can be replaced with the latest iteration.

* [EFI](EFI/): Copy of current EFI directory from macOS boot drive
* [EFI_install](EFI_install/): Copy of EFI directory from the USB drive used during installation


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

* To enable the iGPU (UHD 630) for headless compute tasks, set `AAPL,ig-platform-id=0300913E` and exclude the `framebuffer-patch-enable` and `framebuffer-stolenmem` properties under DeviceProperties.

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

Select **Save and Exit** to save the new BIOS settings and reboot


### Install macOS

1. Restart computer and set the USB drive as the default BIOS boot device
2. Select the `Install macOS Big Sur (external)` option from the OpenCore Boot Menu
3. Launch Disk Utility and format the destination drive (Samsung 970 Evo)
    * Name: `Macintosh SSD`
    * Format: `APFS`
    * Scheme: `GUID Partition Map`
4. Launch Install macOS and select the `Macintosh SSD` drive as the destination
    * As the system restarts, keep selecting `macOS Installer` from the OpenCore Boot Menu
5. Once the installation is complete, select `Macintosh SSD` from the OpenCore Boot Menu
    * Proceed through the normal macOS setup but delay signing into iCloud until post installation is complete


## Post Installation

Based heavily on [Dortania's OpenCore Post-Install Guide](https://dortania.github.io/OpenCore-Post-Install/).

### Make macOS Drive Bootable

1. Mount the EFI partition of `Macintosh SSD` and copy over the entire EFI directory from the USB drive
2. Restart the computer and select the internal drive (Samsung 970 Evo) as the default BIOS boot device
3. Select `Macintosh SSD` from the OpenCore Boot Menu
4. You should now have a bootable macOS installation!

_Note: You can now remove the USB drive but keep it handy for debugging issues with your Hackintosh._
