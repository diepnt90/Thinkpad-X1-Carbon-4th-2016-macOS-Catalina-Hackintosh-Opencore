# Thinkpad X1 Carbon 4th 2016 running macOS (OpenCore bootloader)

> First OpenCore build for X1C4 ever


## Introduction

### General knowledge & credits

* [Why OpenCore](https://dortania.github.io/OpenCore-Install-Guide/why-oc.html)

* To install macOS follow the guides provided by [Dortania](https://dortania.github.io)

* Lots of SSDT patches from [OC-little](https://translate.google.it/translate?sl=zh-CN&tl=en&u=https%3A%2F%2Fgithub.com%2Fdaliansky%2FOC-little)

* [Acidanthera](https://github.com/acidanthera) that make this possible


### My Hardware

* Model: Thinkpad X1 Carbon Gen 4-2016
* Processor: Intel Core i5-6300U (2C, 2.6 / 3.4GHz, 4MB)vPro
* Graphics: Integrated Intel HD Graphics 520
* Memory: 8GB Soldered
* Display: 14" HD (1080P) IPS
* Sound Card: Conexant CX20753/4
* Storage: 256GB SSD M.2 NVME
* WLAN + Bluetooth: DW1560
* Camera: 720p
* Keyboard: Backlit
* Fingerprint Reader: Yes
* Battery: 3-cell (23Wh) + 3-cell (26Wh)


## What if I don't have this exact model?


This EFI will suit any X1C4 regardless of CPU model[^1] / RAM amount / Display resolution[^2] / Storage drive (SATA or NVMe[^3]).

[^1]: custom [CPU Power Management](#cpu-power-management) guide

[^2]: 1440p display models should change UIScale to 2 for better resolution while booting
```sh
NVRAM -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82 -> UIScale = 2
```

[^3]: Some NVMe drives may not work OOTB with MacOS, [NVMeFix](https://github.com/acidanthera/NVMeFix) could resolve some issues.

## Recommended changes

### USB ports map

Use one of the following methods if needed:

* [USBMap from CorpNewt](https://github.com/corpnewt?tab=repositories)

* [USBPorts from Hackintool](https://github.com/headkaze/Hackintool)

* [Native USB fix without injector kext](https://www.olarila.com/topic/6878-guide-native-usb-fix-for-notebooks-no-injectorkext-required/?tab=comments#comment-88412)

### CPU Power Management

If you want to take a step forward and create a custom CPU power profile, follow the steps below:

* Use [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend) to generate  a `.plist` file with PM data; (settings for i7-6600u):

```sh
Low Frequency Mode (LFM) = 800MHz #(TDP-down frequency for i7-6600u)
Energy Performance Preference (EPP) = 80 #(Balance power)
```
* via `ResourceConverter.sh` inside [CPUFriend](https://github.com/acidanthera/CPUFriend), select the `.plist` to generate either `CPUFriendDataProvider.kext` or `SSDT-DATA.dsl`;

* Load `CPUFriend.kext` and `CPUFriendDataProvider.kext` inside `EFI/OC/config.plist` or

* Alternatively combine `SSDT-DATA.dsl` data with `SSDT-PLUG` and load it with `CPUFriend.kext` inside `EFI/OC/config.plist`.

That's how power consumption looks like on my machine at idle state:


### True Macbook experience 

#### [Generate your own SMBIOS](https://github.com/corpnewt/GenSMBIOS)
```sh
run the script with MacbookPro13,1
add results to PlatformInfo > Generic > MLB, SystemSerialNumber and SystemUUID
```

#### Enable HiDPI with [RDM Utility](https://github.com/usr-sse2/RDM/releases)
```sh
install RDM Utility
open it, click on "resolution", then "edit"
for 2560x1440 screens I suggest using 1440x810 resolution
to accomplish that, use the settings below
```

### Other tweaks

Karabiner-Elements for remap F keyboard functions


#### Monitor temperatures and power consumption with [HWMonitor](https://github.com/kzlekk/HWSensors/releases)

This app is relatively old and no longer supported, but it gets the job done and has a nice simple look.

## Bios settings

* `Config` > `USB` > `UEFI BIOS Support` > **Enable**
* `Config` > `Power` > `Intel SpeedStep Technology` > **Enable**
* `Config` > `Power` > `CPU Power Management` > **Enable**
* `Config` > `CPU` > `Hyper-Threading Technology` > **Enable**
* `Security` > `Security Chip` > **Disable**
* `Security` > `Memory Protection`>`Execution Prevention`>**Enable**
* `Security` > `Virtualization` > `Intel Virtualization Technology` > **Enable**
* `Security` > `Virtualization` > `Intel VT-d Feature` > **Enable**
* `Security` > `Anti-Theft` > `Computrace` > `Current Setting` > **Disable**
* `Security` > `Secure Boot` > **Disable**
* `Security` > `Intel SGX` > **Disable**
* `Security` > `Device Guard` > **Disable**
* `Startup` > `UEFI/Legacy Boot` > **UEFI Only**
* `Startup` > `CSM Support` > **No**
* `Startup` > `Boot Mode` > **Quick**

## What's working ✔️

> Startup time from OC Picker to Desktop is 25s

- [x] CPU Power Management `~1W on IDLE`

- [x] Intel HD 520 Graphics `incuding graphics acceleration`

- [x] All USB ports `with custom kext or SSDT`

- [x] Internal camera `working fine on FaceTime, Skype, Webex and others`

- [x] Sleep / Wake / Shutdown / Reboot `with lid sernsor`

- [x] Intel Gigabit Ethernet

- [x] **[Wifi, Bluetooth, Airdrop, Handoff, Continuity, Sidecar wireless]**

- [x] iMessage, FaceTime, App Store, iTunes Store `Generate your own SMBIOS`

- [x] DRM support `iTunes Movies, Apple TV+, Amazon Prime, Netflix and others`

- [x] Speakers and headphones jack `fairly good volume`

- [x] Batteries `very stable and precise capacity tracking`

- [x] Trackpad, Trackpoint and physical buttons `two fingers swipe and three fingers gestures`

- [x] SIP and FileVault 2 can be enabled

- [x] miniDP and HDMI `with digital audio passthrough`

- [x] SD Card Reader `slow r/w speed but works`


## What's not working ⚠️

- [ ] Fingerprint Reader

- [ ] WWAN `not tested`

## Update tracker 🔄


| Item | Version |
| :--- | ---: |
| MacOS | 10.15.6 |
| OpenCore | 0.5.9 |
| Lilu | 1.4.5 |
| VirtualSMC | 1.1.4 |
| WhateverGreen | 1.4.0 |
| AppleALC | 1.5.0 |
| VoodooPS2Controller | 2.1.4 |
| VoodooInput | 1.0.5 |
| IntelMausi | 1.0.3 |


## Thanks to

The hackintosh community from GitHub, [InsanelyMac](https://www.insanelymac.com/forum/) and [r/hackintosh](https://www.reddit.com/r/hackintosh/).
