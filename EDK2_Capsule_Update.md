/** @internal
@file
  This file contains doxygen Fsp Integration Guide

@copyright
  Copyright (c) 2015 - 2020 Intel Corporation. All rights reserved
  This software and associated documentation (if any) is furnished
  under a license and may only be used or copied in accordance
  with the terms of the license. Except as permitted by such
  license, no part of this software or documentation may be
  reproduced, stored in a retrieval system, or transmitted in any
  form or by any means without the express written consent of
  Intel Corporation.
  This file contains an 'Intel Peripheral Driver' and uniquely
  identified as "Intel Reference Module" and is
  licensed for Intel CPUs and chipsets under the terms of your
  license agreement with Intel or your vendor.  This file may
  be modified by the user, subject to additional terms of the
  license agreement
**/

// SECTION 1
/**
@if gen_html
  @mainpage 1 Introduction
@else
  @mainpage Introduction
@endif

@if gen_html
## 1.1 Purpose
@else
# 1.1 Purpose
@endif
  The purpose of this document is to describe the steps required to integrate the Intel&reg; Firmware
  Support Package (FSP) into a boot loader solution. It supports **TigerLake** platforms with **TigerLake**
  processor and **TigerLake** Platform Controller Hub (PCH).</P>

@if gen_html
## 1.2 Intended Audience
@else
# 1.2 Intended Audience
@endif
  This document is targeted at all platform and system developers who need to consume FSP binaries
  in their boot loader solutions. This includes, but is not limited to: system BIOS developers, boot
  loader developers, system integrators, as well as end users.</P>

@if gen_html
## 1.3 Related Documents
@else
# 1.3 Related Documents
@endif
  - *Platform Initialization (PI) Specification v1.7* located at http://www.uefi.org/specifications
  - *Intel&reg; Firmware Support Package: External Architecture Specification (EAS) v2.1* located at
  https://cdrdv2.intel.com/v1/dl/getContent/611786
  - *Boot Setting File Specification (BSF) v1.0* https://firmware.intel.com/sites/default/files/BSF_1_0.pdf
  - *Binary Configuration Tool for Intel&reg; Firmware Support Package* available at http://www.intel.com/fsp

@if gen_html
## 1.4 Acronyms and Terminology
@else
# 1.4 Acronyms and Terminology
@endif

Acronym | Definition
--------|-----------
BCT     | Binary Configuration Tool
BDSM    | Base Data Of Stolen Memory
BSF     | Boot Setting File
BSP     | Boot Strap Processor
BWG     | BIOS Writer&apos;s Guide
CAR     | Cache As Ram
CRB     | Customer Reference Board
DPR     | DMA Protected Range
FIT     | Firmware Interface Table
FSP     | Firmware Support Package
FSP API | Firmware Support Package Interface
FW      | Firmware
GTT     | Graphics Translation Table
IED     | Intel Enhanced Debug
IFWI    | Integrated Firmware Image
IOT     | Internal Observation Trace
MRC     | Memory Reference Code (Memory Init code encapsulated by FSP-M)
MOT     | Memory Observation Trace
PCH     | Platform Controller Hub
PMC     | Power Management Controller
PMRR    | Protected Memory Range Reporting
REMAP   | Remapped Memory Area
RVP     | Reference and Validation Platform
SBSP    | System BSP
SMI     | System Management Interrupt
SMM     | System Management Mode
SMRAM   | System Management Mode RAM
SPI     | Serial Peripheral Interface
TOLUD   | Top of Low Usable Memory
TOUUD   | Top of Upper Usable Memory
TSEG    | Memory Reserved at the Top of Memory to be used as SMRAM
UPD     | Updatable Product Data

**/

// SECTION 2
/**
@if gen_html
  @page  fsp_overview 2 Overview
@else
  @page  fsp_overview Overview
@endif

@if gen_html
## 2.1 Technical Overview
@else
# 2.1 Technical Overview
@endif
  The *Intel&reg; Firmware Support Package (FSP)* provides chipset and processor initialization in a
  format that can easily be incorporated into many existing boot loaders.

  The FSP will perform the necessary initialization steps as documented in the BWG including
  initialization of the CPU, memory controller, chipset and certain bus interfaces, if necessary.

  FSP is not a stand-alone boot loader; therefore it needs to be integrated into a host boot loader
  to carry out other boot loader functions, such as: initializing non-Intel components, conducting
  bus enumeration, and discovering devices in the system and all industry standard initialization.

  The FSP binary can be integrated easily into many different boot loaders, such as Coreboot,
  EDKII MinPlatform, Intel&reg; Slim Bootloader, etc. and also into the embedded OS directly.

  Below are some required steps for the integration:

    - **Customizing**
        The static FSP configuration parameters are part of the FSP binary and can be customized
        by tools provided by Intel. This step is optional as configuration data may also be
        provided at runtime.

    - **Rebasing**
        The FSP is not Position Independent Code (PIC) and the whole FSP has to be rebased if it is placed
        at a location which is different from the preferred address during build process.

    - **Placing**
        Once the FSP binary is ready for integration, the boot loader build process needs to be modified to
        place this FSP binary at the specific rebasing location identified above.

    - **Interfacing**
        The boot loader needs to add code to setup the operating environment for the FSP, call the FSP with
        correct parameters and parse the FSP output to retrieve the necessary information returned by the FSP.

@if gen_html
## 2.2 FSP Distribution Package
@else
# 2.2 FSP Distribution Package
@endif
  - The FSP distribution package contains the following:
    - FSP Binary
    - FSP Integration Guide
    - BSF Configuration File
    - Data Structure Header Files

  - The FSP configuration utility called BCT is available as a separate package.
    It can be downloaded from link mentioned in Section 1.3.

@if gen_html
### 2.2.1 Package Layout
@else
## 2.2.1 Package Layout
@endif
  - **Docs (Auto generated)**
      - FSP_Integration_Guide.pdf
      - FSP_Integration_Guide.chm

  - **Include**
      - FsptUpd.h, FspmUpd.h and FspsUpd.h (FSP UPD structure and related definitions)
      - GpioSampleDef.h (Sample enum definitions for GPIO table)
  - FspBinPkg.dec (EDKII declaration file for package)
  - Fsp.bsf (BSF file for configuring the data using BCT tool)
  - Fsp.fd (FSP Binary)
**/

// SECTION 3
/**
@if gen_html
  @page fsp_integration 3 FSP Integration
@else
  @page fsp_integration FSP Integration
@endif

@if gen_html
## 3.1 Assumptions Used in this Document
@else
# 3.1 Assumptions Used in this Document
@endif
  The FSP for this platform is built with a preferred base address given by \ref PcdFspAreaBaseAddress
  and so the reference code provided in the document assumes that the FSP is placed at this base address
  during the final boot loader build. Users may rebase the FSP binary at a different location with
  Intel&apos;s Binary Configuration Tool (BCT) or SplitFspBin.py before integrating to the boot loader.

  For other assumptions and conventions, please refer to Chapter 8 and 9 of the FSP External Architecture
  Specification version 2.1.

@if gen_html
## 3.2 Boot Flow
@else
# 3.2 Boot Flow
@endif
  Please refer Chapter 7 in the FSP External Architecture Specification version 2.1 for Boot flow chart.
  The FSP for this platform supports dispatch mode, see Chapter 7 and 9 of the FSP
  External Architecture Specification version 2.1 for a description of dispatch mode.

@if gen_html
## 3.3 FSP INFO Header
@else
# 3.3 FSP INFO Header
@endif
  The FSP has an Information Header that provides critical information that is required by the
  bootloader to successfully interface with the FSP. The structure of the FSP Information Header
  is documented in the FSP External Architecture Specification version 2.1 with a HeaderRevision of 4.

@if gen_html
## 3.4 FSP Image ID and Revision
@else
# 3.4 FSP Image ID and Revision
@endif
  FSP information header contains an Image ID field and an Image Revision field that provide the
  identification and revision information of the FSP binary. It is important to verify these fields
  while integrating the FSP as API parameters could change over different FSP IDs and revisions.
  All the FSP FV segments(FSP-T, FSP-M and FSP-S) must have same FSP Image ID and revision number,
  using FV segments with different revision numbers in a single FSP image is not valid. The FSP
  API parameters documented in this integration guide are applicable for the Image ID and
  Revision specified as below.

  The FSP ImageId string in the FSP information header is given by <strong> \ref PcdFspImageIdString
  </strong> and the ImageRevision field is given by \ref PcdSiliconInitVersionMajor
  "SiliconInitVersionMajor|Minor|FspVersionRevision|FspVersionBuild" (Ex:0x0A001460).

@if gen_html
## 3.5 FSP Global Data
@else
# 3.5 FSP Global Data
@endif
  FSP uses some amount of TempRam area to store FSP global data which contains some critical data like
  pointers to FSP information headers and UPD configuration regions, FSP/Bootloader stack pointers
  required for stack switching etc. HPET Timer register(2) \ref PcdGlobalDataPointerAddress
  is reserved to store address of this global data, and hence boot loader should not use this register
  for any other purpose. If TempRAM initialization is done by boot loader, then HPET has to be initialized
  to the base so that access to the register will work fine.

@if gen_html
## 3.6 Memory Map
@else
# 3.6 Memory Map
@endif
  Below diagram represents the memory map allocated by FSP including the FSP specific regions.
@image html MemoryMap.jpg "System Memory Map"
@image latex MemoryMap.jpg "System Memory Map"
**/

