# # OpenCore Hackintosh for Tahoe 26.2 在线安装 & EFI for Matebook 14 2020
备注：在 Sequoia 及以上系统的**恢复模式**中，无线网卡因不支持 Airportitlwm 驱动而无法连接网络，只能连接网线，对于笔记本较为麻烦。受 UP 主 **@win10Q** 的启发，我通过手动修改 itlwm.kext 的配置，使其能自动连接指定 Wi‑Fi，从而实现 **Tahoe 的在线安装**。整个流程**只需准备 EFI 和不到 1.5GB 的恢复系统**，省去找镜像、下镜像和刻录启动盘的麻烦，让全新安装 Tahoe 更快、更简单。

**恢复模式在线安装 ≠ 恢复版镜像安装**

**•**  恢复模式（Recovery Boot）在线安装：类似安卓的“恢复模式”，联网后直接从苹果服务器下载并安装系统。

**•**  恢复版镜像：类似 Ghost，还原别人制作的磁盘映像。 
## 本 EFI 适配机型配置
| 项目   | 详细参数                                                         |
|:----:|--------------------------------------------------------------|
| 型号   | HUAWEI Matebook 14 2020 (KLVC-WFE9L/WXX9)                    |
| CPU  | Intel Core i7 10510U                                         |
| 内存   | Samsung 16 GB 2133 MHz LPDDR3                                |
| GPU  | Intel Comet Lake-U GT2 (UHD Graphics 620) & NVIDIA GeForce MX350 |
| 硬盘   | WD SN730 512G                                                |
| 声卡   | Realtek ALC256                                               |
| 无线网卡 | Intel Wireless-AC 9560                                       |
| 显示屏  | CHIMEI CMN8C02 (P140ZKA-BZ1) @ 2160*1440                     |
| BIOS | 1.31                                                         |
## 适配情况
### ✅ 支持
* CPU（支持睿频）
* Intel 核显（仿冒为 UHD 630，性能发挥更充分）
* 睡眠和休眠
* USB 3.0/2.0
* 触控板/触摸屏（已经修复）
* 无线网卡（Sequoia & Tahoe 暂时只能使用 itlwm + Heliport）
* 蓝牙
* 声卡 扬声器/麦克风/耳机输出（Tahoe 需通过 OCLP-Mod 注入驱动，耳麦暂不支持）
* Siri
* 自动填充 / 通行密钥
* 连续互通：接力/通用剪贴板（仅支持同一局域网的其它 Apple 设备 -> Mac）/ 电话和短信转发 / 验证码
* 连续互通（有线）：随航 / 插入速绘、照片和扫描件 / 共享 Wi-Fi
### ❌ 不支持
* HDMI（原因未知，解决中）
* 摄像头（解决方案：通过 Camo Studio 连续互通）
* 指纹
* MX350 独显
* 连续互通： 隔空投送 / 通用控制 / 连续互通相机 / iPhone 镜像 / 使用 Apple Watch 解锁 Mac
* 定位服务/查找（itlwm 基于以太网服务驱动无线网卡，而定位服务依赖于 Wi-Fi） 

