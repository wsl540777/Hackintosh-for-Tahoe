# Hackintosh Tahoe Guide & EFI for Matebook 14 2020 ｜ 黑苹果 Tahoe 教程 & EFI for Matebook 14 2020

**12/25/25 Update: Tahoe now supports native Wi‑Fi drivers via patch (Intel & Broadcom) and partially supports AirDrop. This guide has been updated with slight changes to the installation process.**
## README.md
en-US English  
zh-CN 简体中文
## Overview
This Hackintosh guide provides a reference for fresh installing or upgrading to macOS Tahoe. It only shares my experience and does NOT provide troubleshooting for any specific installation issues.

**This guide ONLY applies to Intel CPUs + Intel Wi‑Fi cards. For AMD CPUs or Broadcom Wi‑Fi cards, please refer to other guides.**

Note: It is a known fact that `itlwm.kext` does not function in **macOS Recovery** because it lacks a client (like `Heliport`). Actually, **by modifying its configuration file in advance, the system can automatically connect to a specified Wi‑Fi after boot**. This approach effectively resolves the issue in Sequoia and later systems where `AirportItlwm.kext` is no longer supported, resulting in no wireless networking in **recovery system**.

With this method, the entire online installation process requires only an EFI and a macOS Recovery image (under 1.5 GB)—no need to search for full installers, download images, or create bootable media—making a fresh Tahoe installation easier and more convenient.

**Recovery online install vs. Recovery image restore**