// SECTION 4
/**
@if gen_html
  @page fsp_dispatch_mode 4 FSP Dispatch Mode
@else
  @page fsp_dispatch_mode FSP Dispatch Mode
@endif

@htmlonly
<!--
@endhtmlonly
@latexonly
\begin{comment}
@endlatexonly
@subpage fsp_dispatch_policy_init
@subpage config_blocks
@subpage fsp_error_info
@subpage fsp_dispatch_integration
@htmlonly
-->
@endhtmlonly
@latexonly
\end{comment}
@endlatexonly

@if gen_html
## Overview
@else
# Overview
@endif

  The FSP for this platform supports dispatch mode. Support for dispatch mode can be detected by checking
  if FSP_INFO_HEADER->ImageAttribute[BIT1] == 1. Dispatch mode is intended to enable FSP to integrate well
  in to UEFI bootloader implementations. Dispatch mode implements a boot flow that is as close to a standard
  UEFI boot flow as possible. In dispatch mode, the FSP is exposed as standard Firmware Volumes (FVs) directly
  to the bootloader. The PEIMs in these FVs are executed in the same PEI environment as the boot loader. In
  dispatch mode, the PPI database, PCD database, and HOB list are shared between the boot loader and the FSP.

@image html fsp_flow_diagram.png
@image latex fsp_flow_diagram.eps

  Blue blocks are from the FSP binary and green blocks are from the bootloader. Blocks with mixed colors
  indicate that both bootloader and FSP modules are dispatched during that phase of the boot flow. In
  dispatch mode, the NotifyPhase() API is not used. Instead, FSP-S contains DXE drivers that implement
  the native callbacks on equivalent events for each of the NotifyPhase() invocations.

  In dispatch mode, the the PPI database and PCD database are used for providing policy data from the
  bootloader to FSP. Because these mechanisms provide a great deal of flexibility, dispatch mode does
  not constrain the method for passing policy data as strongly as API mode. The following sections
  describe the dispatch mode policy initialization flow used specifically for this platform.

@if gen_html
  @page fsp_dispatch_policy_init 4.1 Dispatch Mode Policy Init
@else
  @page fsp_dispatch_policy_init Dispatch Mode Policy Init
@endif

  For the plurality of platform designs, most of the policy options provided by FSP do not need to be
  modified by the bootloader. To ease this common case, policy initialization has been broken in to two
  phases: 1. Policy Init and 2. Policy Update.

  Policy Init creates the policy data structures with all default policy values pre-populated. Policy
  Init is implemented using the <a href="https://en.wikipedia.org/wiki/Factory_method_pattern">factory
  method pattern</a>. The FSP provides an API to the bootloader for constructing the policy data
  structures. Because the factory method is provided by the FSP, backwards compatibility is made
  substantially easier. The FSP can be assured that the policy data structures match the definitions
  used at the time the FSP was compiled. As long as new fields are added to the bottom of policy
  structures, the bootloader may continue to use an older version of the policy data structures at
  compile time and retain compatibility with both new and old FSP binaries.

  There are two factory methods, one for FSP-M and one for FSP-S.
  @ref PEI_PREMEM_POLICY_INIT "PeiPreMemPolicyInit()" is provided by FSP-M,
  @ref PEI_POLICY_INIT "PeiPolicyInit()" is provided by FSP-S. These methods are exposed to the
  bootloader using PPIs, @ref _PEI_PREMEM_SI_DEFAULT_POLICY_INIT_PPI "PEI_PREMEM_SI_DEFAULT_POLICY_INIT_PPI"
  for FSP-M and @ref _PEI_SI_DEFAULT_POLICY_INIT_PPI "PEI_SI_DEFAULT_POLICY_INIT_PPI" for FSP-S. The
  usage of PPIs ensures ABI stability between the FSP and the bootloader.

  While Policy Init is implemented by the FSP, Policy Update is implemented by the bootloader. Policy
  Update uses a read-modify-write operation to apply any motherboard specific settings to the policy data
  while leaving the default values intact when appropriate. After Policy Update is done, FSP may run its
  SOC initialization code.

## 4.1.1 FSP-M Policy Initialization Flow
  The follow graph shows the policy initialization flow for FSP-M.

  @note The hollow arrow heads in the flow diagram below indicate that action by the PEI Foundation is
  required for the flow to proceed.

@dot
digraph G {
  splines=ortho;
  node [shape = box]; nodesep=0.5;
  graph [fontname = "Verdana"];
  node [fontname = "Verdana"];
  edge [fontname = "Verdana"];
  subgraph cluster_fsp_wrapper { label = "UEFI PI Boot Loader"; fontsize=14;rank=same;
    subgraph cluster_si_policy_pei_pre_mem { label = "SiliconPolicyPeiPreMem"; fontsize=13;rank=same;
      b1[label="SiliconPolicyPeiPreMemEntryPoint()" fontsize=12 style=filled color=thistle2];
      subgraph cluster_c0 { label = "SiliconPolicyInitLib"; fontsize=12;{rank=same; b2 b3}
        b2[label="SiliconPolicyInitPreMem()" fontsize=12 style=filled color=seagreen2];
        b3[label="SiliconPolicyDonePreMem()" fontsize=12 style=filled color=seagreen2];
      }
      subgraph cluster_c1 { label = "SiliconPolicyUpdateLib"; fontsize=12;{rank=same; b4}
        b4[label="SiliconPolicyUpdatePreMem()" fontsize=12 style=filled color=seagreen2];
      }
    }
  }
  subgraph ppis {rank="same";
    b5[label="PEI_PREMEM_SI_DEFAULT_POLICY_PPI" fontsize=12 style=filled color="#FFC000"];
    b6[label="SI_PREMEM_POLICY_PPI" fontsize=12 style=filled color="#FFC000"];
    b7[label="SI_PREMEM_POLICY_READY_PPI" fontsize=12 style=filled color="#FFC000"];
  }
  subgraph cluster_c2 {
    label = "FSP-M"; fontsize=14;{rank=same;}
    subgraph cluster_c3 { label = "SiInitPreMem"; fontsize=13;{rank=same; b10 b11 b12}
      b10[label="SiInitPrePolicy()" fontsize=12 style=filled color=lightblue];
      b11[label="PeiPreMemSiDefaultPolicyInit()" fontsize=12 style=filled color=lightblue];
      b12[label="SiInitPreMemOnPolicyReady()" fontsize=12 style=filled color=lightblue];
    }
  }

  b2 -> b5 [fontsize=12]
  b5 -> b11 [fontsize=12]
  b6 -> b4 [fontsize=12 arrowhead = empty]
  b4 -> b3 [fontsize=12]
  b3 -> b7 [fontsize=12]
  b7 -> b12 [fontsize=12 arrowhead = empty]
  b10 -> b5 [fontsize=12 constraint=false]
  b11 -> b6 [fontsize=12 constraint=false]
  b5 -> b1 [fontsize=12 arrowhead = empty]
  b1 -> b2 [fontsize=12]
  b10 -> b11 [style=invis]
  b11 -> b12 [style=invis]
}
@enddot
  @note The function names and PEIMs used by the UEFI PI boot loader are implementation dependent and may
  vary from those shown here. The function names and PEIMs used by MinPlatform are provided as an example.

  Execution of FSP-M begins after FspmWrapperInit() in
  IntelFsp2WrapperPkg/FspmWrapperPeim/FspmWrapperPeim.c installs a EFI_PEI_FIRMWARE_VOLUME_INFO_PPI
  instance for FSP-M. This causes the PEI foundation to become aware of the FV(s) in FSP-M, which
  schedules all PEIMs in FSP-M for dispatch by PEI. This results in the SiInitPreMem PEIM in
  FSP-M being executed.

  One of the actions performed by the SiInitPreMem entry-point is calling the SiInitPrePolicy()
  function, which installs the
  @ref _PEI_PREMEM_SI_DEFAULT_POLICY_INIT_PPI "PEI_PREMEM_SI_DEFAULT_POLICY_INIT_PPI". The DEPEX for
  MinPlatformPkg/PlatformInit/SiliconPolicyPei/SiliconPolicyPeiPreMem.inf requires
  @ref _PEI_PREMEM_SI_DEFAULT_POLICY_INIT_PPI "PEI_PREMEM_SI_DEFAULT_POLICY_INIT_PPI" to exist in
  the PPI database before SiliconPolicyPeiPreMem can run. While SiliconPolicyPeiPreMem.inf does not
  directly add @ref _PEI_PREMEM_SI_DEFAULT_POLICY_INIT_PPI "PEI_PREMEM_SI_DEFAULT_POLICY_INIT_PPI"
  to the DEPEX, SiliconPolicyPeiPreMem.inf does statically link against
  TigerlakeSiliconPkg/Library/PeiSiliconPolicyInitLib/PeiPreMemSiliconPolicyInitLib.inf, which adds
  @ref _PEI_PREMEM_SI_DEFAULT_POLICY_INIT_PPI "PEI_PREMEM_SI_DEFAULT_POLICY_INIT_PPI" to the DEPEX.
  After the SiInitPreMem entry-point completes the dependency will be satisfied, making
  SiliconPolicyPeiPreMem eligible for dispatch.

  Next SiliconPolicyPeiPreMem PEIM will run. SiliconPolicyPeiPreMemEntryPoint() in
  MinPlatformPkg/PlatformInit/SiliconPolicyPei/SiliconPolicyPeiPreMem.c will call
  SiliconPolicyInitPreMem() in
  TigerlakeSiliconPkg/Library/PeiSiliconPolicyInitLib/PeiPolicyInitPreMem.c. This function will
  locate the instance of
  @ref _PEI_PREMEM_SI_DEFAULT_POLICY_INIT_PPI "PEI_PREMEM_SI_DEFAULT_POLICY_INIT_PPI" previously
  created by SiInitPreMem and use it to call PeiPreMemSiDefaultPolicyInit() in SiInitPreMem.
  PeiPreMemSiDefaultPolicyInit() will construct
  @ref _SI_PREMEM_POLICY_STRUCT "SI_PREMEM_POLICY_PPI", which contains all the policy data needed
  by FSP-M. SiliconPolicyInitPreMem() will then locate
  @ref _SI_PREMEM_POLICY_STRUCT "SI_PREMEM_POLICY_PPI" and return it to
  SiliconPolicyPeiPreMemEntryPoint().

  Next, SiliconPolicyPeiPreMemEntryPoint() takes the pointer to the policy data returned by
  SiliconPolicyInitPreMem() and passes it to SiliconPolicyUpdatePreMem(). SiliconPolicyUpdatePreMem()
  is part of SiliconPolicyUpdateLib, which is statically linked to SiliconPolicyPeiPreMem.
  SiliconPolicyUpdateLib is special since it is only piece of motherboard specific code in this flow.
  An example of SiliconPolicyUpdatePreMem() is seen in
  TigerlakeOpenBoardPkg/TigerlakeURvp/Policy/Library/PeiSiliconPolicyUpdateLib/PeiSiliconPolicyUpdateLib.c.
  SiliconPolicyUpdatePreMem() uses a read-modify-write operation to apply any motherboard specific
  settings to the policy data while leaving the default values intact when appropriate.

  After SiliconPolicyUpdatePreMem() applies any motherboard specific updates,
  SiliconPolicyPeiPreMemEntryPoint() calls SiliconPolicyDonePreMem() in
  TigerlakeSiliconPkg/Library/PeiSiliconPolicyInitLib/PeiPolicyInitPreMem.c. SiliconPolicyDonePreMem()
  dumps the policy data to the debug log (on a DEBUG build of MinPlatform only) and installs
  the SI_PREMEM_POLICY_READY_PPI. SI_PREMEM_POLICY_READY_PPI tells FSP-M that everything is ready
  and that it can run the SOC init code now. Installing SI_PREMEM_POLICY_READY_PPI causes the PEI
  Foundation to signal a PeiServices->NotifyPpi() callback previously set up by SiInitPreMem. This
  callback invokes SiInitPreMemOnPolicyReady() in SiInitPreMem, which runs MRC and all the other SOC
  init code in FSP-M.

## 4.1.1 FSP-S Policy Initialization Flow
  Policy Initialization for FSP-S follows essentially the same flow as FSP-M. The primary difference
  is function and PPI names do not include the "PreMem" qualifier.

@dot
digraph G {
  splines=ortho;
  node [shape = box]; nodesep=0.5;
  graph [fontname = "Verdana"];
  node [fontname = "Verdana"];
  edge [fontname = "Verdana"];
  subgraph cluster_fsp_wrapper { label = "UEFI PI Boot Loader"; fontsize=14;rank=same;
    subgraph cluster_si_policy_pei_pre_mem { label = "SiliconPolicyPeiPostMem"; fontsize=13;rank=same;
      b1[label="SiliconPolicyPeiPostMemEntryPoint()" fontsize=12 style=filled color=thistle2];
      subgraph cluster_c0 { label = "SiliconPolicyInitLib"; fontsize=12;{rank=same; b2 b3}
        b2[label="SiliconPolicyInitPostMem()" fontsize=12 style=filled color=seagreen2];
        b3[label="SiliconPolicyDonePostMem()" fontsize=12 style=filled color=seagreen2];
      }
      subgraph cluster_c1 { label = "SiliconPolicyUpdateLib"; fontsize=12;{rank=same; b4}
        b4[label="SiliconPolicyUpdatePostMem()" fontsize=12 style=filled color=seagreen2];
      }
    }
  }
  subgraph ppis {rank="same";
    b5[label="PEI_SI_DEFAULT_POLICY_INIT_PPI" fontsize=12 style=filled color="#FFC000"];
    b6[label="SI_POLICY_PPI" fontsize=12 style=filled color="#FFC000"];
    b7[label="SI_POLICY_READY_PPI" fontsize=12 style=filled color="#FFC000"];
  }
  subgraph cluster_c2 {
    label = "FSP-S"; fontsize=14;{rank=same;}
    subgraph cluster_c3 { label = "SiInit"; fontsize=13;{rank=same; b10 b11 b12}
      b10[label="SiInit()" fontsize=12 style=filled color=lightblue];
      b11[label="PeiSiDefaultPolicyInit()" fontsize=12 style=filled color=lightblue];
      b12[label="SiInitPostMemOnPolicy()" fontsize=12 style=filled color=lightblue];
    }
  }

  b2 -> b5 [fontsize=12]
  b5 -> b11 [fontsize=12]
  b6 -> b4 [fontsize=12 arrowhead = empty]
  b4 -> b3 [fontsize=12]
  b3 -> b7 [fontsize=12]
  b7 -> b12 [fontsize=12 arrowhead = empty]
  b10 -> b5 [fontsize=12 constraint=false]
  b11 -> b6 [fontsize=12 constraint=false]
  b5 -> b1 [fontsize=12 arrowhead = empty]
  b1 -> b2 [fontsize=12]
  b10 -> b11 [style=invis]
  b11 -> b12 [style=invis]
}
@enddot

@if gen_html
  @page config_blocks 4.2 Config Blocks
@else
  @page config_blocks Config Blocks
@endif

@if gen_html
## Overview
@else
# Overview
@endif

  Config Blocks are a binary data serialization format with several unique properties that are useful
  for firmware implementations.

  The serialization format is simple to implement in the C language, minimizing the code needed to
  implement it; this minimizes the size of the FSP binary. Second, the format is binary backwards
  compatible; enabling the bootloader to use an older version of the data structure at compile time
  and retain compatibility with both new and old FSP binaries. Third, the format is mutable in memory
  without requiring the data to be re-serialized. Finally, the format is position independent with no
  need for a base address or a rebase operation. This allows the data structure to be moved from
  pre-memory to post-memory without applying any fixups.

  Data in @ref _SI_PREMEM_POLICY_STRUCT "SI_PREMEM_POLICY_PPI" and
  @ref _SI_POLICY_STRUCT "SI_POLICY_PPI" are stored using the Config Block format.

@if gen_html
## 4.2.1 Config Block Data Format
@else
# 4.2.1 Config Block Data Format
@endif

  In essence, Config Blocks are multiple C structures that are stored in a single continuous memory
  range using run length encoding.

@image html config_block.png
@image latex config_block.eps

  The overall structure is termed a Config Block Table. The Config Block Table starts with a
  @ref _CONFIG_BLOCK_TABLE_STRUCT "CONFIG_BLOCK_TABLE_HEADER" structure. This header indicates the
  number of Config Blocks in the Config Block Table, the total size of the table, and the amount of
  free space remaining in the table. The table may be over-provisioned, which allows additional
  Config Blocks to be added after the table's initial creation.

  The size of all Config Blocks must be an even multiple of 32-bits as all Config Blocks are DWORD
  aligned. When enumerating a Config Block Table one may assume that all Config Blocks are packed
  back-to-back, hence the enumeration algorithm can use the length of each config block to find the
  next config block in the table.

  Each Config Block has a GUID that identifies which C structure the Config Block contains. All
  Config Blocks start with a standardized @ref _CONFIG_BLOCK_HEADER "CONFIG_BLOCK_HEADER", which
  allows all the structures in the table to be enumerated even if the format of the data payload
  inside each Config Block is unknown.
~~~
  typedef struct _CONFIG_BLOCK_HEADER {
    EFI_HOB_GUID_TYPE GuidHob;                      ///< Offset 0-23  GUID extension HOB header
    UINT8             Revision;                     ///< Offset 24    Revision of this config block
    UINT8             Attributes;                   ///< Offset 25    The main revision for config block
    UINT8             Reserved[2];                  ///< Offset 26-27 Reserved for future use
  } CONFIG_BLOCK_HEADER;
~~~
  The @ref _CONFIG_BLOCK_HEADER "CONFIG_BLOCK_HEADER" uses the header of a GUID HOB to store its
  length and GUID. This makes it easy to copy Config Blocks to the HOB list. Copying Config Blocks
  to the HOB list is a common use case. Often the ACPI code uses policies that were initially
  supplied by a Config Block. Copying the Config Block to the HOB list makes it easy for DXE phase
  to copy any needed policies to the ACPI global NVS memory later.

  The Revision field assists with binary backward compatibility. Whenever a new field is added to
  a Config Block, the new field is added to the bottom of the Config Block and the Revision is
  incremented by 1. Thus if a bootloader wishes to retain compatibility with new and old
  versions of the FSP binary, it may do so by either by only using fields that are present in
  Revision 1 of the Config Block or by checking the Revision number before modifying fields added
  since Revision 1.

  The @ref _CONFIG_BLOCK_TABLE_STRUCT "CONFIG_BLOCK_TABLE_HEADER" builds upon the
  @ref _CONFIG_BLOCK_HEADER "CONFIG_BLOCK_HEADER":
~~~
  typedef struct _CONFIG_BLOCK_TABLE_STRUCT {
    CONFIG_BLOCK_HEADER            Header;          ///< Offset 0-27  GUID number for main entry of config block
    UINT8                          Rsvd0[2];        ///< Offset 28-29 Reserved for future use
    UINT16                         NumberOfBlocks;  ///< Offset 30-31 Number of config blocks (N)
    UINT32                         AvailableSize;   ///< Offset 32-35 Current config block table size
  ///
  /// Individual Config Block Structures are added starting here
  ///
  } CONFIG_BLOCK_TABLE_HEADER;
~~~

@if gen_html
## 4.2.2 Config Block Library APIs
@else
# 4.2.2 Config Block Library APIs
@endif

  To assist in the creation and parsing of Config Blocks, Intel has provided an open source Config
  Block library in
  <a href="https://github.com/tianocore/edk2-platforms/tree/master/Silicon/Intel/IntelSiliconPkg/Library/BaseConfigBlockLib">
  IntelSiliconPkg/Library/BaseConfigBlockLib</a>. This library provides the following functions:

  - @ref GetConfigBlock "GetConfigBlock()"
  - @ref AddConfigBlock "AddConfigBlock()"
  - @ref CreateConfigBlockTable "CreateConfigBlockTable()"

  In most cases, bootloaders will only need to call @ref GetConfigBlock "GetConfigBlock()".

@if gen_html
## 4.2.3 Config Blocks used by FSP-M
@else
# 4.2.3 Config Blocks used by FSP-M
@endif

  On **TigerLake** platforms @ref _SI_PREMEM_POLICY_STRUCT "SI_PREMEM_POLICY_PPI" contains a
  Config Block Table.
  @ref _PEI_PREMEM_SI_DEFAULT_POLICY_INIT_PPI "PEI_PREMEM_SI_DEFAULT_POLICY_INIT_PPI" will
  construct this Config Block Table with all the below Config Blocks pre-populated. Additionally,
  all the Config Blocks will be initialized with default policy values as described in section
  4.1.

  @ref _SI_PREMEM_POLICY_STRUCT "SI_PREMEM_POLICY_PPI" contains the following Config Blocks:

  - SI_PREMEM_CONFIG
  - HOST_BRIDGE_PREMEM_CONFIG
  - CPU_CONFIG_LIB_PREMEM_CONFIG
  - CPU_DMI_PREMEM_CONFIG
  - CPU_PCIE_RP_PREMEM_CONFIG
  - CPU_SECURITY_PREMEM_CONFIG
  - CPU_TRACE_HUB_PREMEM_CONFIG
  - CPU_TXT_PREMEM_CONFIG
  - GRAPHICS_PEI_PREMEM_CONFIG
  - HDAUDIO_PREMEM_CONFIG
  - HYBRID_GRAPHICS_CONFIG
  - IPU_PREMEM_CONFIG
  - ISH_PREMEM_CONFIG
  - ME_PEI_PREMEM_CONFIG
  - MEMORY_CONFIG_NO_CRC
  - MEMORY_CONFIGURATION
  - OVERCLOCKING_PREMEM_CONFIG
  - PCH_DCI_PREMEM_CONFIG
  - PCH_GENERAL_PREMEM_CONFIG
  - PCH_HSIO_PCIE_PREMEM_CONFIG
  - PCH_HSIO_SATA_PREMEM_CONFIG
  - PCH_LPC_PREMEM_CONFIG
  - PCH_PCIE_RP_PREMEM_CONFIG
  - PCH_SMBUS_PREMEM_CONFIG
  - PCH_WDT_PREMEM_CONFIG
  - PCIE_PEI_PREMEM_CONFIG
  - PCIE_PREMEM_CONFIG
  - PRAM_PREMEM_CONFIG
  - SA_MISC_PEI_PREMEM_CONFIG
  - TCSS_PEI_PREMEM_CONFIG
  - TELEMETRY_PEI_PREMEM_CONFIG
  - TWOLM_PREMEM_CONFIG
  - VTD_CONFIG

@if gen_html
## 4.2.4 Config Blocks used by FSP-S
@else
# 4.2.4 Config Blocks used by FSP-S
@endif

  On **TigerLake** platforms @ref _SI_POLICY_STRUCT "SI_POLICY_PPI" contains a Config Block Table.
  @ref _PEI_SI_DEFAULT_POLICY_INIT_PPI "PEI_SI_DEFAULT_POLICY_INIT_PPI" will
  construct this Config Block Table with all the below Config Blocks pre-populated. Additionally,
  all the Config Blocks will be initialized with default policy values as described in section
  4.1.

  @ref _SI_POLICY_STRUCT "SI_POLICY_PPI" contains the following Config Blocks:

  - SI_CONFIG
  - HOST_BRIDGE_PEI_CONFIG
  - ADR_CONFIG
  - AMT_PEI_CONFIG
  - BIOS_GUARD_CONFIG
  - CNVI_CONFIG
  - CPU_CONFIG
  - CPU_PCIE_CONFIG
  - CPU_PID_TEST_CONFIG
  - CPU_POWER_MGMT_BASIC_CONFIG
  - CPU_POWER_MGMT_CUSTOM_CONFIG
  - CPU_POWER_MGMT_PSYS_CONFIG
  - CPU_POWER_MGMT_TEST_CONFIG
  - CPU_POWER_MGMT_VR_CONFIG
  - CPU_TEST_CONFIG
  - GBE_CONFIG
  - GNA_CONFIG
  - GRAPHICS_PEI_CONFIG
  - HDAUDIO_CONFIG
  - HYBRID_STORAGE_CONFIG
  - IEH_CONFIG
  - ISH_CONFIG
  - ME_PEI_CONFIG
  - PCH_DMI_CONFIG
  - PCH_ESPI_CONFIG
  - PCH_FIVR_CONFIG
  - PCH_FLASH_PROTECTION_CONFIG
  - PCH_GENERAL_CONFIG
  - PCH_HSIO_CONFIG
  - PCH_INTERRUPT_CONFIG
  - PCH_IOAPIC_CONFIG
  - PCH_LOCK_DOWN_CONFIG
  - PCH_P2SB_CONFIG
  - PCH_PCIE_CONFIG
  - PCH_PM_CONFIG
  - @ref _PEI_ITBT_CONFIG "PEI_ITBT_CONFIG"
  - PSF_CONFIG
  - RST_CONFIG
  - RTC_CONFIG
  - SA_MISC_PEI_CONFIG
  - SATA_CONFIG
  - SERIAL_IO_CONFIG
  - TCSS_PEI_CONFIG
  - TELEMETRY_PEI_CONFIG
  - THC_CONFIG
  - THERMAL_CONFIG
  - TSN_CONFIG
  - USB_CONFIG
  - USB2_PHY_CONFIG
  - USB3_HSIO_CONFIG
  - VMD_PEI_CONFIG

@if gen_html
  @page fsp_error_info 4.3 FSP Error Information
@else
  @page fsp_error_info FSP Error Information
@endif

  In the case of a fatal error occurring during the execution of the FSP, it may not be possible
  for the FSP to continue. If a fatal error that prevents the successful completion of the FSP
  occurs, the FSP may use FSP_ERROR_INFO to report this error to the bootloader. During PEI
  phase, (*PeiServices)->ReportStatusCode() shall be used to transmit this error notification
  to the bootloader. During DXE phase, EFI_STATUS_CODE_PROTOCOL.ReportStatusCode() shall be used
  to transmit this error notification to the bootloader. The bootloader must ensure that
  ReportStatusCode() services are available before FSP-M begins execution.

~~~
typedef struct {
  EFI_STATUS_CODE_DATA    DataHeader;
  EFI_GUID                ErrorType;
  EFI_STATUS              Status;
} FSP_ERROR_INFO;
~~~

  FSP_ERROR_INFO is provided as the optional EFI_STATUS_CODE_DATA parameter to ReportStatusCode().
  EFI_STATUS_CODE_DATA provides a CallerId GUID, this CallerId combined with the ErrorType GUID
  describes the error to the bootloader. The FSP for this platform implements the following
  CallerId GUIDs:

  CallerId                                                                               | Description
  ---------------------------------------------------------------------------------------|------------------
  <tt>{0x1f4dc7e9, 0x26ca, 0x4336, {0x8c, 0xe3, 0x39, 0x31, 0x03, 0xb5, 0xf3, 0xd7}}</tt>| ME
  <tt>{0x98230916, 0xe632, 0x49ff, {0x81, 0x81, 0x55, 0xce, 0xe5, 0x10, 0x36, 0x89}}</tt>| System Agent
  <tt>{0x5a47c211, 0x642f, 0x4f92, {0x9c, 0xb3, 0x7f, 0xeb, 0x93, 0xda, 0xdd, 0xba}}</tt>| MRC

  If the CallerId parameter is not a GUID in the table above, then it should be the GUID that
  identifies the PEIM or DXE driver which was executing at the time of the error.

  The following ErrorType GUIDs are implemented:

  ErrorType                                                                              | Description
  ---------------------------------------------------------------------------------------|------------------
  <tt>{0x948585c4, 0x76a4, 0x45bb, {0xbe, 0x6c, 0x39, 0x61, 0xc3, 0xab, 0xde, 0x15}}</tt>|ME EOP failure
  <tt>{0x8106a5cc, 0x30ba, 0x41cf, {0xa1, 0x78, 0x63, 0x38, 0x91, 0x11, 0xae, 0xb2}}</tt>|SA PEI GOP Init failure
  <tt>{0x348cc7fe, 0x1e9a, 0x4c7a, {0x86, 0x28, 0xae, 0x48, 0x5b, 0x42, 0x10, 0xf0}}</tt>|SA PEI GOP GetMode failure
  <tt>{0x5de1c071, 0x2c9c, 0x4a53, {0x80, 0x21, 0x4e, 0x80, 0xd2, 0x5d, 0x44, 0xa8}}</tt>|MRC training failure

  When the FSP calls ReportStatusCode(), the Type parameter's EFI_STATUS_CODE_TYPE_MASK must be
  EFI_ERROR_CODE with the EFI_STATUS_CODE_SEVERITY_MASK >= EFI_ERROR_UNRECOVERED. The Value and
  Instance parameters must be 0.

  The bootloader must register a listener for this status code. This listener should check if
  DataHeader.Type == STATUS_CODE_DATA_TYPE_FSP_ERROR_GUID to detect an FSP_ERROR_INFO notification.
  If an FSP_ERROR_INFO notification is encountered, the bootloader should assume that normal
  operation is no longer possible. In debug scenarios, this notification should be considered an
  ASSERT.

@if gen_html
  @page fsp_dispatch_integration 4.4 Dispatch Mode Integration
@else
  @page fsp_dispatch_integration Dispatch Mode Integration
@endif
  Dispatch Mode Integration Notes:

  1. The FSP for this platform contains PEIMs compiled for the IA32 architecture. The boot loader
     therefore must utilize a PEI Foundation compiled for the IA32 architecture.
  2. Since the FSP binary can be integrated into flash at any address, the boot loader has
     to report FSP FVs to the PEI and DXE dispatcher using PI specification defined mechanisms
     so PEIMs and DXE drivers inside the FSP Binary can be dispatched. FspmWrapperPeim and
     FspsWrapperPeim from IntelFsp2WrapperPkg can aid in implementing this.
  3. For this platform, FSP-T, FSP-M, and FSP-S contain 1 FV each.
  4. The FSP distribution package will include a DSC file which contains all DynamicEx
     PCDs consumed by the FSP binary. The boot loader should include the DSC during its build
     process so that any PCDs defined by this DSC file are included in the boot loader's PCD
     database. This enables the boot loader and FSP to share a single PCD database.
     - A NULL library (FspPcdListLibNull.inf) is included in the FSP distribution package.
       This library should be included in one of the boot loader's PEIMs.
       This ensures all DynamicEx PCDs used by the FSP are included in the boot loader's PCD
       database.
       One can fulfill this requirement by including the following code snippet in *BoardPkg.dsc:
~~~
        IntelFsp2WrapperPkg/FspmWrapperPeim/FspmWrapperPeim.inf {
          <LibraryClasses>
          !if gIntelFsp2WrapperTokenSpaceGuid.PcdFspModeSelection == 0
            #
            # In FSP Dispatch mode below dummy library should be linked to bootloader PEIM
            # to build all DynamicEx PCDs that FSP consumes into bootloader PCD database.
            #
            NULL|$(PLATFORM_FSP_BIN_PACKAGE)/Library/FspPcdListLib/FspPcdListLibNull.inf
          !endif
        }
~~~
  5. The boot loader must provide at minimum 256KB of stack and 128KB of HOB heap to execute
     FSP on this platform.
  6. In dispatch mode, the boot loader should not use FSP API calls described in
     chapter 5 of this document or chapter 8 of the FSP External Architecture
     Specification version 2.1. The TempRamInit API is the only exception, it is
     supported in both API mode and dispatch mode. All other APIs
     (MemoryInit, SiliconInit, etc.) should not be invoked.
  7. For dispatch mode, FSP contains x64 DXE drivers to replace the NotifyPhase API.
     This eliminates thunking from 64bit to 32bit when using FSP dispatch mode. The
     boot loader should remove S3EndOfPeiNotify and FspWrapperNotifyDxe since they
     are no longer used in dispatch mode.
  8. EFI_PEI_CORE_FV_LOCATION_PPI should be installed by the boot loader's SEC phase.
     EFI_PEI_CORE_FV_LOCATION_PPI.PeiCoreFvLocation should point to the first
     Firmware Volume (FV) in FSP-M so the PeiCore inside FSP will be invoked.
     If EFI_PEI_CORE_FV_LOCATION_PPI is not installed or PeiCore cannot be
     found at the address specified by EFI_PEI_CORE_FV_LOCATION_PPI.PeiCoreFvLocation,
     the PeiCore from the Boot Firmware Volume (BFV) will be invoked instead.
  9. FSP-S requires multi-threaded code to complete silicon initialization on this platform.
     FSP-S includes the <tt>UefiCpuPkg/CpuMpPei/CpuMpPei.inf</tt> PEIM to provide multiprocessing.
     The bootloader can choose to either use the MP_SERVICES provided by the FSP for all PEIMs or
     the bootloader may provide an alternative implementation. In either case, only one
     MP_SERVICES implementation can be active at once. If the bootloader wishes to not use the
     FSP provided MP_SERVICES, then the bootloader must install the MP_SERVICES_PPI before
     installing the FV_INFO_PPI for FSP-S. If MP_SERVICES_PPI already exists, then FSP-S
     will use it and not execute its own implementation.
  10. If you are using the Intel Reference BIOS, some EDK2 overrides may be
     required for Dispatch Mode, please refer to override folders in
     reference code or the override EDK2 repo for more details. For open source
     MinPlatform based firmware, no EDK2 overrides are required.
  11. FSPM_ARCH_CONFIG_PPI->NvsBufferPtr is now a universal policy option
     (FSP Dispatch Mode and EDK2 native.) To enable the fast MRC training flow,
     the boot loader or platform code must to install this PPI to restore the
     previous MRC training data (SA_MISC_PEI_PREMEM_CONFIG->S3DataPtr is obsolete).
  12. Policy initialization Flow Changes:
     - PEIMs from FSP-M/FSP-S should be dispatched early in PEI to produce
       the _DefaultPolicyInit_ PPIs.
       -> Bootloader consumes the _DefaultPolicyInit_ PPIs produced by the FSP binary to
          create the policy PPIs with default settings.
          -> Bootloader then locates and updates the policy PPIs as needed.
             -> Bootloader installs the _PolicyReadyPpi_ after policy updates are completed.
                This signals to the FSP that silicon initialization may proceed.
     - Bootloader shall consume two PPIs produced by FSP binary to create
       policy PPIs with default settings. These PPIs are:
       + @ref _PEI_PREMEM_SI_DEFAULT_POLICY_INIT_PPI "PEI_PREMEM_SI_DEFAULT_POLICY_INIT_PPI"
       + @ref _PEI_SI_DEFAULT_POLICY_INIT_PPI "PEI_SI_DEFAULT_POLICY_INIT_PPI"
     - The bootloader shall call the two functions below after the bootloader has completed any
       needed policy updates:
       + SiPreMemInstallPolicyReadyPpi()
       + SiInstallPolicyReadyPpi()
  13. Debug message handling in dispatch mode:
     - Before the ReportStatusCode service is ready, a debug built FSP will send debug messages using the FSP-T UPD
       configuration (passed as FSP-T API input parameter). FSP-T is recommended to be used
       regardless FSP API mode or Dispatch mode.
     - Once the ReportStatusCode service is ready, a debug built FSP will send debug messages using the ReportStatusCode service.
     - It is recommended that bootloader register a StatusCode listener immediately after the ReportStatusCode service is ready.
       It is important to register this listener as soon as possible so that all debug messages sent by the FSP are captured.
     - Please refer to section 9.4.7 in the Intel(R) Firmware Support Package External Architecture Specification v2.1
       for details about the ReportStatusCode debug message format.
  14. Enable or Disable FSP S3BootScript functionality:
     Since edk2-stable201911 release the PiDxeS3BootScriptLib supports enabling or disabling S3BootScript functionality by PCD,
     and FSP consumes this PCD as DynamicEx type to enable or disable internal S3BootScript functionality.
     Bootloader must configure below PCD before entering DXE phase or before executing FSP DXE drivers.
     - gEfiMdeModulePkgTokenSpaceGuid.PcdAcpiS3Enable = TRUE if S3BootScript should be enabled.
     - gEfiMdeModulePkgTokenSpaceGuid.PcdAcpiS3Enable = FALSE if S3BootScript should be disabled.

**/

