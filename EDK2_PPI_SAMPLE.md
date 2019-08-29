# Edk2 PPI Sample

## PEIM Test Driver
``` C
#include <Library/DebugLib.h>
#include <Library/PeiServicesLib.h>

EFI_GUID PEIM_TEST_GUID = { 0x9909EAA6, 0x0C72, 0x44A4,{ 0x8C,0xD6,0x52,0xED,0xEA,0x3C,0x0A,0x41 } };

EFI_STATUS
EFIAPI
PeimTestPrint(
  IN CHAR16 *Msg
  )
{
  DEBUG((DEBUG_INFO, "%s\n", Msg));
  return EFI_SUCCESS;
}

typedef EFI_STATUS(EFIAPI *PEIM_TEST_PRINT_PTR)(CHAR16 *Msg);

typedef struct _PEIM_TEST_PRINT_PROTOCOL {
  PEIM_TEST_PRINT_PTR PeimTestPrintPtr;
} PEIM_TEST_PRINT_PROTOCOL;

PEIM_TEST_PRINT_PROTOCOL PeimTestPrintProtocol = {
  PeimTestPrint
};

///
/// Create a PPI object
///
EFI_PEI_PPI_DESCRIPTOR     mPpiTestPpi = {
  (EFI_PEI_PPI_DESCRIPTOR_PPI | EFI_PEI_PPI_DESCRIPTOR_TERMINATE_LIST),
  &PEIM_TEST_GUID,
  &PeimTestPrintProtocol
};

EFI_STATUS
EFIAPI
PeimTestEntryPoint(
  IN       EFI_PEI_FILE_HANDLE  FileHandle,
  IN CONST EFI_PEI_SERVICES     **PeiServices
)
{
  EFI_STATUS Status;

  DEBUG((DEBUG_INFO, "Entry Peim Test Module, Try to install PeimTestPrintProtocol into system!\n"));

  ///
  /// Install PPI into system
  ///
  Status = PeiServicesInstallPpi(&mPpiTestPpi);
  if (EFI_ERROR(Status)) {
    DEBUG((DEBUG_INFO, "Instal PPI fail!\n"));
    return Status;
  }

  DEBUG((DEBUG_INFO, "Instal PPI success!\n"));
  return Status;
}
```

``` inf
[Defines] 
  INF_VERSION = 0x00010006 
  BASE_NAME = PeimTest 
  FILE_GUID = E7EC9FF0-5E42-477F-B705-199ECC26BCA2
  MODULE_TYPE = PEIM
  VERSION_STRING = 1.0
  ENTRY_POINT = PeimTestEntryPoint 
  
[Sources] 
  PeimTest.c 
  
[Packages]
  MdePkg/MdePkg.dec
  
[LibraryClasses]
  BaseLib
  PeimEntryPoint
  DebugLib
  PeiServicesLib
  PrintLib

[depex]
  TRUE
```

## PEIM Caller Driver
``` C
#include <Library/DebugLib.h>
#include <Library/PeiServicesLib.h>

EFI_GUID PEIM_TEST_GUID = { 0x9909EAA6, 0x0C72, 0x44A4,{ 0x8C,0xD6,0x52,0xED,0xEA,0x3C,0x0A,0x41 } };

typedef EFI_STATUS(EFIAPI *PEIM_TEST_PRINT_PTR)(CHAR16 *Msg);

typedef struct _PEIM_TEST_PRINT_PROTOCOL {
  PEIM_TEST_PRINT_PTR PeimTestPrintPtr;
} PEIM_TEST_PRINT_PROTOCOL;

EFI_STATUS
EFIAPI
PeimCallerEntryPoint(
  IN       EFI_PEI_FILE_HANDLE  FileHandle,
  IN CONST EFI_PEI_SERVICES     **PeiServices
)
{
  EFI_STATUS  Status;
  PEIM_TEST_PRINT_PROTOCOL *mPeimTestProtocol;

  mPeimTestProtocol = NULL;

  DEBUG((DEBUG_INFO, "Try to locate PEIM_TEST_PRINT_PROTOCOL PPI\n"));
  ///
  /// Locate PPI by GUID
  ///
  Status = PeiServicesLocatePpi(
             &PEIM_TEST_GUID,
             0,
             NULL,
             (VOID **)&mPeimTestProtocol
             );
  if (EFI_ERROR(Status)) {
    DEBUG((DEBUG_INFO, "Locate PPI Fail\n"));
    return Status;
  }

  DEBUG((DEBUG_INFO, "Locate PPI Success\n"));
  ///
  /// Call API
  ///
  mPeimTestProtocol->PeimTestPrintPtr(L"Call PeimTestPrintPtr API by PPI!\n");

  return Status;
}
```

``` inf
[Defines] 
  INF_VERSION = 0x00010006 
  BASE_NAME = PeimCaller 
  FILE_GUID = 31809A75-4645-44C3-8F56-FBD4143E4246
  MODULE_TYPE = PEIM
  VERSION_STRING = 1.0
  ENTRY_POINT = PeimCallerEntryPoint 
  
[Sources] 
  PeimCaller.c 
  
[Packages]
  MdePkg/MdePkg.dec
  
[LibraryClasses]
  BaseLib
  PeimEntryPoint
  DebugLib
  PeiServicesLib

[depex]
  TRUE
```