**•**  **Recovery online install:** similar to Android’s Recovery Mode; once networked, it downloads and installs macOS directly from Apple servers.
**•**  **Recovery image restore:** similar to Ghost; restores a disk image made by someone else. 
## Target Machine for This EFI
| Item | Details |
|:----:|--------------------------------------------------------------|
| Model | HUAWEI Matebook 14 2020 (KLVC-WFE9L/WXX9) |
| CPU | Intel Core i7 10510U |
| RAM | Samsung 16 GB 2133 MHz LPDDR3 |
| GPU | Intel Comet Lake‑U GT2 (UHD Graphics 620) & NVIDIA GeForce MX350 |
| SSD | WD SN730 512G **(Not compatible with Samsung SSDs on this model)** |
| Audio | Realtek ALC256 |
| Wi‑Fi | Intel Wireless‑AC 9560 |
| Display | CHIMEI CMN8C02 (P140ZKA‑BZ1) @ 2160×1440 |
| BIOS | 1.31 |
## Compatibility of this EFI
### ✅ Supported
* CPU (Turbo Boost supported)
* Intel integrated graphics (spoofed as UHD 630; closer to native performance)
* Sleep and hibernation (fixed)
* USB 3.0/2.0
* Trackpad / Touchscreen (fixed)
* Wi‑Fi (WPA-Personal & WPA-Enterprise via injected patch)
* Bluetooth
* Audio: speakers / microphone / headphone output (via injected audio patch; audio input through headphone jack not supported)
* Siri
* AutoFill / Passkeys
* Wireless Continuity **(on the same LAN only)**: AirDrop (this Mac -> other Apple devices) / Handoff / Universal Clipboard (other Apple devices -> this Mac) / AirPlay / Calls and Texts / Verification codes
* Wired Continuity: Sidecar / Insert Sketch, Photos and Scans / Instant Hotspot
* Location Services / Find My
### ❌ Not supported
* HDMI (reason unknown; under investigation)
* Camera (workaround: access mobile camera via [Camo Studio](https://camo.com/studio))
* Fingerprint Sensor
* MX350 discrete GPU
* Continuity: Universal Control / Continuity Camera / iPhone Mirroring / Unlock with Apple Watch
* DRM Contents: Apple Music **(Only downloaded AAC 256 kbps file is playable)**/ Apple TV / Netflix / Amazon Prime / Disney+ etc.


## Tahoe Installation Guide (Intel)
### Preparation
1. ⚠️ **Back up your existing system and EFI partition.**  
2. Prepare at least one empty USB drive.  
3. Prepare EFI tailored for your model.  
4. Download [itlwm.kext](https://github.com/OpenIntelWireless/itlwm/releases/tag/v2.3.0) and Wi‑Fi driver patch package ([Intel version](https://github.com/wsl540777/Hackintosh-for-Tahoe/releases/download/v26.2.1/Tahoe_Intel_Wi-Fi_Patch.zip)).  
5. Use [OpenCore Auxiliary Tools (OCAT)](https://github.com/ic005k/OCAuxiliaryTools) or [OpenCore Configurator (OCC)](https://mackie100projects.altervista.org/category/opencore-configurator/) to generate the identifiers (Serial/UUID/MLB/ROM/Processor) (see [guide](https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html#using-macserial)), add `itlwm.kext` to `Kernel > Add`, and **ensure `AirportItlwm.kext` is absent or disabled**;  
6. **Configure network service (skip this if using wired network):** in File Explorer / Finder, locate `EFI > OC > Kext > itlwm.kext (Show Package Contents) > Contents > Info.plist`. Open with a text editor, find `WiFi_1`, modify the `ssid` and `password` string values to the Wi‑Fi **name** and **password** you want the computer to connect to, save and exit.
### Installation Process
* #### Fresh install from Windows:
  * #### Method 1: Official Recovery online install (only one USB drive required; no full installer image needed)
    1. **Prepare system partitions**
       1. ⚠️ **Enlarge the computer’s EFI partition to 300MB** (see [guide](https://www.shuzhiduo.com/A/pRdBN2n6dn/)). It’s recommended to create an external PE (Preinstallation Environment) system boot disk and resize under it (recommended [HotPE](https://www.hotpe.top/));  
       2. Create a macOS system partition (not less than 60GB; for long‑term use at least 160GB recommended). Filesystem type doesn’t matter now — it will be erased during install.
    2. **Prepare Recovery partition**
       1. Download [RecoveryOS.26.zip](https://github.com/wsl540777/Hackintosh-for-Tahoe/releases/download/v26.2.0/RecoveryBoot.26.zip) and extract the `com.apple.recovery.boot` folder;  
       2. On the USB drive create a 2GB FAT32 partition with GUID partition scheme and place `com.apple.recovery.boot` and the prepared `EFI` folder at the root.
    3. **Install using macOS Recovery**
       1. Reboot into BIOS settings and disable **Secure Boot** and **TPM / Security Chip** (for more options, see [this](https://apple.sqlsec.com/3-%E5%87%86%E5%A4%87%E5%B7%A5%E4%BD%9C/3-1/)); 
       2. Boot the recovery system from USB and in the OpenCore picker select **`Recovery 26.0 (Portable)`**;  
       3. In Recovery, open **Disk Utility** and erase the target macOS partition to APFS;  
       4. Exit Disk Utility and choose **Reinstall macOS Tahoe**. If you can see the software license/agreement, the previous network configuration is succeessful;  
       5. Select the erased system disk and follow the instructions to install macOS; the computer may reboot several times;  
       6. Finish the Setup Assistant, **⚠️ DO NOT enable FileVault**;
    4. **Copy EFI to disk**
       1. Reboot into PE system, copy the prepared `EFI` folder to the disk EFI partition;  
       2. Use [EasyUEFI](https://github.com/wsl540777/Hackintosh-for-Tahoe/releases/download/v26.2.0/EasyUEFI.zip) to add a boot entry (see [tutorial](https://www.bilibili.com/opus/872635104279658505));  
       3. Reboot, remove the USB, select the OpenCore boot entry from disk in BIOS and boot into macOS;  
       4. Use [OCAT](https://github.com/ic005k/OCAuxiliaryTools) or [OCC](https://mackie100projects.altervista.org/category/opencore-configurator/) to mount the EFI partition, disable or remove `itlwm.kext`; then download and install [OCLP‑Mod.pkg](https://github.com/laobamac/OCLP-Mod/releases?utm_source=copilot.com) and use it to inject the audio and Wi‑Fi driver patches (see the [tutorial](https://b23.tv/R7wvPSn?utm_source=copilot.com)).
  * #### Method 2: Install from a full installer image (requires at least two USB drives and a .dmg or .iso installer image) ([image download](https://hackintosh.club/d/10000080))
    1. **Prepare system partitions**
       1. ⚠️ **Enlarge the computer’s EFI partition to 300MB** (see [guide](https://www.shuzhiduo.com/A/pRdBN2n6dn/)). It’s recommended to make a external PE (Preinstallation Environment) system boot disk and resize under it (recommended [HotPE](https://www.hotpe.top/)); 
       2. Create a macOS system partition (not less than 60GB; for long‑term use at least 160GB recommended). Filesystem type doesn’t matter now — it will be erased during install.
    2. **Prepare installer media**
       1. Use another USB to burn a macOS installer (see [guide](https://divineengine.net/article/macos-bootable-installer/));  
       2. Mount this installer’s EFI partition and copy the prepared `EFI` folder into it (see [guide](https://www.mfpud.com/topics/930/)).
    3. **Install macOS**
       1. Reboot into BIOS settings and disable **Secure Boot** and **TPM / Security Chip** (for more options, see [this](https://apple.sqlsec.com/3-%E5%87%86%E5%A4%87%E5%B7%A5%E4%BD%9C/3-1/));
       2. Boot the installer from USB and in the OpenCore picker select **Install macOS Tahoe**;  
       3. In the installer, open **Disk Utility** and erase the target macOS partition to APFS;  
       4. Exit Disk Utility and choose **Install macOS Tahoe**;  
       5. Select the erased system disk and follow installer to install macOS; the computer may reboot multiple times;  
       6. Finish Setup Assistant, **⚠️ DO NOT enable FileVault**;
    4. **Copy EFI to disk**
       1. Reboot into PE system, copy the prepared `EFI` folder to the disk EFI partition;  
       2. Use [EasyUEFI](https://github.com/wsl540777/Hackintosh-for-Tahoe/releases/download/v26.2.0/EasyUEFI.zip) to add a boot entry (see [tutorial](https://www.bilibili.com/opus/872635104279658505));  
       3. Reboot, remove the USB, select the OpenCore boot entry from disk in BIOS and boot into macOS;  
       4. Use [OCAT](https://github.com/ic005k/OCAuxiliaryTools) or [OCC](https://mackie100projects.altervista.org/category/opencore-configurator/) to mount the EFI partition, disable or remove `itlwm.kext`; then download and install [OCLP‑Mod.pkg](https://github.com/laobamac/OCLP-Mod/releases?utm_source=copilot.com) and use it to inject the audio and Wi‑Fi driver patches (see the [tutorial](https://b23.tv/R7wvPSn?utm_source=copilot.com)). 
* #### Upgrading from an existing macOS:
  1. Use [OCAT](https://github.com/ic005k/OCAuxiliaryTools) or [OCC](https://mackie100projects.altervista.org/category/opencore-configurator/) to mount the EFI partition and replace it with the prepared EFI;  
  2. Reboot and Reset NVRAM, then in Windows or PE use [EasyUEFI](https://github.com/wsl540777/Hackintosh-for-Tahoe/releases/download/v26.2.0/EasyUEFI.zip) to re‑add the boot entry (see [tutorial](https://www.bilibili.com/opus/872635104279658505));  
  3. Use OTA update or an image installer (recommended [Mist](https://github.com/ninxsoft/Mist)) to Tahoe. On first boot **⚠️ DO NOT enable FileVault**;  
  4. In macOS, use [OCAT](https://github.com/ic005k/OCAuxiliaryTools) or [OCC](https://mackie100projects.altervista.org/category/opencore-configurator/) to disable or remove `itlwm.kext`; then download and install [OCLP‑Mod.pkg](https://github.com/laobamac/OCLP-Mod/releases?utm_source=copilot.com) and use it to inject the audio and Wi‑Fi driver patches (see the [tutorial](https://b23.tv/R7wvPSn?utm_source=copilot.com)).    
### Post-Installation
1. **Modify keys**: `System Settings > Keyboard > Keyboard Shortcuts > Modifier Keys`;  
2. **Adjust trackpad tap behavior**: `System Settings > Trackpad > Point & Click`, set to your preferred tap behavior;  
3. **Enable HiDPI** (see [guide](https://zhuanlan.zhihu.com/p/205279615), SIP state is already set for audio and Wi-Fi driver patches, **do not change its status this time**). **⚠️ Unsupported resolutions may cause screen artifacting**;  
4. **Fix noise from headphone:** download [ComboJack.zip](https://github.com/hoaug-tran/ComboJack/releases) (for ALC255/256/295/298), open Terminal and drag in the `install.sh` from the extracted `ComboJack_Installer` folder, press `Enter` to run, then reboot;  
5. **Prevent auto‑mounting** of non‑macOS volumes (see [guide](https://apple.stackexchange.com/questions/310574/how-to-prevent-auto-mounting-of-a-volume-in-macos-high-sierra)). Replace spaces in volume names with `\040`. If unsure of the filesystem name, use `auto`;  
6. Go to `System Settings > Privacy & Security` and **double‑check FileVault is turned off**. **⚠️ If you accidentally enabled FileVault on first boot and cannot log in, refer to [this guide](https://imacos.top/2025/07/01/1457-3/) for solutions; if decryption is stuck at Paused, see the solution at the end of [this guide](https://kextcache.com/filevault-login-issue-fix/).**  
7. ⚠️ The **GPU framebuffer patch**, **CPUFriendDataProvider.kext**, and **USBMap.kext** included in this EFI are **model‑specific**. **If your machine or SMBIOS spoof differs, you must customize them to your own.**  
8. ⚠️ Before using Time Machine backup/restore or OTA updates, **temporarily remove the installed system patches with OCLP‑Mod** to avoid system panic when booting.

## Recommended Tools
* [Camo Studio](https://camo.com/studio) — Use Android/iOS device camera as Continuity Camera substitute;  
* [HoRNDIS](https://github.com/jwise/HoRNDIS) — A driver that allows you to use your Android phone's USB tethering mode to get Internet access；
* [Blip](https://blip.net/download) — Remote file transfer alternative to AirDrop;  
* [Paragon NTFS for Mac](https://support-en.wd.com/app/answers/detailweb/a_id/34871) — NTFS read/write support on macOS;  
* [DockDoor](https://dockdoor.net/) — Enhanced window preview switching;  
* [Swish](https://highlyopinionated.co/swish/) — Trackpad gesture window manager;  
* [Hidden Bar](https://apps.apple.com/us/app/hidden-bar/id1452453066) — Hide menu bar icons;  
* [KeyClu](https://github.com/Anze/KeyCluCask/) — View and learn shortcuts;  
* [BandiZip](https://www.bandisoft.com/bandizip/) — Powerful archive manager;  
* [IINA](https://iina.io) — Versatile macOS media player;  
* [UninstallPKG](https://www.corecode.io/uninstallpkg/) — Uninstall `.pkg` installers;  
* [App Cleaner & Uninstaller](https://app-cleaner.com/zh-hans) — App cleanup/uninstall tool;  
* [BuhoLaunchpad](https://www.drbuho.com/buholaunchpad) — Launchpad app for Tahoe;  
* [Displaperture](https://apps.apple.com/us/app/displaperture/id1543920362?mt=12) — Make macOS screen rounded corners.

## Thanks and References
* [OpenCore Reference Manual](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/Configuration.pdf)
* [Dortania's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
* [国光的黑苹果安装教程：手把手教你配置 OpenCore](https://apple.sqlsec.com/)
* [精解OpenCore | 黑果小兵的部落阁](https://blog.daliansky.net/OpenCore-BootLoader.html)
* [完全面向萌新的黑苹果安装教学：黑苹果安装从入门到入白](https://www.mfpud.com/topics/10263/)
* [opencore 小白指南（教程）- 欧尼酱的小屋](https://jmdonj.com/opencore-%e5%b0%8f%e7%99%bd%e6%8c%87%e5%8d%97-%ef%bc%88%e6%95%99%e7%a8%8b%ef%bc%89.html)
* [记录自己matebook14黑苹果过程，仅供参考](https://github.com/K1ruo/matebook14-hack)
* ~~[macOSTahoe intel网卡WiFi修复教程（itlwm）](https://www.bilibili.com/video/BV17Q8QzeEJq/)~~
* [「全站首发」macOS26intel网卡原生wifi蓝牙驱动教程（非itlwm）「黑苹果通用系列教程」](https://b23.tv/R7wvPSn)
* [macOS Tahoe 声卡修复（macos26）OCLP-MOD【全站首发】](https://www.bilibili.com/video/BV1xWKkzbEjT/)

## This EFI was modified based on:
* [华为mate book14-i7 10510u EFI适用Sonoma 14.4分享](https://bbs.pcbeta.com/viewthread-2002062-1-1.html)
* [Matebook 14/13 \(2019/2020/2021\) MacOS Monterey & Bigsur 黑苹果安装教程](https://github.com/frezs/MateBook14-Hackintosh)