// SECTION 5
/**
@if gen_html
  @page fsp_api_mode 5 FSP API Mode
@else
  @page fsp_api_mode FSP API Mode
@endif

@htmlonly
<!--
@endhtmlonly
@latexonly
\begin{comment}
@endlatexonly
@subpage fsp_api_descriptions
@subpage fsp_status_reset_required
@subpage fsp_upd_porting_guide
@htmlonly
-->
@endhtmlonly
@latexonly
\end{comment}
@endlatexonly

@if gen_html
## Overview
@else
# Overview
@endif
  This release of the FSP supports the all APIs required by the FSP External
  Architecture Specification version 2.1. These APIs are only used when running the FSP in API mode.
  In Dispatch mode, these APIs are not used (with the exception of TempRamInit.)
  The FSP information header contains the address offset for these APIs. Register usage is
  described in the FSP External Architecture Specification version 2.1. Any usage not described
  by the specification is described in the individual sections below.

  The sections below will highlight any changes that are specific to this FSP release.

@if gen_html
  @page fsp_api_descriptions 5.1 FSP APIs
@else
  @page fsp_api_descriptions FSP APIs
@endif

## 5.1.1 TempRamInit API
  Please refer Chapter 8.5 in the FSP External Architecture Specification version 2.1 for complete
  details including the prototype, parameters and return value details for this API.

  TempRamInit does basic early initialization primarily setting up temporary RAM using cache. It
  returns ECX pointing to beginning of temporary memory and EDX pointing to end of temporary memory + 1.
  The total temporary ram currently available is given by \ref PcdTemporaryRamSize starting from the base
  address of \ref PcdTemporaryRamBase. Out of the total temporary memory available, the last \ref
  PcdFspReservedBufferSize bytes of space are reserved by the FSP for TempRamInit if temporary RAM initialization
  is done by the FSP. Any remaining space from **TemporaryRamBase**(ECX) to **TemporaryRamBase+TemporaryRamSize-FspReservedBufferSize**
  (EDX) is available for both bootloader and FSP use.

  **TempRamInit** also sets up the code caching of the region passed in through CodeCacheBase and CodeCacheLength,
  which are input parameters to TempRamInitApi. if 0 is passed in for CodeCacheBase, the base used will
  be (4 GB - 1 - CodeCacheLength).
  @note: When programming MTRRs CodeCacheLength will be reduced, if the LLC size on the current processor is
  smaller than the requested size.

  It is a requirement for Firmware to have a Firmware Interface Table (FIT). The FIT contains pointers to
  each microcode update. The microcode update is loaded for all logical processors before executing the reset
  vector. If more than one microcode update for the CPU is present, the microcode update with the
  latest revision is loaded.

  **FSPT_UPD.MicrocodeRegionBase** and **FSPT_UPD.MicrocodeRegionLength** are input parameters to TempRamInit API.
  If these values are 0, FSP will not attempt to update microcode. If MicrocodeRegionBase != 0 and
  MicrocodeRegionLength != 0, then FSP will search that region for microcode updates. If a newer microcode update
  revision is found in the region, FSP will load it.

  **TempRamInit** will program MTRRs values to provide the following memory map:

  Memory range                           | Cache Attribute
  ---------------------------------------|-----------------
  0xFEF00000    - 0x00040000             | Write back
  CodeCacheBase - CodeCacheLength        | Write protect

## 5.1.2 FspMemoryInit API
  Please refer to Chapter 8.6 in the FSP External Architecture Specification version 2.1 for the
  prototype, parameters and return value details for this API.

  The variable **FspmUpdPtr** is a pointer to the **FSPM_UPD** structure which is described in
  the header file FspmUpd.h.

  The bootloader must pass valid a CAR region for FSP through **FSPM_UPD.FspmArchUpd.StackBase**
  and **FSPM_UPD.FspmArchUpd.StackSize** UPDs.

  The FSP for this platform will run FspMemoryInit top of the stack provided by the bootloader
  instead of establishing a separate stack as described by the FSP External Architecture
  Specification version 2.1. The memory region provided by the **FSPM_UPD.FspmArchUpd.StackBase**
  and **FSPM_UPD.FspmArchUpd.StackSize** UPDs is used to establish a HOB heap. The names **StackBase**
  and **StackSize** can be confusing since they are **NOT** used for stack. These names were retained
  for backwards compatibility with FSP v2.0.

  Below are the heap and stack requirement for FSP v2.1:

  HOB Heap requirement:
  HOB Heap | UPD                           | Setting
  ---------|-------------------------------|----------------------------------------------------
  Base     |FSPM_UPD.FspmArchUpd.StackBase |Any non-conflict CAR region (0xFEF17F00 as default)
  Size     |FSPM_UPD.FspmArchUpd.StackSize |at least 128KB

  Stack requirement:
  FSP's stack usage starts from the current stack pointer. The minimum stack size requirement for FSP-M
  is 256KB.

  The bootloader must ensure that sufficient stack space is available to fulfill the FSP-M minimum stack size
  requirement at the point in execution where FspMemoryInit() is called. The stack allocated
  by the bootloader must be large enough for both FSP-M as well as any other parent function calls that are
  still on the stack at the point when FspMemoryInit() is called.

  After FspMemoryInit() is completed, permanent memory is available. After this point, the memory pressure experienced
  early in boot is eliminated. Accordingly, right before FspMemoryInit() exits, any data that needs to be retained for
  later use by FspSiliconInit() will be copied to permanent memory. FspSiliconInit() will then execute on a second stack.

  The base address of HECI device (Bus 0, Device 22, Function 0) is required to be initialized prior
  to calling FspMemoryInit(). The default address is programmed to 0xFED1A000.

  FspMemoryInit() will calculate the memory map by taking into account the size of several memory regions:
  TSEG, IED, GTT, BDSM, ME stolen, Uncore PMRR, IOT, MOT, DPR, REMAP, TOLUD, TOUUD.
  These memory regions may not be initialized by FspMemoryInit(), but space will be reserved for them.

## 5.1.3 TempRamExit API
  Please refer to Chapter 8.7 in the FSP external Architecture Specification version 2.1 for the
  prototype, parameters and return value details for this API.

  If Boot Loader initializes the Temporary RAM (CAR) and skip calling **TempRamInit API**, it is expected that
  bootloader must skip calling this API and bootloader will tear down the temporary memory area setup in the
  cache and bring the cache to normal mode of operation.

  The FSP for this platform doesn't have any input parameters for this API.
  The value of *TempRamExitParamPtr* should be NULL.

  At the end of *TempRamExit* the original code and data cache are disabled.  FSP will reconfigure all MTRRs
  as described in the table below. These MTRR values optimize performance in most scenarios.
  If the boot loader wishes to configure the MTRRs differently, they can be reprogrammed immediately after
  this API call.

  Memory range                            | Cache Attribute
  ----------------------------------------|-----------------
  0xFF000000 -  0xFFFFFFFF (Flash region) | Write protect
  0x00000000 -  0x0009FFFF                | Write back
  0x000C0000 -  Top of Low Memory         | Write back
  xxxx       -  xxxx                      | x *Note1
  0x100000000 - Top of High Memory        | Write back *Note2

  Stack requirement: 4KB of free stack space should be provided to execute <b>TempRamExit</b>.

  *Note1: Certain silicon features require specific cache types for specific memory ranges. These ranges will be
          configured by FSP when such features are enabled.

  *Note2: In some cases MTRRs might not be enough to cover all desired regions, in this case memory regions need
          to be adjusted for better alignment (e.g., adjust MmioSize or MmioSizeAdjustment UPD)
          Covering flash region and above 4GB memory is another case which may consume more MTRRs, when there is
          not enough MTRR available FSP will only cover above 4GB memory partially.
          In this case the boot loader can optimize MTRRs to remove flash from the cached regions after
          all needed data is loaded from flash and before booting the OS.

## 5.1.4 FspSiliconInit API
  Please refer to Chapter 8.8 in the FSP external Architecture Specification version 2.1 for the
  prototype, parameters and return value details for this API.

  The variable *FspsUpdPtr* is a pointer to the **FSPS_UPD** structure which is described in the header file FspsUpd.h.

  It is expected that the boot loader will adjust MTRRs for SBSP if needed after **TempRamExit** but before
  entering **FspSiliconInit**. If the MTRRs are not programmed properly, boot performance
  can be impacted.

  The region of 0x5_8000 - 0x5_8FFF is used by FspSiliconInit for starting APs. If this data
  is important to bootloader, then bootloader needs to preserve it before calling FspSiliconInit.

  <b>FspSiliconInit</b> requires multi-threaded code to complete silicon initialization on this platform.
  FSP includes the <tt>UefiCpuPkg/CpuMpPei/CpuMpPei.inf</tt> PEIM to provide multiprocessing. Accordingly,
  the boot loader must expect FSP to perform an INIT-SIPI-SIPI during **FspSiliconInit** and
  <b>NotifyPhase</b> which will take control of all APs in the system and restart them. This will kill any
  background threads that were running on the APs. In some cases, this may not be desirable. As an
  alternative, the boot loader may provide an instance of the @ref MpServices.h "EFI_PEI_MP_SERVICES_PPI"
  through the <tt>FspsUpd->FspsConfig.CpuMpPpi</tt> UPD for the FSP to use instead of the built-in
  implementation. <b>This is entirely optional</b>, if callbacks from FSP to the boot loader are not desired,
  one may set <tt>FspsUpd->FspsConfig.CpuMpPpi == NULL</tt>.

  In summary, there are two methods that FSP can use for performing multiprocessing:

  1. Using the multiprocessing services built-in to the FSP.
  2. Using the boot loader's multiprocessing services.

  Using method #1, the boot loader will set <tt>FspsUpd->FspsConfig.CpuMpPpi == NULL</tt>. If this UPD is
  NULL, then FSP will use its built-in MP services implementation for multiprocessing. Accordingly, the
  boot loader must expect FSP to perform an INIT-SIPI-SIPI during **FspSiliconInit** and **NotifyPhase**
  which will take control of all APs in the system and restart them. This will kill any background threads
  that were running on the APs.

  Using method #2, the boot loader will set <tt>FspsUpd->FspsConfig.CpuMpPpi != NULL</tt>. If this UPD
  is not NULL, it must point to an instance of @ref MpServices.h "EFI_PEI_MP_SERVICES_PPI". When the FSP
  needs to perform multiprocessing, it will use the @ref MpServices.h "EFI_PEI_MP_SERVICES_PPI" instance
  provided by the boot loader to do so. Accordingly, the boot loader must expect the function pointers
  in @ref MpServices.h "EFI_PEI_MP_SERVICES_PPI" to be invoked in the middle of the execution of
  <b>FspSiliconInit</b> and **NotifyPhase**.

  @note If the boot loader is a UEFI boot loader using API mode instead of dispatch mode, and
  <tt>FspsUpd->FspsConfig.CpuMpPpi != NULL</tt>, then <tt>FspsUpd->FspsConfig.CpuMpHob</tt>
  must be != NULL. This UPD is a pointer to the CPU_MP_DATA HOB.

  It is a requirement for the bootloader to have a Firmware Interface Table (FIT), which contains pointers
  to each microcode. The microcode is loaded for all cores before reset vector. If more than one
  microcode update for the CPU is present, the latest revision is loaded.

  MicrocodeRegionBase and MicrocodeRegionLength are both input parameters to TempRamInit
  and UPD for SiliconInit API. UPD has priority and will be searched for a later revision
  than TempRamInit. If MicrocodeRegionBase and MicrocodeRegionLength values are 0, FSP will
  not attempt to update the microcode. If a microcode region is passed, FSP will search that
  region for microcode updates. If a newer microcode update revision is found in the region,
  FSP will load it.

  FspSiliconInit initializes PCH audio. This includes selecting the HD Audio verb table and
  initializing the audio CODEC.

  FspSiliconInit initializes the following PCH features: HECI, USB, HSIO, Integrated Sensor Hub,
  Camera, PCI Express, DMI, Vt-d.

  FspSiliconInit initializes the following CPU features: XD, VMX, AES, IED, HDC, x(2)APIC,
  Intel&reg; Processor Trace, Three Strike Counter, Machine Check, Cache Pre-fetchers,
  Core PMRR, and Power Management.

  FspSiliconInit initializes Internal Graphics. During this initialization, FSP publishes the
  EFI_PEI_GRAPHICS_INFO_HOB and the EFI_PEI_GRAPHICS_DEVICE_INFO_HOB. These HOBs will not be
  published during S3 resume as FSP will not initialize the internal graphics frame buffer
  during S3 resume.

  FspSiliconInit programs SA BARs: MchBar, DmiBar, EpBar, GdxcBar, EDRAM (if supported). Please refer to section
  2.8 (MemoryMap)  for the corresponding Bar values. GttMmadr (0xDF000000) and GmAdr(0xC0000000)
  are temporarily programmed and cleared after use in FSP.

  Stack requirement: 4KB of free stack space should be provided to execute *FspSiliconInit*.

## 5.1.5 NotifyPhase API
  Please refer Chapter 8.9 in the FSP External Architecture Specification version 2.1 for the
  prototype, parameters and return value details for this API.

  Stack requirement: 4KB of free stack space should be provided to execute *NotifyPhase*.

### 5.1.5.1 PostPciEnumeration Notification
  This phase *EnumInitPhaseAfterPciEnumeration* is to be called after PCI enumeration but before
  execution of third party code such as option ROMs. It includes powering down unused PCH SATA ports,
  locking registers in PMC, DMI, TCO, and SPI Flash (DLOCK + WRSDIS + FLOCKDN). It also includes
  enabling bus mastering for eSPI connected devices.

### 5.1.5.2 ReadyToBoot Notification
  This phase *EnumInitPhaseReadyToBoot* is to be called before giving control to the operating system.
  It includes some final initialization steps recommended by the BWG, including power management settings,
  and sending ME Message EOP (End of Post).

### 5.1.5.3 EndOfFirmware Notification
  This phase *EnumInitEndOfFirmware* is to be called before the firmware/preboot environment transfers
  management of all system resources to the OS or next level execution environment. It includes final locking
  of chipset registers.

@if gen_html
  @page fsp_status_reset_required 5.2 Reset Return Codes
@else
  @page fsp_status_reset_required Reset Return Codes
@endif
  As per FSP External Architecture Specification version 2.0/2.1, any reset required in the FSP flow will
  be reported by returning one of the FSP_STATUS_RESET_REQUIRED* return codes. It is the boot loader's responsibility
  to reset the system according to the reset type requested.

  Below table specifies the return status returned by FSP API and the requested reset type.

FSP_STATUS_RESET_REQUIRED Code | Reset Type requested
-------------------------------|----------------------
0x40000001 | Cold Reset
0x40000002 | Warm Reset
0x40000003 | Global Reset - Puts the system through a Global Reset through HECI or a Full Reset through PCH
0x40000004 | Reserved
0x40000005 | Reserved
0x40000006 | Reserved
0x40000007 | Reserved
0x40000008 | Reserved

@if gen_html
  @page fsp_upd_porting_guide 5.3 UPD Porting Guide
@else
  @page fsp_upd_porting_guide UPD Porting Guide
@endif

Recommended values for UPDs:
UPD                                 | Dependency          | Description                                  | Value
------------------------------------|---------------------|----------------------------------------------|-------
EnableSgx                           | TigerLake Platform  | Temporary workaround                         | 2
CstateLatencyControl1Irtl           | Server platform     | Server platform should has different setting | 0x6B
PchPcieHsioRxSetCtleEnable          | Board design        | Different board requires different value     | tune
PchPcieHsioRxSetCtle                | Board design        | Different board requires different value     | tune
PchSataHsioRxGen3EqBoostMagEnable   | Board design        | Different board requires different value     | tune
PchSataHsioRxGen3EqBoostMag         | Board design        | Different board requires different value     | tune
PchSataHsioTxGen1DownscaleAmpEnable | Board design        | Different board requires different value     | tune
PchSataHsioTxGen1DownscaleAmp       | Board design        | Different board requires different value     | tune
PchSataHsioTxGen2DownscaleAmpEnable | Board design        | Different board requires different value     | tune
PchSataHsioTxGen2DownscaleAmp       | Board design        | Different board requires different value     | tune
PchNumRsvdSmbusAddresses            | Board design        | Different board requires different value     | tune
RsvdSmbusAddressTablePtr            | Board design        | Different board requires different value     | tune
BiosSize                            | Board design        | Different board requires different value     | tune

**/

