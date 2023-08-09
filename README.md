# hackintosh

## docs

[hackintosh](./docs/hackintosh.md)

[ASL](hack_asl.md)

## Find Device Path in DSDT

Ref: https://www.tonymacx86.com/threads/ssdt-gpu-graphics-card-injection.183354/

1. Location: Find your GPU's location in your IO Registry
    - searching for the word "display"
    - for example
        - `.../PCI0@0/AppleACPIPCI/NPE3@2/IOPP/GFX0@0/...`
        - In this example, the GPU is located in the PCI slot named "NPE3" and the address is "@2" (0x00020000)
        - Your GPU may already have a name assigned to it in the ACPI tables like "GFX0@0" or "PEGP@0".
        - NOTE
            - "AppleACPIPCI" is the driver and not a device.
            - "IOPP" is the PCI bridge for that slot and not a additional device (it has no "@" address)

2. Path
    - Open your own DSDT.aml with MaciASL
    - search `Device (NPE3)`
    - Take a note of the device path at the far bottom left corner of the app but discard "DSDT" 
        - `_SB.PCI0.NPE3`


# ACPI 热补丁

https://xstar-dev.github.io/hackintosh_advanced/ACPI_Hotpatch_Overview.html#%E4%BB%80%E4%B9%88%E6%98%AF-acpi-%E7%83%AD%E8%A1%A5%E4%B8%81


# OC-Little

英文感觉更全

https://github.com/5T33Z0/OC-Little-Translated/blob/main/04_Fixing_Sleep_and_Wake_Issues/060D_Instant_Wake_Fix/README.md

中文版

https://github.com/daliansky/OC-little/blob/master/12-060D%E8%A1%A5%E4%B8%81/README.md


# Dell 5480 Sleep

try


1. XDCI (Extensible Device Controller Interface)

```
pmset -g log | grep -e "Sleep.*due to" -e "Wake.*due to"

看是不是 due to XDCI \ HID 

如果是， 试试 在 config.plist 中 加入

0A6D0A03 -> 00000000
0A6D0A04 -> 00000000
```


另外，SSDT-GPRW.aml 的补丁不对，。 需要现在 config.plist, 把 GPRW 全局替换成 XPRW



2. disable usb wake up in bios

3. 


PBTN related


```
PBTN
  BTNV

HIDD
  BTNL

LPC总线（Low pin count Bus）
  SWAK

HECI   Intel英特尔Management Engine Interface(HECI)
  _DSM
```