## Hackintosh for Tahoe 安装教程（仅供参考）
* **访问和下载文件加速：[GitHub 文件加速代理](https://gh-proxy.com/)**
### 安装前
1. ⚠️ **备份原有系统和 EFI 分区；**
2. 准备至少 1 个 空 U 盘；
3. 从 [Release 页面](https://github.com/wsl540777/Matebook-14-Hackintosh-for-Tahoe/releases)下载 EFI.zip（或自行准备适配机型的 EFI）；
4. 使用 [OCAT](https://github.com/ic005k/OCAuxiliaryTools) 或 [OpenCore Configurator](https://mackie100projects.altervista.org/category/opencore-configurator/) 注入三码（[教程](https://www.cnblogs.com/codtina/p/18314522)）；
5. 下载好 [Heliport 客户端](https://github.com/OpenIntelWireless/HeliPort/releases)（用于连接 Wi-Fi）。
### 安装过程
* #### 从 Windows 下开始全新安装：
  * #### 方法一：使用官方 Recovery Boot 在线安装（仅需一个 U 盘，无需准备完整系统镜像）
    1. **系统分区的准备**
       1. ⚠️ **EFI 分区扩容**至 300MB（[教程](https://bbs.pcbeta.com/viewthread-1880061-1-1.html)）。建议制作一个 U 盘启动盘，在 PE 下进行扩容（推荐 [HotPE](https://www.hotpe.top/)）；
       2. 创建 macOS 系统分区（不低于 60GB，长期使用建议至少160G，酌情安排）。分区格式随意，安装前会再次抹掉。
    2. **恢复分区的准备**
       1. 下载 [RecoveryOS.26.zip](https://github.com/wsl540777/Matebook-14-Hackintosh-for-Tahoe/releases/download/untagged-f8b01d5c41ba81fe0530/RecoveryOS.26.0.zip?utm_source=copilot.com)，解压得到 com.apple.recovery.boot 文件夹；
       2. U 盘创建一个 2G 的 FAT32 分区，根目录下放入 com.apple.recovery.boot 和 EFI 文件夹；
       3. **配置网络服务（有网线连接的可忽略）：** 进入 EFI > OC > Kext > itlwm.kext > Contents > Info.plist，用记事本打开，找到 WiFi_1，修改 **ssid** 和 **password** 对应的 string 值为要连接的 **Wi-Fi 名称** 和 **密码**，保存退出。
    3. **使用恢复模式安装系统**
       1. 重新启动，进入 BIOS，关闭 **安全启动** 和 **安全芯片**；
       2. 选择刚才配置的 U 盘恢复分区启动， OpenCore 界面选择 **Recovery 26.0 (Portable)；**
       3. 进入恢复分区后选择 **磁盘工具**， 将准备安装 macOS 的分区抹掉成 APFS 格式；
       4. 退出磁盘工具，选择 **重新安装 macOS Tahoe**。如果能够看到用户协议，说明上一步的网络配置是成功的；
       5. 选择刚才抹掉的系统盘，按照提示安装 macOS，期间可能会多次重启；
       6. 完成初次激活，**⚠️ 不要启用文件保险箱加密**；
       7. 进入系统后安装 Heliport 客户端，以连接网络。
    4. **拷贝 EFI 分区**
       1. 重新启动，BIOS 选择进入 PE 系统，将 EFI 文件夹拷贝到硬盘 EFI 分区；
       2. 使用 [EasyUEFI](https://github.com/wsl540777/Matebook-14-Hackintosh-for-Tahoe/releases/download/untagged-f8b01d5c41ba81fe0530/EasyUEFI.4.zip) 添加引导项（[教程](https://www.bilibili.com/opus/872635104279658505)）；
       3. 重新启动，拔掉 U 盘，BIOS 选择硬盘 OpenCore 引导，进入 macOS，完成。
* #### 方法二：使用完整的系统镜像安装（至少需要两个 U 盘，且需要准备 .dmg 或 .iso 格式系统镜像）（[镜像下载地址](https://hackintosh.club/d/10000080)）
  1. **系统分区的准备**
     1. ⚠️ **EFI 分区扩容**至 300MB（[教程](https://bbs.pcbeta.com/viewthread-1880061-1-1.html)）。建议制作一个 U 盘启动盘，在 PE 下进行扩容（推荐 [HotPE](https://www.hotpe.top/)）；
     2. 创建 macOS 系统分区（不低于 60GB，长期使用建议至少160G，酌情安排）。分区格式随意，安装前会再次抹掉。
  2. **安装盘的准备**
     1. 用另一个 U 盘制作 macOS 安装介质（[教程](https://divineengine.net/article/macos-bootable-installer/)）；
     2. 挂载安装盘的 EFI 分区，并将准备好的 EFI 文件夹拷贝进去。（[教程](https://www.mfpud.com/topics/930/)）
  3. **安装系统**
     1. 重新启动，进入 BIOS，关闭 **安全启动** 和 **安全芯片**；
     2. 选择刚才配置的 U 盘安装盘启动，OpenCore 界面选择 **Install macOS Tahoe；**
     3. 进入恢复分区后选择 **磁盘工具**， 将准备安装 macOS 的分区抹掉成 APFS 格式；
     4. 退出磁盘工具，选择 **安装 macOS Tahoe**；
     5. 选择刚才抹掉的系统盘，按照提示安装 macOS，期间可能会多次重启；
     6. 完成初次激活，**⚠️ 不要启用文件保险箱加密**；
     7. 进入系统后安装 Heliport 客户端，以连接网络。
  4. **拷贝 EFI 分区**
     1. 重新启动，BIOS 选择进入 PE 系统，将 EFI 文件夹拷贝到硬盘 EFI 分区；
     2. 使用 [EasyUEFI](https://github.com/wsl540777/Matebook-14-Hackintosh-for-Tahoe/releases/download/untagged-f8b01d5c41ba81fe0530/EasyUEFI.4.zip) 添加引导项（[教程](https://www.bilibili.com/opus/872635104279658505)）；
     3. 重新启动，拔掉 U 盘，BIOS 选择硬盘 OpenCore 引导，进入 macOS，完成。
* #### 从原有 macOS 升级：
  1. 使用 [**OCAT**](https://github.com/ic005k/OCAuxiliaryTools) 或 [**OpenCore Configurator**](https://mackie100projects.altervista.org/category/opencore-configurator/) 挂载 EFI 分区并替换新 EFI；
  2. 重启，执行 Reset NVRAM，在 Windows 或 PE 下使用 [EasyUEFI](https://github.com/wsl540777/Matebook-14-Hackintosh-for-Tahoe/releases/download/untagged-f8b01d5c41ba81fe0530/EasyUEFI.4.zip) 重新添加引导项（[教程](https://www.bilibili.com/opus/872635104279658505)）；
  3. 进入 macOS，安装 Heliport 客户端，连接网络；
  4. 使用镜像安装（推荐使用 [Mist](https://github.com/ninxsoft/Mist)）或 OTA 更新到 Tahoe，初次进入系统时 **⚠️ 不要启用文件保险箱加密**。
### 安装后
1. **更改键盘按键映射：**系统设置 > 键盘 > 键盘快捷键 > 修饰键；
2. **调整触控板点按方式：** 系统设置 > 触控板 > 光标与点按，调整为自己熟悉的点按方式；
3. **开启 HiDPI**（[教程](https://zhuanlan.zhihu.com/p/205279615)，SIP 状态已为后续注入声卡驱动设置好，**不用手动开启或关闭**）。目前硬件支持的最佳分辨率为 1335x890，其他分辨率可以自行设置，**⚠️ 不支持的分辨率可能导致花屏**；
4. **解决声卡问题：**下载 [OCLP-Mod.pkg](https://github.com/laobamac/OCLP-Mod/releases) 并安装，按照提示 **安装驱动补丁**，重启；
5. **修复耳机电流声：**下载 [ComboJack.zip](https://github.com/hoaug-tran/ComboJack/releases)（适用于 ALC255/256/295/298），打开终端，将解压后 ComboJack_Installer 目录下的 install.sh 拖到终端中，回车运行，重启；
6. **禁止自动挂载**非 macOS 分区（[教程](https://apple.stackexchange.com/questions/310574/how-to-prevent-auto-mounting-of-a-volume-in-macos-high-sierra)），如果不清楚文件系统名称，可用 auto 代替；
7. **⚠️ 若在初次进入 macOS Tahoe 时不慎选择了启用文件保险箱加密，导致输入密码无法进入系统，可参考[这篇教程](https://imacos.top/2025/07/01/1457-3/)解决问题；如果解密过程卡在 Paused 状态，[这篇教程](https://kextcache.com/filevault-login-issue-fix/)文末有解决方案；**
8. ⚠️ 本 EFI 中的**显卡缓冲帧补丁**、**CPUFriendDataProvider.kext** 和 **USBMap.kext** 为**机型定制**。**若本机机型或仿冒机型不同，请重新定制**。

## 推荐工具
* [Camo Studio](https://camo.com/studio)，将 安卓/iOS 设备摄像头用作 Mac 摄像头，实现类似连续互通相机功能；
* [LocalSend](https://apps.apple.com/us/app/localsend/id1661733229)，替代隔空投送的局域网互传工具；
* [Paragon NTFS for Mac](https://support-en.wd.com/app/answers/detailweb/a_id/34871)，macOS 下支持 NTFS 读写；
* [DockDoor](https://dockdoor.net/)，窗口预览切换增强工具；
* [Swish](https://highlyopinionated.co/swish/)，触控板手势窗口管理工具；
* [Bartender](https://www.macbartender.com/)，菜单栏图标整理工具；
* [KeyClu](https://github.com/Anze/KeyCluCask/)，学习并熟悉 macOS 快捷键功能；
* [Keka](https://www.keka.io/zh-cn/)， 压缩文件管理器；
* [UninstallPKG](https://www.corecode.io/uninstallpkg/)，卸载 .pkg 安装的程序；
* [BuhoLaunchpad](https://www.drbuho.com/buholaunchpad)，旧版启动台应用；
* [Displaperture](https://apps.apple.com/us/app/displaperture/id1543920362?mt=12)，让 Mac 变成圆角屏幕。

## 感谢以下提供帮助和灵感的教程：
* [国光的黑苹果安装教程：手把手教你配置 OpenCore](https://apple.sqlsec.com/5-%E5%AE%9E%E6%88%98%E6%BC%94%E7%A4%BA/5-5/)
* [完全面向萌新的黑苹果安装教学：黑苹果安装从入门到入白](https://www.mfpud.com/topics/10263/)
* [记录自己matebook14黑苹果过程，仅供参考](https://github.com/K1ruo/matebook14-hack)
* [macOSTahoe intel网卡WiFi修复教程（itlwm）](https://www.bilibili.com/video/BV17Q8QzeEJq/)
* [macOS Tahoe 声卡修复（macos26）OCLP-MOD【全站首发】](https://www.bilibili.com/video/BV1xWKkzbEjT/)

## 本 EFI 以如下为蓝本进行修改：
* [华为mate book14-i7 10510u EFI适用Sonoma 14.4分享](https://bbs.pcbeta.com/viewthread-2002062-1-1.html)
* [Matebook 14/13 \(2019/2020/2021\) MacOS Monterey & Bigsur 黑苹果安装教程](https://github.com/frezs/MateBook14-Hackintosh)
