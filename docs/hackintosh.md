[](...menustart)

- [NUC8i5BEK Hackintosh](#da39d68f2cf6c3c46123458cd98bd79d)
    - [NUC8i5BEK BISO setting](#90b4244131823d7d8c05f4f1c274183e)
    - [Hackintosh USB Creation](#3144f57f7c88cbc932e24094440b99d8)
- [PMSET](#4aef377d2268514c138fb8df812de501)
    - [pmset utils](#6b11e49104896b1ebbbba0efc133864a)
    - [Tips:  disalbe `Power Nap` !!!](#88de13afc9c8f51b1576661724f9e502)
    - [Catalina 10.5.2 Sleep problem (Deprecated in Ventura)](#21b23f4b2c31a358f6a64baa1bd7f0e6)
    - [Big Sur Sleep problem (Deprecated in Ventura)](#71a0c3b13bc8bba856aaa874173e33a8)
- [Misc](#74248c725e00bf9fe04df4e35b249a19)
    - [Centos bootable USB installer on OSX](#e529594e6b7af1bbc305ae37bca03507)
    - [Iris Plus 655  Without WhatEverGreen](#b629a48f87baa1255d141a0d31957405)
    - [check your opencore plist](#f9a1aa084270d908f100a833ffc5f42d)
    - [Install Monterey on unsupported Mac](#269dbe135802539e86431697032c3599)
    - [bluetooth problem: airpod audio crack (NOT WORK)](#5b39fb0e867ea7803ee39bad2c8ea7d6)

[](...menuend)


<h2 id="da39d68f2cf6c3c46123458cd98bd79d"></h2>

# NUC8i5BEK Hackintosh

<h2 id="90b4244131823d7d8c05f4f1c274183e"></h2>

## NUC8i5BEK BISO setting

<details>
<summary>
BIOS 077
</summary>

- « Intel VT for directed I/VO (VT-d) » ： disabled
    - **can be enabled if you set DisableIoMapper to YES**
- « Secure Boot » ： disabled
- « Fast Boot » ： disabled
- UEFI boot enalbe /   Legacy Boot disable
- SATA mode ： AHCI
- Boot->Boot Configuration-> « Boot Network Devices Last » ： disabled
- Power->Secondary Power Settings, « Wake on LAN from S4/S5 », set to « Stay Off »
- Devices->Video, « IGD Minimum Memory » set to 64mb
- Devices->Video, « IGD Aperture Size » set to MAX
- disable reader-card

</details>


<details>
<summary>
BIOS 085
</summary>

https://github.com/zearp/Nucintosh

- Devices -> USB -> Port Device Charging Mode: off
- Devices -> USB -> USB Legacy -> Disabled
- Devices -> PCI  
    - wifi -> disable
    - readcard -> disable
- Security -> Thunderbolt Security Level: Legacy Mode
- Power -> Wake on LAN from S4/S5: Stay Off
- Boot -> Boot Configuration -> Network Boot: Disable
- Boot -> Secure Boot -> Disable

</details>




<h2 id="3144f57f7c88cbc932e24094440b99d8"></h2>

## Hackintosh USB Creation

- [post on tonymac86](https://www.tonymacx86.com/threads/guide-booting-the-os-x-installer-on-laptops-with-clover.148093/#post-917900)

- Recommend: MBR with a FAT32 partition for Clover and a separate HFS+J partition for the OS X installer.
    - most computers can boot from it (even with UEFI), and it is convenient that the EFI partition will mount automatically when the USB is inserted.

- step 1,find your USB disk, in this case its /dev/disk1
    - 
    ```bash
    diskutil list
    /dev/disk0 (internal, physical):
       #:                       TYPE NAME                    SIZE       IDENTIFIER
       0:      GUID_partition_scheme                        *500.1 GB   disk0
       1:                        EFI EFI                     209.7 MB   disk0s1
       2:                  Apple_HFS 10.10.x                 80.0 GB    disk0s2
       3:                  Apple_HFS 10.11.gm1               80.0 GB    disk0s3
       4:       Microsoft Basic Data Win10_TP                79.4 GB    disk0s4
       5:                  Apple_HFS 10.10.test              80.0 GB    disk0s5
    /dev/disk1 (external, physical):
       #:                       TYPE NAME                    SIZE       IDENTIFIER
       0:                                                   *8.0 GB     disk1[/B]
    ```
- step 2, partion , in this example, it create 3 partions 
    - 
    ```bash
    diskutil partitionDisk /dev/disk1 3 MBR FAT32 "OpenCore EFI" 200Mi HFS+J "install_osx" 12000Mi ExFat "ExFat" R
    Finished partitioning on disk1
    /dev/disk1 (external, physical):
       #:                       TYPE NAME                    SIZE       IDENTIFIER
       0:     FDisk_partition_scheme                        *31.0 GB    disk1
       1:                 DOS_FAT_32 CLOVER EFI              209.7 MB   disk1s1
       2:                  Apple_HFS install_osx             12.6 GB    disk1s2
       3:               Windows_NTFS ExFat                   18.2 GB    disk1s3
    ```
    - Note: If you're using Clover legacy, the USB should must be MBR.
    - Note: Some BIOS implementations require GPT, some require MBR (many allow both). If you can't get BIOS to recognize your USB for booting, try GPT instead of MBR.
- OSX installation
    - 
    ```bash
    sudo /Applications/Install\ macOS\ Mojave.app/Contents/Resources/createinstallmedia --volume /Volumes/install_osx/ --nointeraction --downloadassets
    ```


<h2 id="4aef377d2268514c138fb8df812de501"></h2>

# PMSET

https://eclecticlight.co/2017/01/20/power-management-in-detail-using-pmset/

1. Sleep
2. displaysleep is the time in minutes before the display is put to sleep
3. disksleep is the time in minutes before hard disks are spun down and put to sleep; 
4. gpuswitch sets GPU behaviour during sleep, and appears to be undocumented;
5. hibernatefile sets the location of the cache to be used when going into standby mode, which must be on the boot disk; 
6. hibernatemode is set to 0 for plain sleep (desktops), 3 to store a copy of memory to disk and keep memory powered up (laptops), or 25 for full hibernation in which memory is powered down too;
7. highstandbythreshold, which is set to 50 by default (i.e. 50 percent battery capacity). Instead of one standbydelay argument you now have two: standbydelayhigh and standbydelaylow. If you’re above 50, the high delay is used, if you’re below 50 percent battery, the low delay kicks in.
8. networkoversleep - this setting affects how OS X networking presents shared network services during system sleep. This setting is not used by all platforms; changing its value is unsupported.
9. powernap sets whether your Mac will take Power Naps, where supported;
10. proximitywake - On supported systems, this option controls system wake from sleep based on proximity of devices using same iCloud id. (value = 0/1)
11. sleep is the time in minutes to system sleep; 0 means never;
12. standby sets whether to hibernate when a Mac has been asleep for a set period; this writes memory to disk and powers down memory;
13. standbydelay
14.     is the delay in seconds between going to sleep and switching to standby mode;
15. tcpkeepalive
16. ttyskeepawake can prevent sleep when a text input, such as a remote login session, is active;
17. womp is 1 if you want your Mac to wake when it receives a ‘magic’ network packet, which is the same as wake for network access in the pane;


<h2 id="6b11e49104896b1ebbbba0efc133864a"></h2>

## pmset utils

```bash
# restore 
sudo pmset restoredefaults

pmset -g 
pmset -g assertions
pmset -g log
pmset -g assertionslog
```

<h2 id="88de13afc9c8f51b1576661724f9e502"></h2>

## Tips:  disalbe `Power Nap` !!!

<h2 id="21b23f4b2c31a358f6a64baa1bd7f0e6"></h2>

## Catalina 10.5.2 Sleep problem (Deprecated in Ventura)

```bash
launchctl unload -w /System/Library/LaunchAgents/com.apple.parsec-fbf.plist
```

As parsec-fbf sends analytical data to Apple and flushes which is sent from local machine, then probably there is no any serious drawbacks on disabling parsec.fbf agent, except that data collected by Siri is not flushed. Data is collected in users Cashes into com.apple.parsecd folder.

Data can be flushed manually if needed:

```bash
cd ~/Library/Caches
rm -R com.apple.parsecd
```

<h2 id="71a0c3b13bc8bba856aaa874173e33a8"></h2>

## Big Sur Sleep problem (Deprecated in Ventura)

```bash
launchctl unload -w  /System/Library/LaunchAgents/com.apple.triald.plist
launchctl unload -w  /System/Library/LaunchAgents/com.apple.proactiveeventtrackerd.plist

rm -R ~/Library/Caches/com.apple.proactive.eventtracker
```

## Ventura , some of above methods failed

```bash
rm -rf ~/Library/Caches/com.apple.parsecd
rm -rf  ~/Library/Caches/com.apple.proactive.eventtracker

# need `csrutil disable`
sudo launchctl bootout system /System/Library/LaunchDaemons/com.apple.triald.system.plist

# can not find and disable following services
$ launchctl list|egrep -i track
642	0	com.apple.UsageTrackingAgent
994	0	com.apple.proactiveeventtrackerd

# cancel apple scheduled task
$ pmset -g sched
$ sudo pmset schedule cancelall
```


<h2 id="74248c725e00bf9fe04df4e35b249a19"></h2>

# Misc 


<h2 id="e529594e6b7af1bbc305ae37bca03507"></h2>

## Centos bootable USB installer on OSX

https://cloudwrk.com/create-centos-7-bootable-usb-on-osx/

1. Format a USB drive as **MBR**/eFAT with Disk Utility
2. Download CentOS 7 Full or Minimal iso file
3. Open terminal and find USB drive partition name with command “diskutil list“
4. In terminal, use dd command to copy CentOS iso to usb
5. Test usb drive in Parallels virtual machine

```bash
sudo dd if=./Downloads/CentOS-7-x86_64-DVD-1611.iso of=/dev/rdisk2 bs=1m
```

- **Note** the additional ***r*** prepended to the usb partition name rdisk2 instead of disk2**, but more faster



<h2 id="b629a48f87baa1255d141a0d31957405"></h2>

## Iris Plus 655  Without WhatEverGreen

1. Add SSDT for Iris Plus 655 iGPU
    - a) find it from network
    - or b) Hackintool / PCIe / update & export 
    - **PS: no more needed !!!, just renaming & patching !!!**
2. rename  GFX0 to IGPU , HECI to IMEI ( whatever once take over it )
    ```plist
            <dict>
                <key>Comment</key>
                <string>change GFX0 to IGPU</string>
                <key>Enabled</key>
                <true/>
                <key>Find</key>
                <data>R0ZYMA==</data>
                <key>Replace</key>
                <data>SUdQVQ==</data>
            </dict>
            <dict>
                <key>Comment</key>
                <string>change HECI to IMEI</string>
                <key>Enabled</key>
                <true/>
                <key>Find</key>
                <data>SEVDSQ==</data>
                <key>Replace</key>
                <data>SU1FSQ==</data>
            </dict>
    ```
3. Add patch to DeviceProperties


<details>
<summary>
Iris Plus 655  platform-id / device-id
</summary>

```text
1. Bios   gpu memory setting
    - Allocation : depending on the frame buffer ( look value TOTAL_STOLEN in frame buffer list )
    - DVMT total memory size: max
    - https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md
    - 但是 似乎没什么问题。。。

To inject DeviceProperties

Only these properties may be added:
— AAPL,ig-platform-id or AAPL,snb-platform-id framebuffer
— device-id for IGPU (if faking is necessary)
— device-id for IMEI (if faking is necessary)
— properties for patches (if necessary)


### AAPL,ig-platform-id

Iris Plus 655

ID: 3EA50005, STOLEN: 57 MB, FBMEM: 0 bytes, VRAM: 1536 MB, Flags: 0x00E30B0A
TOTAL STOLEN: 58 MB, TOTAL CURSOR: 1 MB (1572864 bytes), MAX STOLEN: 172 MB, MAX OVERALL: 173 MB (181940224 bytes)
Model name: Intel Iris Plus Graphics 655


Recommended framebuffers:

Laptop:
0x3EA50009 (default)


# And now you have your final framebuffer profile
0900A53E = AAPL,ig-platform-id


#### device-id

https://ark.intel.com/content/www/us/en/ark/products/135935/intel-core-i5-8259u-processor-6m-cache-up-to-3-80-ghz.html


Device ID
0x3EA5   // lucky

# And voila, you have your device-id
A53E0000 = device-id


DeviceProperties
  Add
    PciRoot(0x0)/Pci(0x2,0x0)
      AAPL,ig-platform-id  Data <>
      device-id            Data <>
      ...
```

</details>


<h2 id="f9a1aa084270d908f100a833ffc5f42d"></h2>

## check your opencore plist

https://opencore.slowgeek.com/



<h2 id="269dbe135802539e86431697032c3599"></h2>

## Install Monterey on unsupported Mac

1. create Monterey Install USB
2. download [OpenCore Legacy Patcher](https://github.com/dortania/OpenCore-Legacy-Patcher/releases/tag/0.3.1)
    - OpenCore-Patcher-TUI.app.zip
3. For USB
    - Set Minimal or higher spoofing to retain the current Board ID
        - Available with TUI, Patcher Settings -> SMBIOS Settings -> Set SMBIOS Spoof Level
    - run OC patcher, apply 1. Build OpenCore  2. Install OpenCore to USB driver
    - now an EFI folder has been installed on your USB EFI disk partition
4. Reboot ( hold Option key), entering EFI, and Install new OSX
5. (Install Finish) In MacOSX,   run OC patcher again
    - 5 Patcher Setting
        - Set Minimal or higher spoofing to retain the current Board ID
            - Available with TUI, Patcher Settings -> SMBIOS Settings -> Set SMBIOS Spoof Level
        - 1. Debug Setting -> false
        - 5. Misc / 1. show picker -> false
    - apply 1. Build OpenCore  2. Install OpenCore to internal driver
    - eject USB
6. hold Option Reboot,   hold ctrl + EFI
7. run OC patcher again,
    - apply 5. POST ...

<h2 id="5b39fb0e867ea7803ee39bad2c8ea7d6"></h2>

## bluetooth problem: airpod audio crack (NOT WORK)

```bash
$ defaults write bluetoothaudiod "Enable AAC codec" -bool true

$ defaults read bluetoothaudiod "Enable AAC codec"
1
```

## Extract DSDT

- 名词解释
    - ACPI：Advanced Configuration and Power Management Interface
    - DSDT：The Differentiated System Description Table，字面翻译是“差异系统描述表”，是ACPI规范的一部分。包含了所有除基本系统以外所有不同设备的信息。
        - 和 Windows 相反，macOS 的驱动是被动驱动，需要让系统知道存在这个硬件且知道硬件位置，所以修改DSDT的主要目的就是让macOS识别存在这个硬件并告诉系统硬件位置，以便让系统内置的驱动来使硬件工作。
    - SSDT：System Services Descriptor Table，字面翻译是“系统服务描述符表”，是DSDT的主要组成部分。
        - SSDT相当于系统内部API（Application Programming Interface，应用程序接口）的指向标，
        - 告诉系统，需要调用的API在什么地方。在黑苹果中，SSDT里的信息主要关于处理器电源管理、USB、显卡等相关信息。针对DSDT打补丁，将使设备的控制尽量向白苹果靠近，解决稳定性并提升性能。

- get hardware info (on Windows)
    ```bash
    AIDA64  获取硬件配置信息
      管理员身份打开，
      /报告/报告向导/ * 硬件相关内容 / * HTML 文件 --> 保存为文件
    ```
- extract ACPI (DSDT & SSDT)
    - methods
        - https://dortania.github.io/Getting-Started-With-ACPI/Manual/dump.html
    - using clover
        - 在网上随便下载一个 四叶草 Clover 的 EFI 文件
            - /Clover/ACPI/origin  清空
        - 拷贝到 空的Fat32 U盘
        - U 盘启动， 四叶草引导界面，按一下 左右键 防止自动进入系统，然后按 F4 或者 Fn+F4，稍等半分钟， /Clover/ACPI/origin 会重新生成你的ACPI 文件
- SSDT Time
    - dump DSDT
    - delete the generated DSDT.aml in /Results folder, replace with yours
    - `d`  select dsdt file
    - run SSDT command from 1 through 7 
    - using those generated .aml files to replace corresponding files in your OPEN CORE /EFI/OC/ACPI .


## 黑苹果睡眠问题

http://blog.webleon.me/2021/08/blog-post.html

```txt
首先，在黑苹果里我们追求的睡眠主要是指 Sleep，而不是hibernatemode或者standby。

log show --last 2h | grep 'Wake reason'
过去2小时的唤醒原因



pmset -g log | grep -e "Sleep.*due to" -e "Wake.*due to"
查看睡眠与唤醒的原因

pmset -g assertions
查看电源管理当前状态

最大程度保证休眠的稳定：

hibernatemode = 0 数据只写入内存，不写入硬盘

proximitywake = 0 关闭被同一网络下的同 iCloud 设备唤醒

standby = 0 只使用睡眠，不使用待机

tcpkeepalive = 0 休眠后断开网络

ttyskeepawake = 0 远程登录或其他远程输入不影响睡眠


如果设置了屏幕保护，进入屏幕保护的时间要短于屏幕关闭的等待时间（displaysleep），否则可能会造成 (sleep prevented by sharingd) 而无法睡眠。
```
