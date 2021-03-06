 What's this for? Not too many X299 hardware running macOS, let alone OpenCore so thought I'd post this ;p 
 Please use this as a base **and not a guide**. Inital setup please follow the [OpenCore Vanilla Desktop Guide](https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/)
 
 I also include a clover config as well in case you feel your issues are OpenCore based, please note that the OpenCore config is for 0.5.2. You'll need to update to support 
 
# What work and what doesn't

Works:
* Native NVRAM
* Bootcamp
* Sleep
* USB power(Including iPad Pro 12.9 Charging!)
* CPU Power Management
* iTunes and Netflix
* Handoff, AirDrop and all Apple services
* [Display Brightness and Volume with Apple Keyboard](https://github.com/the0neyouseek/MonitorControl/releases)
* CPU Name
   * Easy fix under `PlatformInfo->SMBIOS->ProcessorType->3841`
* macOS High Sierra, Mojave and Catalina


Doesn't work:

* Onboard Wifi: Won't work, I removed it and use a genuine Apple Airport BCM94360CD Card with PCIe x1 adapter.
* Onboard Bluetooth: Works inconsistently, replaced with BCM94360CD so didn't look into it, see [this thread](https://github.com/daliansky/XiaoMi-Pro-Hackintosh/issues/262) for some ideas.
* Sidecar: Well *kinda*, this is a common issue with Catalina and can sometimes be fixed by swapping board IDs to either an iMac19,1 or MacPro7,1 with the following boot flags:
   * `shikigva=40`: Needed for patching
   * `shiki-id=Mac-AA95B1DDAB278B95`: For iMac19,1 boardID
   * `shiki-id=Mac-27AD2F918AE68F61`: For MacPro7,1 boardID
 
 
# OpenCore Specifics

X299 config.plist specifics:
* `slide=N`
* `DisableIoMapper` set to `YES`
* Ethernet set to `built-in`
* `AppleRTC` kernel Patches(reboots into BIOS safemode)
* `layout-id` set to 11
* iMacPro1,1 or MacPro7,1 SMBIOS


X299 SSDTs specifics:
* `SSDT-EC-USBX-X299`: Powers off `EC0` controller and creates a fake EC just for macOS, needed for all Catalina users. [See here for more info](https://www.reddit.com/r/hackintosh/comments/den28t/whats_new_in_macos_catalina/)
* `SSDT-PLUG-X299`: Sets `Plugin-type=1` to `CP00`
* `SSDT-SBUS-MCHC`: Creates SMbus device and resolves issues with DeviceProperties injection for using SSDTs like `SSDT-XHCI`, `SSDT-XHCX` and `SSDT-SATA`
* `SSDT-XHCI`, `SSDT-XHCX` and `SSDT-SATA`: DeviceProperties injection, mostly cosmetic

X299 kexts specifics:

* `X299-Map`: Maps USB chipset ports, **please make your own as this is just an example**
* `AsMedia-Map`: Maps AsMedia 3.1 ports, both front and rear(front panel needs proper testing), **Please make your own as this is just an example**
* [`VoodooTSCSync`](https://github.com/RehabMan/VoodooTSCSync): Synchronize the TSC, can be fixed via OpenCore as well.


Other kexts that are needed regardless of system:

* [`VirtualSMC`](https://github.com/acidanthera/VirtualSMC)
* [`Lilu`](https://github.com/vit9696/Lilu/releases)
* [`AppleALC`](https://github.com/vit9696/AppleALC/releases)
* [`WhateverGreen`](https://github.com/acidanthera/WhateverGreen/releases)

Ethernet:

* [`IntelMausiEthernet`](https://github.com/Mieze/IntelMausiEthernet): For most intel controllers.
* [`I211-AT SmallTree kext`](https://cdn.discordapp.com/attachments/390417931659378688/556912824228773888/SmallTree-Intel-211-AT-PCIe-GBE.kext.zip): For newer chipsets are based off of I211-AT.
* [`AtherosE2200Ethernet`](https://github.com/Mieze/AtherosE2200Ethernet): Required for Atheros and Killer NICs
* [`RealtekRTL8111`](https://github.com/Mieze/RTL8111_driver_for_OS_X): Required for Realtek NICs


X299 Firmware Driver specifics:
* `UsbKbDxe.efi`: Some firmwares won't work with OpenCore's built-in drivers when using `PollAppleHotKeys`

# Hardware Specifics

Hardware:
* i9 7920X
* Noctua NH D15
* Asus Strix X299-E Gaming
* MSI AirBoost Vega 56 with AIO cooler
* WD Black SN750 1TB NVMe
* 32GB 2666Mhz (4x8GB)
* Apple BCM94360CD

Main important BIOS settings:

* Asus Multicore: Auto
* CPU Core Ratio: All Core Sync
* MSR Lock: Disabled
   * If can't disable, turn on `AppleCpuPmCfgLock` and `AppleXcpmCfgLock`. Without this, you won't go far for install.
* Legacy USB: Disabled
* Above 4G ecoding: Enabled
* CSM: Disabled
* OS Type: Windows UEFI