// SECTION 6
/**
@if gen_html
  @page fsp_porting_recommendation 6 Porting Recommendations
@else
  @page fsp_porting_recommendation Porting Recommendations
@endif
  Here are some notes and recommendations for adapting an existing boot loader to FSP.

@if gen_html
## 6.1 Locking PAM register
@else
# 6.1 Locking PAM register
@endif
  FSP 2.0 introduced the EndOfFirmware Notify phase callback which is the recommended place for locking PAM registers.
  Accordingly, by default FSP locks the PAM registers during the EndOfFirmware Notify phase. If the EndOfFirmware
  Notify phase is too early to lock PAM registers, then the PAM locking code inside the FSP can be disabled by
  setting the UPD -> FSP_S_TEST_CONFIG -> SkipPamLock or SA policy -> _SI_PREMEM_POLICY_STRUCT
  -> SA_MISC_PEI_CONFIG -> SkipPamLock. If the PAM locking code inside the FSP is skipped, then the boot loader
  must lock the PAM registers before booting the OS. by programming one PCI config space register as below.

  The PAM registers must be locked in all boot paths including S3 resume. To lock the PAM registers, program
  the following lock bit:

~~~
    MmioOr32 (B0: D0: F0: Register 0x80, BIT0)
~~~

@if gen_html
## 6.2 Locking SMRAM register
@else
# 6.2 Locking SMRAM register
@endif
  It is recommended that SMRAM be locked before any third party code (e.g. OpROM) execution. The point in
  execution where third party OpROMs begin executing can vary depending on the boot loader implementation.
  To provide flexibility, the FSP by default will not lock it. The boot loader should lock SMRAM by programming
  the following lock bit before any third party OpROM execution. Additionally, SMRAM should be locked before
  the **EnumInitPhaseReadyToBoot** notify phase is called. During S3 resume, the lock bit should be set
  right before the OS wake vector.
~~~
    PciOr8 (B0: D0: F0: Register 0x88, BIT4);
    Note: This register must be programmed using the legacy CF8/CFC PCI access mechanism. (MMIO access will not work)
~~~

@if gen_html
## 6.3 Locking SMI register
@else
# 6.3 Locking SMI register
@endif
  It is recommended that the global SMI bit is locked before any third party code (e.g. OpROM) execution.
  SMM initialization flows may vary depending on boot loader implementation details. Accordingly, FSP will not
  lock it by default. The boot loader is responsible for locking the following registers after SMM configuration
  is complete.
      Set AcpiBase + 0x30[0] to 1b to enable global SMI.
      Set PMC PCI offset A0h[4] = 1b to lock SMI.

@if gen_html
## 6.4 Verify below settings are correct for your platforms
@else
# 6.4 Verify below settings are correct for your platforms
@endif
  PMC PCI configuration space is not PCI specification compliant. The FSP will hide the PMC controller to
  avoid external software or OS from corrupting the BAR addresses. FSP will program the PMC controller IO
  and MMIO BAR's with below addresses. Please use these addresses in the boot loader code instead of reading
  BAR addresses from the PMC controller.

Register | Values
---------|---------------
ABASE | 0x1800
PWRMBASE | 0xFE000000
PCIEXBAR_BASE_ADDRESS | 0xE0000000
  @note:\n
    - Boot Loader can use a different value for PCIEXBAR_BASE_ADDRESS either by modifying the UPD (under FSP-T) or by overriding the PCIEXBAR (B0:D0:F0:R60h) before calling FspMemoryInit.
    - Boot Loader should avoid using conflicting address when reprogramming PCIEXBAR_BASE_ADDRESS than the recommended one.
**/

