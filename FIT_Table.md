# Firmware Interface Table

## Overview
- **Describe a data structure inside BIOS flash and consists of multiple entries**
- **Each entry defines the starting address and attributes of different components in the BIOS**
- **FIT resides in the BIOS Flash area and is located by a FIT pointer at physical address, 4GB - 40h**
- **This table is generated at build time, no modify for runtime**
- **The CPU processes the FIT before executing the first BIOS instruction located at the reset vector(0xFFFFFFF0)**

![avatar](https://github.com/chenc2/Notes/blob/master/Images/FIT%20Table%20Payload.png)
