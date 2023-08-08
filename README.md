# hackintosh

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