// SECTION 7
/**
@if gen_html
  @page fsp_output 7 FSP Output
@else
  @page fsp_output FSP Output
@endif

  The FSP builds a series of data structures called Hand-Off-Blocks (HOBs) as it progresses
  through initializing the silicon.
  Please refer to the Platform Initialization (PI) Specification - Volume 3: Shared Architectural
  Elements specification for PI Architectural HOBs. Please refer Chapter 10 in the FSP External Architecture
  Specification version 2.1 for details about FSP Architectural HOBs.

  The sections below describe the HOBs not covered in the above two specifications.

@if gen_html
## 7.1 SMRAM Resource Descriptor HOB
@else
# 7.1 SMRAM Resource Descriptor HOB
@endif
  The FSP will report the system SMRAM T-SEG range through a generic resource HOB if T-SEG is
  enabled. The owner field of the HOB identifies the owner as T-SEG.

~~~
#define FSP_HOB_RESOURCE_OWNER_TSEG_GUID  \
{ 0xd038747c, 0xd00c, 0x4980, { 0xb3, 0x19, 0x49, 0x01, 0x99, 0xa4, 0x7d, 0x55 } }
~~~

@if gen_html
## 7.2 SMBIOS INFO HOB
@else
# 7.2 SMBIOS INFO HOB
@endif
  The FSP will report the SMBIOS through a HOB with below GUID. This information can be consumed
  by the bootloader to produce the SMBIOS tables. These structures are included as part of MemInfoHob.h,
  SmbiosCacheInfoHob.h, SmbiosProcessorInfoHob.h, & FirmwareVersionInfoHob.h

~~~
#define SI_MEMORY_INFO_DATA_HOB_GUID \
{ 0x9b2071d4, 0xb054, 0x4e0c, { 0x8d, 0x09, 0x11, 0xcf, 0x8b, 0x9f, 0x03, 0x23 } };

typedef struct {
  MrcDimmStatus Status;                     ///< See MrcDimmStatus for the definition of this field.
  UINT8         DimmId;
  UINT32        DimmCapacity;               ///< DIMM size in MBytes.
  UINT16        MfgId;
  UINT8         ModulePartNum[20];          ///< Module part number for DDR3 is 18 bytes however for DRR4 20 bytes as per JEDEC Spec, so reserving 20 bytes
  UINT8         RankInDimm;                 ///< The number of ranks in this DIMM.
  UINT8         SpdDramDeviceType;          ///< Save SPD DramDeviceType information needed for SMBIOS structure creation.
  UINT8         SpdModuleType;              ///< Save SPD ModuleType information needed for SMBIOS structure creation.
  UINT8         SpdModuleMemoryBusWidth;    ///< Save SPD ModuleMemoryBusWidth information needed for SMBIOS structure creation.
  UINT8         SpdSave[MAX_SPD_SAVE_DATA]; ///< Save SPD Manufacturing information needed for SMBIOS structure creation.
} DIMM_INFO;

typedef struct {
  UINT8         Status;                     ///< Indicates whether this channel should be used.
  UINT8         ChannelId;
  UINT8         DimmCount;                  ///< Number of valid DIMMs that exist in the channel.
  MRC_CH_TIMING Timing[MAX_PROFILE];        ///< The channel timing values.
  DIMM_INFO     Dimm[MAX_DIMM];             ///< Save the DIMM output characteristics.
} CHANNEL_INFO;

typedef struct {
  UINT8            Status;                  ///< Indicates whether this controller should be used.
  UINT16           DeviceId;                ///< The PCI device id of this memory controller.
  UINT8            RevisionId;              ///< The PCI revision id of this memory controller.
  UINT8            ChannelCount;            ///< Number of valid channels that exist on the controller.
  CHANNEL_INFO     Channel[MAX_CH];         ///< The following are channel level definitions.
} CONTROLLER_INFO;

typedef struct {
  EFI_HOB_GUID_TYPE EfiHobGuidType;
  UINT8             Revision;
  UINT16            DataWidth;
  /// As defined in SMBIOS 3.0 spec
  /// Section 7.18.2 and Table 75
  UINT8             DdrType;                ///< DDR type: DDR3, DDR4, or LPDDR3
  UINT32            Frequency;              ///< The system's common memory controller frequency in MT/s.
  /// As defined in SMBIOS 3.0 spec
  /// Section 7.17.3 and Table 72
  UINT8             ErrorCorrectionType;

  SiMrcVersion      Version;
  UINT32            FreqMax;
  BOOLEAN           EccSupport;
  UINT8             MemoryProfile;
  UINT32            TotalPhysicalMemorySize;
  BOOLEAN           XmpProfileEnable;
  UINT8             Ratio;
  UINT8             RefClk;
  UINT32            VddVoltage[MAX_PROFILE];
  CONTROLLER_INFO   Controller[MAX_NODE];
} MEMORY_INFO_DATA_HOB;

#define  SI_MEMORY_PLATFORM_DATA_HOB \
  { 0x6210d62f, 0x418d, 0x4999, { 0xa2, 0x45, 0x22, 0x10, 0x0a, 0x5d, 0xea, 0x44 } }

typedef struct {
  UINT8             Revision;
  UINT8             Reserved[3];
  UINT32            BootMode;
  UINT32            TsegSize;
  UINT32            TsegBase;
  UINT32            PrmrrSize;
  UINT32            PrmrrBase;
  UINT32            GttBase;
  UINT32            MmioSize;
  UINT32            PciEBaseAddress;
} MEMORY_PLATFORM_DATA;

typedef struct {
  EFI_HOB_GUID_TYPE    EfiHobGuidType;
  MEMORY_PLATFORM_DATA Data;
  UINT8                *Buffer;
} MEMORY_PLATFORM_DATA_HOB;

#define SMBIOS_CACHE_INFO_HOB_GUID \
 { 0xd805b74e, 0x1460, 0x4755, {0xbb, 0x36, 0x1e, 0x8c, 0x8a, 0xd6, 0x78, 0xd7} }

///
/// SMBIOS Cache Info HOB Structure
///
typedef struct {
  UINT16     ProcessorSocketNumber;
  UINT16     NumberOfCacheLevels;         ///< Based on Number of Cache Types L1/L2/L3
  UINT8      SocketDesignationStrIndex;   ///< String Index in the string Buffer. Example "L1-CACHE"
  UINT16     CacheConfiguration;          ///< Format defined in SMBIOS Spec v3.0 Section7.8 Table36
  UINT16     MaxCacheSize;                ///< Format defined in SMBIOS Spec v3.0 Section7.8.1
  UINT16     InstalledSize;               ///< Format defined in SMBIOS Spec v3.0 Section7.8.1
  UINT16     SupportedSramType;           ///< Format defined in SMBIOS Spec v3.0 Section7.8.2
  UINT16     CurrentSramType;             ///< Format defined in SMBIOS Spec v3.0 Section7.8.2
  UINT8      CacheSpeed;                  ///< Cache Speed in nanoseconds. 0 if speed is unknown.
  UINT8      ErrorCorrectionType;         ///< ENUM Format defined in SMBIOS Spec v3.0 Section 7.8.3
  UINT8      SystemCacheType;             ///< ENUM Format defined in SMBIOS Spec v3.0 Section 7.8.4
  UINT8      Associativity;               ///< ENUM Format defined in SMBIOS Spec v3.0 Section 7.8.5
  ///String Buffer - each string terminated by NULL "0x00"
  ///String buffer terminated by double NULL "0x0000"
} SMBIOS_CACHE_INFO;

#define SMBIOS_PROCESSOR_INFO_HOB_GUID \
  { 0xe6d73d92, 0xff56, 0x4146, {0xaf, 0xac, 0x1c, 0x18, 0x81, 0x7d, 0x68, 0x71} }

///
/// SMBIOS Processor Info HOB Structure
///
typedef struct {
  UINT16     TotalNumberOfSockets;
  UINT16     CurrentSocketNumber;
  UINT8      ProcessorType;                 ///< ENUM defined in SMBIOS Spec v3.0 Section 7.5.1
  ///This info is used for both ProcessorFamily and ProcessorFamily2 fields
  ///See ENUM defined in SMBIOS Spec v3.0 Section 7.5.2
  UINT16     ProcessorFamily;
  UINT8      ProcessorManufacturerStrIndex; ///< Index of the String in the String Buffer
  UINT64     ProcessorId;                   ///< ENUM defined in SMBIOS Spec v3.0 Section 7.5.3
  UINT8      ProcessorVersionStrIndex;      ///< Index of the String in the String Buffer
  UINT8      Voltage;                       ///< Format defined in SMBIOS Spec v3.0 Section 7.5.4
  UINT16     ExternalClockInMHz;            ///< External Clock Frequency. Set to 0 if unknown.
  UINT16     CurrentSpeedInMHz;             ///< Snapshot of current processor speed during boot
  UINT8      Status;                        ///< Format defined in the SMBIOS Spec v3.0 Table 21
  UINT8      ProcessorUpgrade;              ///< ENUM defined in SMBIOS Spec v3.0 Section 7.5.5
  ///This info is used for both CoreCount & CoreCount2 fields
  /// See detailed description in SMBIOS Spec v3.0 Section 7.5.6
  UINT16     CoreCount;
  ///This info is used for both CoreEnabled & CoreEnabled2 fields
  ///See detailed description in SMBIOS Spec v3.0 Section 7.5.7
  UINT16     EnabledCoreCount;
  ///This info is used for both ThreadCount & ThreadCount2 fields
  /// See detailed description in SMBIOS Spec v3.0 Section 7.5.8
  UINT16     ThreadCount;
  UINT16     ProcessorCharacteristics;      ///< Format defined in SMBIOS Spec v3.0 Section 7.5.9
  /// String Buffer - each string terminated by NULL "0x00"
  /// String buffer terminated by double NULL "0x0000"
} SMBIOS_PROCESSOR_INFO;

#define SMBIOS_FIRMWARE_VERSION_INFO_HOB_GUID \
  { 0x947c974a, 0xc5aa, 0x48a2, {0xa4, 0x77, 0x1a, 0x4c, 0x9f, 0x52, 0xe7, 0x82} }

///
/// Firmware Version Structure
///
typedef struct {
  UINT8                          MajorVersion;
  UINT8                          MinorVersion;
  UINT8                          Revision;
  UINT16                         BuildNumber;
} FIRMWARE_VERSION;

///
/// Firmware Version Information Structure
///
typedef struct {
  UINT8                          ComponentNameIndex;        ///< Offset 0   Index of Component Name
  UINT8                          VersionStringIndex;        ///< Offset 1   Index of Version String
  FIRMWARE_VERSION               Version;                   ///< Offset 2-6 Firmware version
} FIRMWARE_VERSION_INFO;

///
/// The Smbios structure header.
///
typedef struct {
  UINT8                          Type;
  UINT8                          Length;
  UINT16                         Handle;
} SMBIOS_STRUCTURE;
///
/// Firmware Version Information HOB Structure
///
typedef struct {
  EFI_HOB_GUID_TYPE              Header;                    ///< Offset 0-23  The header of FVI HOB
  SMBIOS_STRUCTURE               SmbiosData;                ///< Offset 24-27  The SMBIOS header of FVI HOB
  UINT8                          Count;                     ///< Offset 28    Number of FVI elements included.
///
/// FIRMWARE_VERSION_INFO structures followed by the null terminated string buffer
///
} FIRMWARE_VERSION_INFO_HOB;
~~~

@if gen_html
## 7.3 CHIPSETINIT INFO HOB
@else
# 7.3 CHIPSETINIT INFO HOB
@endif
  The FSP will report the ChipsetInit CRC through a HOB with below GUID. This information can be consumed
  by the bootloader to check if ChipsetInit CRC is matched between BIOS and ME. These structures are included
  as part of FspsUpd.h

~~~
#define CHIPSETINIT_INFO_HOB_GUID \
{ 0xc1392859, 0x1f65, 0x446e, { 0xb3, 0xf5, 0x84, 0x35, 0xfc, 0xc7, 0xd1, 0xc4 }}

///
/// The ChipsetInit Info structure provides the information of ME ChipsetInit CRC and BIOS ChipsetInit CRC.
///
typedef struct {
  UINT8             Revision;
  UINT8             Rsvd[3];
  UINT16            MeChipInitCrc;
  UINT16            BiosChipInitCrc;
} CHIPSET_INIT_INFO;
~~~

@if gen_html
## 7.4 HOB USAGE INFO HOB
@else
# 7.4 HOB USAGE INFO HOB
@endif
  The FSP will report the Hob memory usage through a HOB with below GUID.
  This information can be consumed by the bootloader to check how much
  temporary RAM is left.

~~~
#define HOB_USAGE_DATA_HOB_GUID \
{0xc764a821, 0xec41, 0x450d, { 0x9c, 0x99, 0x27, 0x20, 0xfc, 0x7c, 0xe1, 0xf6 }}

typedef struct {
  EFI_PHYSICAL_ADDRESS EfiMemoryTop;
  EFI_PHYSICAL_ADDRESS EfiMemoryBottom;
  EFI_PHYSICAL_ADDRESS EfiFreeMemoryTop;
  EFI_PHYSICAL_ADDRESS EfiFreeMemoryBottom;
  UINTN                FreeMemory;
} HOB_USAGE_DATA_HOB;
~~~

@if gen_html
## 7.5 FSP_ERROR_INFO_HOB
@else
# 7.5 FSP_ERROR_INFO_HOB
@endif
  In the case of an error occurring during the execution of the FSP, the FSP may produce
  this HOB which describes the error in more detail. FSP_ERROR_INFO_HOB is only used in
  FSP API Mode. In FSP Dispatch Mode, FSP may call ReportStatusCode() and provide a
  FSP_ERROR_INFO structure using the PI status code services.

~~~
#define FSP_ERROR_INFO_HOB_GUID \
{0x611e6a88, 0xadb7, 0x4301, { 0x93, 0xff, 0xe4, 0x73, 0x04, 0xb4, 0x3d, 0xa6 }}

typedef struct {
  EFI_HOB_GUID_TYPE     GuidHob;
  EFI_STATUS_CODE_TYPE  Type;
  EFI_STATUS_CODE_VALUE Value;
  UINT32                Instance;
  EFI_GUID              CallerId;
  EFI_GUID              ErrorType;
  UINT32                Status;
} FSP_ERROR_INFO_HOB;
~~~

  The FSP for this platform implements the following CallerId GUIDs:

  CallerId                                                                               | Description
  ---------------------------------------------------------------------------------------|------------------
  <tt>{0x1f4dc7e9, 0x26ca, 0x4336, {0x8c, 0xe3, 0x39, 0x31, 0x03, 0xb5, 0xf3, 0xd7}}</tt>| ME
  <tt>{0x98230916, 0xe632, 0x49ff, {0x81, 0x81, 0x55, 0xce, 0xe5, 0x10, 0x36, 0x89}}</tt>| System Agent
  <tt>{0x5a47c211, 0x642f, 0x4f92, {0x9c, 0xb3, 0x7f, 0xeb, 0x93, 0xda, 0xdd, 0xba}}</tt>| MRC

  The following ErrorType GUIDs are implemented:

  ErrorType                                                                              | Description
  ---------------------------------------------------------------------------------------|------------------
  <tt>{0x948585c4, 0x76a4, 0x45bb, {0xbe, 0x6c, 0x39, 0x61, 0xc3, 0xab, 0xde, 0x15}}</tt>|ME EOP failure
  <tt>{0x8106a5cc, 0x30ba, 0x41cf, {0xa1, 0x78, 0x63, 0x38, 0x91, 0x11, 0xae, 0xb2}}</tt>|SA PEI GOP Init failure
  <tt>{0x348cc7fe, 0x1e9a, 0x4c7a, {0x86, 0x28, 0xae, 0x48, 0x5b, 0x42, 0x10, 0xf0}}</tt>|SA PEI GOP GetMode failure
  <tt>{0x5de1c071, 0x2c9c, 0x4a53, {0x80, 0x21, 0x4e, 0x80, 0xd2, 0x5d, 0x44, 0xa8}}</tt>|MRC training failure

**/

