# Capsule Payload

## Overview
![avatar](https://github.com/chenc2/Notes/blob/master/Images/UEFI%20Capsule%20Payload.png)

## 1. EFI Capsule Header
- **Type, EFI_CAPSULE_HEADER**
- **Size of EFI_CAPSULE_HEADER is 28**
``` C
///
/// EFI Capsule Header.
///
typedef struct {
  ///
  /// A GUID that defines the contents of a capsule.
  ///
  EFI_GUID          CapsuleGuid;
  ///
  /// The size of the capsule header. This may be larger than the size of
  /// the EFI_CAPSULE_HEADER since CapsuleGuid may imply
  /// extended header entries
  ///
  UINT32            HeaderSize;
  ///
  /// Bit-mapped list describing the capsule attributes. The Flag values
  /// of 0x0000 - 0xFFFF are defined by CapsuleGuid. Flag values
  /// of 0x10000 - 0xFFFFFFFF are defined by this specification
  ///
  UINT32            Flags;
  ///
  /// Size in bytes of the capsule.
  ///
  UINT32            CapsuleImageSize;
} EFI_CAPSULE_HEADER;
```

## 2. Capsule Body
- **This part will be processed by ProcessFmpCapsuleImage(DxeCapsuleLib.c) function**
- **Call StartFmpImage to start all the drivers within capsule**
- **Process all payloads with SetFmpImageData(DxeCapsuleLib.c) function**

### 2.1 EFI_FIRMWARE_MANAGEMENT_CAPSULE_HEADER
- **The count of Optional drivers is indicated by EmbeddedDriverCount field**
- **The count of Payload is indicated by PayloadItemCount field**
``` C
typedef struct {
  UINT32 Version;

  ///
  /// The number of drivers included in the capsule and the number of corresponding
  /// offsets stored in ItemOffsetList array.
  ///
  UINT16 EmbeddedDriverCount;

  ///
  /// The number of payload items included in the capsule and the number of
  /// corresponding offsets stored in the ItemOffsetList array.
  ///
  UINT16 PayloadItemCount;

  ///
  /// Variable length array of dimension [EmbeddedDriverCount + PayloadItemCount]
  /// containing offsets of each of the drivers and payload items contained within the capsule
  ///
  // UINT64 ItemOffsetList[];
} EFI_FIRMWARE_MANAGEMENT_CAPSULE_HEADER;
```

### 2.2 Optional Driver 1
### Optional Driver ...
### Optional Driver n

### 2.3 Payload 1
![avatar](https://github.com/chenc2/Notes/blob/master/Images/UEFI%20Capsule%20Payload%201.png)
- **Payload part will be processed by SetFmpImageData(DxeCapsuleLib.c) function**
- **Locate protocol gEfiFirmwareManagementProtocolGuid**
- **Set the pointer Image to point to the Binary Update Image**
- **Set the pointer VendorCode to point to the Vendor Code Byes**
- **Call Fmp->SetImage enter SetTheImage of FmpDxe driver**
#### 2.3.1 EFI_FIRMWARE_MANAGEMENT_CAPSULE_IMAGE_HEADER
#### 2.3.2 Binary Update Image
- **Processed with SetTheImage function in FmpDxe driver**
- **Parse EFI_FIRMWARE_IMAGE_AUTHENTICATION and FMP_PAYLOAD_HEADER**
- **Call FmpDeviceSetImage to update the Real Binary Data**
##### 2.3.2.1 EFI_FIRMWARE_IMAGE_AUTHENTICATION
- **Check whether the signature of the following tow parts are correct**
##### 2.3.2.2 FMP_PAYLOAD_HEADER
``` C
typedef struct {
  UINT32  Signature;
  UINT32  HeaderSize;
  UINT32  FwVersion;
  UINT32  LowestSupportedVersion;
} FMP_PAYLOAD_HEADER;
```
##### 2.3.2.3 Real Binary Data
- **Implement related on different platform**
- **ICL platform for uCode udpate**
  - **Full : XDR + FvHeader + Version.ffs + Padding.ffs + uCodeArray.ffs**
  - **Bgup : XDR + FvHeader + Version.ffs + Padding.ffs + uCodeArray.ffs + XDR + BgupContent**
  - **Slot : XDR(0) + XDR(0) + XDR + Version.ffs + XDR + uCodeArray.ffs(Just support one patch in current solution)**
  ``` C
  typedef struct {
    UINT32  Version;
    UINT32  LowestSupportedVersion;
    CHAR16  VersionString[0];
  } INTEL_MICROCODE_VERSION_FFS_DATA;
  ```
#### 2.3.3 Vendor Code Byes

### Payload ...
### Payload n
