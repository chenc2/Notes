# Module Information File
&ensp;&ensp;&ensp;&ensp;.inf file describes how the module was compiled, linked and what platform configuration database items (PCDs) are exposed. 
## Section Table
|Section|Description|
|:-----:|:---------:|
|[Defines]|Used to define variable assignments that can be used in later build steps.|
|[Sources]|Used to specify the files that make up the component
|[BuildOptions]|Defines module specific tool chain flags that must be used as the default flags for a module.|
|[Protocols]|A list of the global Protocol Names that are used by the module developer.|
|[Guids]|A list of the global GUID Names that are used by the module, and not already included.|
|[LibraryClasses]|Used to list the names of the library classes that are required, or optionally required by a component.|
|[Packages]|Lists all of the EDK II declaration files that are used by the component.|

# Platform Description File
## Section Table
|Section|Description|
|:-----:|:---------:|
|[Defines]|Used to define variable assignments that can be used in later build steps.|
|[BuildOptions]|Used to define module specific tool chain flags rather than use the default flags for a module.|
|[LibraryClasses]|used to provide a mapping between the library class names used by an EDK II module and the Library Instances that are selected by the platform integrator. <br>For example, PrintLib\|MdePkg/Library/BasePrintLib/BasePrintLib.inf|
|[Components]|This section defines the modules and components that will be processed by compilation tools and the EDK II tools used to generate PE32/PE32+/Coff image files.|

# EDK II Declaration File
&ensp;&ensp;&ensp;&ensp;The file is used to define specific information that will be shared between different EDK II Modules.
## Section Table
|Section|Description|
|:-----:|:---------:|
|[Defines]|Used to track the package's GUID and version|
|[Includes]|Used to identify the "standard" location "include directories" provide by this EDK II package.|
|[Guids]|Define the GUID Value for Guid|
|[Protocols]|Define the GUID Value for Protocol|
|[LibraryClasses]|Define the headers associated with the new EDK II library classes. <br> For example: PrintLib\|Include/Library/PrintLib.h|