// SECTION 8
/**
@if gen_html
  @page fsp_postcode 8 POST Codes
@else
  @page fsp_postcode POST Codes
@endif
  The FSP outputs 16-bit POST codes to indicate which API and which module is currently executing.

  Bit Range          | Description
  -------------------|------------
  Bit15 - Bit12 (X)  | used to indicate the phase/api under which the code is executing
  Bit11 - Bit8 (Y)   | used to indicate the module
  Bit7 (ZZ bit 7)    | reserved for error
  Bit6 - Bit0 (ZZ)   | individual codes

@if gen_html
## 8.1 POST Code Info
@else
# 8.1 POST Code Info
@endif
  The diagram below illustrates how sub-fields are encoded in the POST codes produced by the FSP.

@dot
digraph structs {
    node [shape=record, width=3, height=1];
    struct1 [style=bold,label="<f0> X|<f1> Y|<f2> ZZ"];
    struct2 [label="<f0> FSP API - 4 BITS (one Digit)\n
F - Tempraminit /SEC \l
E - Reserved \l
D - MemoryInit /Pre-Memory \l
C - Reserved \l
B - Tempramexit \l
A - Reserved \l
9 - SiliconInit /Post Memory \l
8 - Reserved \l
7 - Reserved \l
6 - Notify / Post PCIE Enumeration \l
5 - Reserved \l
4 - Notify / Ready To Boot \l
3 - Reserved \l
2 - Notify / End Of Firmware \l
1-0 - Reserved\l"];
    struct3 [label="<f0> Module -  4 BITS (one digit)\n
7 - Gfx PEIM \l
8 - FSP Common Code \l
9 - Silicon Common Code \l
A - System Agent \l
B - PCH \l
C - CPU \l
D - MRC \l
E - ME-BIOS \l
F - Reserved \l
"];
    struct4 [label="<f0>Individual Codes\n
0x00 - API Entry \l
0x7F - API Exit \l

(Bit7 reserved for error)\l
 "];
    struct1:f0 -> struct2:f0;
    struct1:f1 -> struct3:f0;
    struct1:f2 -> struct4:f0;
}
@enddot


@if gen_html
### 8.1.1 TempRamInit API Status Codes (0xFxxx)
@else
## 8.1.1 TempRamInit API Status Codes (0xFxxx)
@endif

PostCode | Module | Description
---------|--------|-----------------
0x0000   | FSP    | TempRamInit API Entry (The change in upper byte is due to not enabling of the Port81 early in the boot)
0x007F   | FSP    | TempRamInit API Exit

@if gen_html
### 8.1.2 FspMemoryInit API Status Codes (0xDxxx)
@else
## 8.1.2 FspMemoryInit API Status Codes (0xDxxx)
@endif

PostCode | Module | Description
---------|--------|-----------------
0xD800   | FSP    | FspMemoryInit API Entry
0xD87F   | FSP    | FSpMemoryInit API Exit
0xDA00   | SA     | Pre-Mem SaInit Entry
0xDA02   | SA     | OverrideDev0Did Start
0xDA04   | SA     | OverrideDev2Did Start
0xDA06   | SA     | Programming SA Bars
0xDA08   | SA     | Install SA HOBs
0xDA0A   | SA     | Reporting SA PCIe code version
0xDA0C   | SA     | SaSvInit Start
0xDA10   | SA     | Initializing DMI
0xDA15   | SA     | Initialize TCSS PreMem
0xDA1F   | SA     | Initializing DMI/OPI Max PayLoad Size
0xDA20   | SA     | Initializing SwitchableGraphics
0xDA30   | SA     | Initializing SA PCIe
0xDA3F   | SA     | Programming PEG credit values Start
0xDA40   | SA     | Initializing DMI Tc/Vc mapping
0xDA42   | SA     | CheckOffboardPcieVga
0xDA44   | SA     | CheckAndInitializePegVga
0xDA50   | SA     | Initializing Graphics
0xDA52   | SA     | Initializing System Agent Overclocking
0xDA7F   | SA     | Pre-Mem SaInit Exit
0xDB00   | PCH    | Pre-Mem PchInit Entry
0xDB02   | PCH    | Pre-Mem Disable PCH fused controllers
0xDB15   | PCH    | Pre-Mem SMBUS configuration
0xDB48   | PCH    | Pre-Mem PchOnPolicyInstalled Entry
0xDB49   | PCH    | Pre-Mem Program HSIO
0xDB4A   | PCH    | Pre-Mem DCI configuration
0xDB4C   | PCH    | Pre-Mem Host DCI enabled
0xDB4D   | PCH    | Pre-Mem Trace Hub - Early configuration
0xDB4E   | PCH    | Pre-Mem Trace Hub - Device disabled
0xDB4F   | PCH    | Pre-Mem TraceHub - Programming MSR
0xDB50   | PCH    | Pre-Mem Trace Hub - Power gating configuration
0xDB51   | PCH    | Pre-Mem Trace Hub - Power gating Trace Hub device and locking HSWPGCR1 register
0xDB52   | PCH    | Pre-Mem Initialize HPET timer
0xDB55   | PCH    | Pre-Mem PchOnPolicyInstalled Exit
0xDB7F   | PCH    | Pre-Mem PchInit Exit
0xDC00   | CPU    | CPU Pre-Mem Entry
0xDC0F   | CPU    | CpuAddPreMemConfigBlocks Done
0xDC20   | CPU    | CpuOnPolicyInstalled Start
0xDC2F   | CPU    | XmmInit Start
0xDC3F   | CPU    | TxtInit Start
0xDC4F   | CPU    | Init CPU Straps
0xDC5F   | CPU    | Init Overclocking
0xDC6F   | CPU    | CPU Pre-Mem Exit
0x**55   | SA     | MRC_MEM_INIT_DONE
0x**D5   | SA     | MRC_MEM_INIT_DONE_WITH_ERRORS
0xDD00   | SA     | MRC_INITIALIZATION_START
0xDD10   | SA     | MRC_CMD_PLOT_2D
0xDD1B   | SA     | MRC_FAST_BOOT_PERMITTED
0xDD1C   | SA     | MRC_RESTORE_NON_TRAINING
0xDD1D   | SA     | MRC_PRINT_INPUT_PARAMS
0xDD1E   | SA     | MRC_SET_OVERRIDES_PSPD
0xDD20   | SA     | MRC_SPD_PROCESSING
0xDD21   | SA     | MRC_SET_OVERRIDES
0xDD22   | SA     | MRC_MC_CAPABILITY
0xDD23   | SA     | MRC_MC_CONFIG
0xDD24   | SA     | MRC_MC_MEMORY_MAP
0xDD25   | SA     | MRC_JEDEC_INIT_LPDDR3
0xDD26   | SA     | MRC_RESET_SEQUENCE
0xDD27   | SA     | MRC_PRE_TRAINING
0xDD28   | SA     | MRC_EARLY_COMMAND
0xDD29   | SA     | MRC_SENSE_AMP_OFFSET
0xDD2A   | SA     | MRC_READ_MPR
0xDD2B   | SA     | MRC_RECEIVE_ENABLE
0xDD2C   | SA     | MRC_JEDEC_WRITE_LEVELING
0xDD2D   | SA     | MRC_LPDDR_LATENCY_SET_B
0xDD2E   | SA     | MRC_WRITE_TIMING_1D
0xDD2F   | SA     | MRC_READ_TIMING_1D
0xDD30   | SA     | MRC_DIMM_ODT
0xDD31   | SA     | MRC_EARLY_WRITE_TIMING_2D
0xDD32   | SA     | MRC_WRITE_DS
0xDD33   | SA     | MRC_WRITE_EQ
0xDD34   | SA     | MRC_EARLY_READ_TIMING_2D
0xDD35   | SA     | MRC_READ_ODT
0xDD36   | SA     | MRC_READ_EQ
0xDD37   | SA     | MRC_READ_AMP_POWER
0xDD38   | SA     | MRC_WRITE_TIMING_2D
0xDD39   | SA     | MRC_READ_TIMING_2D
0xDD3A   | SA     | MRC_CMD_VREF
0xDD3B   | SA     | MRC_WRITE_VREF_2D
0xDD3C   | SA     | MRC_READ_VREF_2D
0xDD3D   | SA     | MRC_POST_TRAINING
0xDD3E   | SA     | MRC_LATE_COMMAND
0xDD3F   | SA     | MRC_ROUND_TRIP_LAT
0xDD40   | SA     | MRC_TURN_AROUND
0xDD41   | SA     | MRC_CMP_OPT
0xDD42   | SA     | MRC_SAVE_MC_VALUES
0xDD43   | SA     | MRC_RESTORE_TRAINING
0xDD44   | SA     | MRC_RMT_TOOL
0xDD45   | SA     | MRC_WRITE_SR
0xDD46   | SA     | MRC_DIMM_RON
0xDD47   | SA     | MRC_RCVEN_TIMING_1D
0xDD48   | SA     | MRC_MR_FILL
0xDD49   | SA     | MRC_PWR_MTR
0xDD4A   | SA     | MRC_DDR4_MAPPING
0xDD4B   | SA     | MRC_WRITE_VOLTAGE_1D
0xDD4C   | SA     | MRC_EARLY_RDMPR_TIMING_2D
0xDD4D   | SA     | MRC_FORCE_OLTM
0xDD50   | SA     | MRC_MC_ACTIVATE
0xDD51   | SA     | MRC_RH_PREVENTION
0xDD52   | SA     | MRC_GET_MRC_DATA
0xDD53   | SA     | Reserved
0xDD58   | SA     | MRC_RETRAIN_CHECK
0xDD5A   | SA     | MRC_SA_GV_SWITCH
0xDD5B   | SA     | MRC_ALIAS_CHECK
0xDD5C   | SA     | MRC_ECC_CLEAN_START
0xDD5D   | SA     | MRC_DONE
0xDD5F   | SA     | MRC_CPGC_MEMORY_TEST
0xDD60   | SA     | MRC_TXT_ALIAS_CHECK
0xDD61   | SA     | MRC_ENG_PERF_GAIN
0xDD68   | SA     | MRC_MEMORY_TEST
0xDD69   | SA     | MRC_FILL_RMT_STRUCTURE
0xDD70   | SA     | MRC_SELF_REFRESH_EXIT
0xDD71   | SA     | MRC_NORMAL_MODE
0xDD7D   | SA     | MRC_SSA_PRE_STOP_POINT
0xDD7F   | SA     | MRC_SSA_STOP_POINT, MRC_INITIALIZATION_END
0xDD90   | SA     | MRC_CMD_PLOT_2D_ERROR
0xDD9B   | SA     | MRC_FAST_BOOT_PERMITTED_ERROR
0xDD9C   | SA     | MRC_RESTORE_NON_TRAINING_ERROR
0xDD9D   | SA     | MRC_PRINT_INPUT_PARAMS_ERROR
0xDD9E   | SA     | MRC_SET_OVERRIDES_PSPD_ERROR
0xDDA0   | SA     | MRC_SPD_PROCESSING_ERROR
0xDDA1   | SA     | MRC_SET_OVERRIDES_ERROR
0xDDA2   | SA     | MRC_MC_CAPABILITY_ERROR
0xDDA3   | SA     | MRC_MC_CONFIG_ERROR
0xDDA4   | SA     | MRC_MC_MEMORY_MAP_ERROR
0xDDA5   | SA     | MRC_JEDEC_INIT_LPDDR3_ERROR
0xDDA6   | SA     | MRC_RESET_ERROR
0xDDA7   | SA     | MRC_PRE_TRAINING_ERROR
0xDDA8   | SA     | MRC_EARLY_COMMAND_ERROR
0xDDA9   | SA     | MRC_SENSE_AMP_OFFSET_ERROR
0xDDAA   | SA     | MRC_READ_MPR_ERROR
0xDDAB   | SA     | MRC_RECEIVE_ENABLE_ERROR
0xDDAC   | SA     | MRC_JEDEC_WRITE_LEVELING_ERROR
0xDDAD   | SA     | MRC_LPDDR_LATENCY_SET_B_ERROR
0xDDAE   | SA     | MRC_WRITE_TIMING_1D_ERROR
0xDDAF   | SA     | MRC_READ_TIMING_1D_ERROR
0xDDB0   | SA     | MRC_DIMM_ODT_ERROR
0xDDB1   | SA     | MRC_EARLY_WRITE_TIMING_ERROR
0xDDB2   | SA     | MRC_WRITE_DS_ERROR
0xDDB3   | SA     | MRC_WRITE_EQ_ERROR
0xDDB4   | SA     | MRC_EARLY_READ_TIMING_ERROR
0xDDB5   | SA     | MRC_READ_ODT_ERROR
0xDDB6   | SA     | MRC_READ_EQ_ERROR
0xDDB7   | SA     | MRC_READ_AMP_POWER_ERROR
0xDDB8   | SA     | MRC_WRITE_TIMING_2D_ERROR
0xDDB9   | SA     | MRC_READ_TIMING_2D_ERROR
0xDDBA   | SA     | MRC_CMD_VREF_ERROR
0xDDBB   | SA     | MRC_WRITE_VREF_2D_ERROR
0xDDBC   | SA     | MRC_READ_VREF_2D_ERROR
0xDDBD   | SA     | MRC_POST_TRAINING_ERROR
0xDDBE   | SA     | MRC_LATE_COMMAND_ERROR
0xDDBF   | SA     | MRC_ROUND_TRIP_LAT_ERROR
0xDDC0   | SA     | MRC_TURN_AROUND_ERROR
0xDDC1   | SA     | MRC_CMP_OPT_ERROR
0xDDC2   | SA     | MRC_SAVE_MC_VALUES_ERROR
0xDDC3   | SA     | MRC_RESTORE_TRAINING_ERROR
0xDDC4   | SA     | MRC_RMT_TOOL_ERROR
0xDDC5   | SA     | MRC_WRITE_SR_ERROR
0xDDC6   | SA     | MRC_DIMM_RON_ERROR
0xDDC7   | SA     | MRC_RCVEN_TIMING_1D_ERROR
0xDDC8   | SA     | MRC_MR_FILL_ERROR
0xDDC9   | SA     | MRC_PWR_MTR_ERROR
0xDDCA   | SA     | MRC_DDR4_MAPPING_ERROR
0xDDCB   | SA     | MRC_WRITE_VOLTAGE_1D_ERROR
0xDDCC   | SA     | MRC_EARLY_RDMPR_TIMING_2D_ERROR
0xDDCD   | SA     | MRC_FORCE_OLTM_ERROR
0xDDD0   | SA     | MRC_MC_ACTIVATE_ERROR
0xDDD1   | SA     | MRC_RH_PREVENTION_ERROR
0xDDD2   | SA     | MRC_GET_MRC_DATA_ERROR
0xDDD3   | SA     | Reserved
0xDDD8   | SA     | MRC_RETRAIN_CHECK_ERROR
0xDDDA   | SA     | MRC_SA_GV_SWITCH_ERROR
0xDDDB   | SA     | MRC_ALIAS_CHECK_ERROR
0xDDDC   | SA     | MRC_ECC_CLEAN_ERROR
0xDDDD   | SA     | MRC_DONE_WITH_ERROR
0xDDDF   | SA     | MRC_CPGC_MEMORY_TEST_ERROR
0xDDE0   | SA     | MRC_TXT_ALIAS_CHECK_ERROR
0xDDE1   | SA     | MRC_ENG_PERF_GAIN_ERROR
0xDDE8   | SA     | MRC_MEMORY_TEST_ERROR
0xDDE9   | SA     | MRC_FILL_RMT_STRUCTURE_ERROR
0xDDF0   | SA     | MRC_SELF_REFRESH_EXIT_ERROR
0xDDF1   | SA     | MRC_MRC_NORMAL_MODE_ERROR
0xDDFD   | SA     | MRC_SSA_PRE_STOP_POINT_ERROR
0xDDFE   | SA     | MRC_NO_MEMORY_DETECTED

@if gen_html
### 8.1.3 TempRamExit API Status Codes (0xBxxx)
@else
## 8.1.3 TempRamExit API Status Codes (0xBxxx)
@endif

PostCode | Module | Description
---------|--------|-----------------
0xB800   | FSP    | TempRamExit API Entry
0xB87F   | FSP    | TempRamExit API Exit

@if gen_html
### 8.1.4 FspSiliconInit API Status Codes (0x9xxx)
@else
## 8.1.4 FspSiliconInit API Status Codes (0x9xxx)
@endif

PostCode | Module | Description
---------|--------|-----------------
0x9800   | FSP    | FspSiliconInit API Entry
0x987F   | FSP    | FspSiliconInit API Exit
0x9A00   | SA    | PostMem SaInit Entry
0x9A01   | SA    | DeviceConfigure Start
0x9A02   | SA    | UpdateSaHobPostMem Start
0x9A03   | SA    | Initializing Pei Display
0x9A04   | SA    | PeiGraphicsNotifyCallback Entry
0x9A05   | SA    | CallPpiAndFillFrameBuffer
0x9A06   | SA    | GraphicsPpiInit
0x9A07   | SA    | GraphicsPpiGetMode
0x9A08   | SA    | FillFrameBufferAndShowLogo
0x9A0F   | SA    | PeiGraphicsNotifyCallback Exit
0x9A14   | SA    | Initializing SA IPU device
0x9A16   | SA    | Initializing SA GNA device
0x9A1A   | SA    | SaProgramLlcWays Start
0x9A20   | SA    | Initializing PciExpressInitPostMem
0x9A22   | SA    | Initializing ConfigureNorthIntelTraceHub
0x9A30   | SA    | Initializing Vtd
0x9A31   | SA    | Initializing TCSS
0x9A32   | SA    | Initializing Pavp
0x9A34   | SA    | PeiInstallSmmAccessPpi Start
0x9A36   | SA    | EdramWa Start
0x9A4F   | SA    | Post-Mem SaInit Exit
0x9A50   | SA    | SaSecurityLock Start
0x9A5F   | SA    | SaSecurityLock End
0x9A60   | SA    | SaSResetComplete Entry
0x9A61   | SA    | Set BIOS_RESET_CPL to indicate all configurations complete
0x9A62   | SA    | SaSvInit2 Start
0x9A63   | SA    | GraphicsPmInit Start
0x9A64   | SA    | SaPciPrint Start
0x9A6F   | SA    | SaSResetComplete Exit
0x9A70   | SA    | SaS3ResumeAtEndOfPei Callback Entry
0x9A7F   | SA    | SaS3ResumeAtEndOfPei Callback Exit
0x9B00   | PCH   | Post-Mem PchInit Entry
0x9B03   | PCH   | Post-Mem Tune the USB 2.0 high-speed signals quality
0x9B04   | PCH   | Post-Mem Tune the USB 3.0 signals quality
0x9B05   | PCH   | Post-Mem Configure PCH xHCI
0x9B06   | PCH   | Post-Mem Performs configuration of PCH xHCI SSIC
0x9B07   | PCH   | Post-Mem Configure PCH xHCI after init
0x9B08   | PCH   | Post-Mem Configures PCH USB device (xDCI)
0x9B0A   | PCH   | Post-Mem DMI/OP-DMI configuration
0x9B0B   | PCH   | Post-Mem Initialize P2SB controller
0x9B0C   | PCH   | Post-Mem IOAPIC initialization
0x9B0D   | PCH   | Post-Mem PCH devices interrupt configuration
0x9B0E   | PCH   | Post-Mem HD Audio initizalization
0x9B0F   | PCH   | Post-Mem HD Audio Codec enumeration
0x9B10   | PCH   | Post-Mem HD Audio Codec not detected
0x9B13   | PCH   | Post-Mem SCS initizalization
0x9B14   | PCH   | Post-Mem ISH initizalization
0x9B15   | PCH   | Post-Mem Configure SMBUS power management
0x9B16   | PCH   | Post-Mem Reserved
0x9B17   | PCH   | Post-Mem Performing global reset
0x9B18   | PCH   | Post-Mem Reserved
0x9B19   | PCH   | Post-Mem Reserved
0x9B40   | PCH   | Post-Mem OnEndOfPEI Entry
0x9B41   | PCH   | Post-Mem Initialize Thermal controller
0x9B42   | PCH   | Post-Mem Configure Memory Throttling
0x9B47   | PCH   | Post-Mem OnEndOfPEI Exit
0x9B4D   | PCH   | Post-Mem Trace Hub - Memory configuration
0x9B4E   | PCH   | Post-Mem Trace Hub - MSC0 configured
0x9B4F   | PCH   | Post-Mem Trace Hub - MSC1 configured
0x9B7F   | PCH   | Post-Mem PchInit Exit
0x9C00   | CPU   | CPU Post-Mem Entry
0x9C09   | CPU   | CpuAddConfigBlocks Done
0x9C0A   | CPU   | SetCpuStrapAndEarlyPowerOnConfig Start
0x9C13   | CPU   | SetCpuStrapAndEarlyPowerOnConfig Reset
0x9C14   | CPU   | SetCpuStrapAndEarlyPowerOnConfig Done
0x9C15   | CPU   | CpuInit Start
0x9C16   | CPU   | SgxInitializationPrePatchLoad Start
0x9C17   | CPU   | CollectProcessorFeature Start
0x9C18   | CPU   | ProgramProcessorFeature Start
0x9C19   | CPU   | ProgramProcessorFeature Done
0x9C20   | CPU   | CpuInitPreResetCpl Start
0x9C21   | CPU   | ProcessorsPrefetcherInitialization Start
0x9C22   | CPU   | InitRatl Start
0x9C23   | CPU   | ConfigureSvidVrs Start
0x9C24   | CPU   | ConfigurePidSettings Start
0x9C25   | CPU   | SetBootFrequency Start
0x9C26   | CPU   | CpuOcInitPreMem Start
0x9C27   | CPU   | CpuOcInit Reset
0x9C28   | CPU   | BiosGuardInit Start
0x9C29   | CPU   | BiosGuardInit Reset
0x9C3F   | CPU   | CpuInitPreResetCpl Done
0x9C42   | CPU   | SgxActivation Start
0x9C43   | CPU   | InitializeCpuDataHob Start
0x9C44   | CPU   | InitializeCpuDataHob Done
0x9C4F   | CPU   | CpuInit Done
0x9C50   | CPU   | S3InitializeCpu Start
0x9C55   | CPU   | MpRendezvousProcedure Start
0x9C56   | CPU   | MpRendezvousProcedure Done
0x9C69   | CPU   | S3InitializeCpu Done
0x9C6A   | CPU   | CpuPowerMgmtInit Start
0x9C71   | CPU   | InitPpm
0x9C7F   | CPU   | CPU Post-Mem Exit
0x9C80   | CPU   | ReloadMicrocodePatch Start
0x9C81   | CPU   | ReloadMicrocodePatch Done
0x9C82   | CPU   | ApSafePostMicrocodePatchInit Start
0x9C83   | CPU   | ApSafePostMicrocodePatchInit Done

@if gen_html
### 8.1.5 NotifyPhase API Status Codes (0x6xxx)
@else
## 8.1.5 NotifyPhase API Status Codes (0x6xxx)
@endif

PostCode | Module | Description
---------|--------|-----------------
0x6800   | FSP    | NotifyPhase API Entry
0x687F   | FSP    | NotifyPhase API Exit

**/
