# Capsule Update

## Overview
&ensp;&ensp;&ensp;&ensp;UEFI capsule services already defined in UEFI specification, and the user could call this service pass the capsule files to the firmware. The flash device is locked during the system boot, so the capsule will be processed on the next boot phase. And report the information via ESRT which is also defined in UEFI specification. For detail of capsule image format, please refer: https://github.com/chenc2/Notes/blob/master/Capsule_Payload.md

&ensp;&ensp;&ensp;&ensp;On the next boot time, a PEI driver named CapsulePei(defined in MdeModulePkg\Universal\CapsulePei folder) will produce EFI_PEI_CAPSULE_PPI which is used to handle UEFI capsule coalesce process. If found a valid capsule, will set boot mode to BOOT_ON_FLASH_UPDATE, pass the coalesced capsule file to DXE pahse via HOB.

&ensp;&ensp;&ensp;&ensp;The coalesced capsule will be processed by DxeCapsuleLib driver, if the capsule image is a UEFI defined FMP capsule, the ProcessFmpCapsuleImage is called. This function will search all FMP instances, and then try to call SetImage with each FMP instance. The FMP instance driver is platform implementation-related, also could say, platform as a consumer to consume the capsule file.

## Implementation Details

### Capsule in Memory

### Capsule on Disk

## References

[1] https://github.com/tianocore/tianocore.github.io/wiki/UEFI-Capsule-on-Disk-Introducation
