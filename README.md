# PianoFromAbove

This is the software that drives some of the Impossible MIDI videos on youtube:

<p align="center">
  <a href="https://www.youtube.com/watch?v=p_c6uQHlhZ0" target="_blank">
    <img src="https://img.youtube.com/vi/p_c6uQHlhZ0/hqdefault.jpg"/>
  </a>
</p>

Synthesia is the main alternate with way more market share and much more active development, but appaerently PFA is more performant, so some prefer it. Yay.

The original inspiration:

<p align="center">
  <a href="https://www.youtube.com/watch?v=mTS16klgqMU" target="_blank">
    <img src="https://img.youtube.com/vi/mTS16klgqMU/hqdefault.jpg"/>
  </a>
</p>

And so I made it happen:


<p align="center">
  <a href="https://www.youtube.com/watch?v=PWQj61p6D5s" target="_blank">
    <img src="https://img.youtube.com/vi/PWQj61p6D5s/hqdefault.jpg"/>
  </a>
</p>

## Binaries

https://github.com/brian-pantano/PianoFromAbove/releases

## Viz branch

There's now a viz branch which will house graphics and performance updates going forward (if there is a forward).

## How to build

This is unfortunately very tricky. Hopefully I will simplify this in the future.

* clone this repo
* Download and install VisualStudio 2010
* Download and install Direct X SDK
* Download and extract Google Protocol Buffers 2.5
  * Build libprotobuf-lite.vcproj
* Download and extract Boost 1.55
* Open the .sln and edit the VC++ Directories from the project properties so that the Include Directories and Library Directories point to the location of your boost and protocol buffers downloads
* Cross fingers
* Build! (Release, x64)

Once that's done, there should be a Release\PFA-1.1.0-x86_64.exe that you can run.

There's an optional .nsi script that you can run if you want to build an installer.

The code probably isn't the best, and it probably goes against all sorts of best practices but it is fairly snappy. I'm not very good at writing UI or UX, but I am fairly good at writing datastructures and writing minimal and fast code. Good luck reading it! 

## This Fork
8/30/20

Goal
* I am learning how to program.  I have zero/minimal C++ experience.  My first goal is to complie PianoFromAbove using VS Community 2019.  This might not work.  My second goal is to allow note playback on a Nektar Midi Controller.

Steps During Testing
* Installed VS 2019 Community
* Installed GIT for VS
* Forked repository
* Installed vcpkg (https://github.com/microsoft/vcpkg)
* Installed boost
* Installed protobuf
* Realized vcpkg defaults to x86
* Reinstall boost (:x64-windows) (18 min install time)
* Reinstall protobuf (:x64-windows)
* Specific version issue for vcbkg (https://github.com/microsoft/vcpkg/issues/1681)
* Protoc.exe located (C:\src\vcpkg\installed\x64-windows\tools\protobuf)
* I think I need to generate a new MetaData.pb.h for the latest protobuf version
* Protobuf tutorial starts with a *.proto file (https://developers.google.com/protocol-buffers/docs/cpptutorial)
* Protoc.exe found in C:\src\vcpkg\installed\x64-windows\tools\protobuf
* Running tutorial code successful using command ./protoc -I=. --cpp_out=. ./test.proto
* Trying protobuf 2.5 for now before attempting to find *.proto file (https://github.com/protocolbuffers/protobuf/releases?after=v2.6.1)
* Found protobuf 2.5 on NuGet
* Uninstalled protobuf installed on vcpkg (.\vcpkg remove protobuf:x64-windows)
* Looks like only DirectX errors are left
* Install DirectX SDK.  Fix for S21023 error (https://jaewon.hwang.info/jaewon/2015/05/08/s1023-error-when-you-install-the-directx-sdk-june-2010/)
* Fatal Error RC1015 (https://community.developers.refinitiv.com/questions/8225/fatal-error-rc1015-cannot-open-include-file-afxres.html)
* Linker Error (https://stackoverflow.com/questions/23471337/lnk1117-syntax-error-in-option-version1-0-0)
* Going back to building 2.5
* Error on VS 2019 (https://stackoverflow.com/questions/30430789/c-hash-deprecation-warning)
* Recomplie libraries - C/C++ --> Code Generation --> Runtime Library, select the Multi-threaded (/MT)  (https://stackoverflow.com/questions/28887001/lnk2038-mismatch-detected-for-runtimelibrary-value-mt-staticrelease-doesn)
* The program loads!




Current Errors
* Tons

