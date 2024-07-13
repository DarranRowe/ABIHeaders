# ABIHeaders
## What This Contains
A repository containing the .idl and C/C++ header files generated from the metadata for the Windows App SDK, WebView 2 and Win2D.
These are generated to be as similar as the Windows SDK ABI files as possible.

## Purpose
While using a projection such as C++/WinRT is preferred, there are some cases where using the projection is not possible.
The files in this repository for some libraries which do not have the ABI files by default.

This repository is incomplete. It doesn't contain any files that are in the NuGet packages, which include the interop headers.
The repository also doesn't contain any IID definitions usable from C. Visual C++ doesn't need these files due to the __uuidof operator
being able to read the IID associated with a class. The IIDs are unable to be generated for the Windows App SDK to how enumerations work.

## Generation Steps
The IDL files are generated from the metadata files using the winmdidl tool, which is part of the Windows SDK. The generated IDL files are
then renamed to only have lowercase filenames. There is one main reason to do this, the include guard in the ABI header file is based upon the
input filename and the Windows SDK include guards are all lower case. The IDL files are then compiled using midlrt, which is also part of the
Windows SDK. Finally, a block of comments at the start of the header files is removed.

## Other Information
The Windows App SDK contains two seemingly similar sets of headers in two separate directories. This is based upon the Windows App SDK's layout,
where it separates the metadata files based upon Windows version. The idea being that you use the metadata files with the version of 17763 when
you are targetting Windows 10 1809 (Redstone 5). The only missing API is Microsoft.UI.Input.Interop.PenDeviceInterop. This outputs a
Windows.Devices.Input.PenDevice instance, which was introduces in Windows 10 1903 (18362).

You should use the headers in the sdk directory, which contains the rest of the Windows App SDK files, along with one of the versioned directories.
If you are only targetting Windows 10 1809, then use the files in the 17763 directory, otherwise use the files in the 18362 directory. If you are
using the 18362 headers, your application is able to run on Windows 10 1809 and you use Microsoft.UI.Input.Interop.PenDeviceIntero, then you 
should use the ApiInformation runtime class to check that the Windows.Foundation.UneversalApiContract version 8 is available.

While the Win2D NuGet package contains an ABI C/C++ header file, it doesn't contain an IDL file. I have generated the IDL file from the Win2D
metadata for this repository. I also generated header files based upon the IDL files just to match the other sets. It is also important to note that
there was no API changes between the 1.1 series of Win2D releases and Win2D 1.2. This is why there is no 1.2 version.

One very important note about cross version usage of these headers. While it is possible, but not supported, to use newer versions of these component
headers with older versions of the runtimes, Win2D 1.1 did something very naughty with its interface definitions in 1.1. They changed some interfaces
but kept the IIDs the same. This is against the general rules of COM. But be careful to never use the 1.1/1.2 Win2D headers or even the C++/WinRT
projection to interface with Win2D 1.0. It is possible to get unexpected crashes if you try.