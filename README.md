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
* Installed GIT for VS (https://visualstudio.github.com/)
* Forked repository
* Installed vcpkg (https://github.com/microsoft/vcpkg)
* Installed boost (1.73)
* Installed protobuf (3.13.0)
* Realized vcpkg defaults to x86
* Reinstall boost (:x64-windows) (18 min install time)
* Reinstall protobuf (:x64-windows)
* Cannot install older version of libraries for vcbkg (https://github.com/microsoft/vcpkg/issues/1681)
* Protoc.exe located (C:\src\vcpkg\installed\x64-windows\tools\protobuf)
* I think I need to generate a new MetaData.pb.h for the latest protobuf 3.13 version
* Protobuf tutorial starts with a *.proto file (https://developers.google.com/protocol-buffers/docs/cpptutorial)
* Protoc.exe found in C:\src\vcpkg\installed\x64-windows\tools\protobuf
* Running tutorial code successful using command ./protoc -I=. --cpp_out=. ./test.proto
* Trying protobuf 2.5 for now before attempting to find *.proto file (https://github.com/protocolbuffers/protobuf/releases?after=v2.6.1)
* Found protobuf 2.5 on NuGet
* Uninstalled protobuf 3.13.0 installed on vcpkg (.\vcpkg remove protobuf:x64-windows)
* Looks like only DirectX errors are left
* Install DirectX SDK.  Fix for S21023 error (https://jaewon.hwang.info/jaewon/2015/05/08/s1023-error-when-you-install-the-directx-sdk-june-2010/)
* Fatal Error RC1015 (https://community.developers.refinitiv.com/questions/8225/fatal-error-rc1015-cannot-open-include-file-afxres.html)
* Linker Error (https://stackoverflow.com/questions/23471337/lnk1117-syntax-error-in-option-version1-0-0)
* Still missing lib files even after getting nuget protobuf 2.5
* Going back to building protobuf 2.5 from link (https://github.com/protocolbuffers/protobuf/releases?after=v2.6.1)
* Error on VS 2019 when building libraries.  Adding _SILENCE_STDEXT_HASH_DEPRECATION_WARNINGS=1; fixes error (https://stackoverflow.com/questions/30430789/c-hash-deprecation-warning)
* Add files to C:\src\ProtobufLibs (libprotobuf.lib/libprotobuf-lite.lib/libprotoc.lib) and referenced in Properties/VC Directory/Library Directories
* Error with complied libraries.  Need to recompile libprotobuf.lib/libprotobuf-lite.lib/libprotoc.lib libraries - C/C++ --> Code Generation --> Runtime Library, select the Multi-threaded (/MT)  (https://stackoverflow.com/questions/28887001/lnk2038-mismatch-detected-for-runtimelibrary-value-mt-staticrelease-doesn)
* The program loads!

8/31/20

Tweeks to learn the program
* Reduced opening sound to 3 seconds

Notes to try to figure out how to get playback on Nektar key press
* Gamestate.cpp has text for button presses
```
const wchar_t *GameScore::MissedText = L"Missed!";
const wchar_t *GameScore::IncorrectText = L"Wrong!";
const wchar_t *GameScore::OkText = L"OK!";
const wchar_t *GameScore::GoodText = L"Good!";
const wchar_t *GameScore::GreatText = L"Great!";
```
* Main logic code seems to be in Gamestate.cpp
```
GameState::GameError MainScreen::Logic( void )
```

* Possible Sound Logic
```
        // Advance start position updating initial state as we pass stale events
        // Also PLAYS THE MUSIC
        while ( m_iStartPos < iEventCount && m_vEvents[m_iStartPos]->GetAbsMicroSec() <= m_llStartTime )
        {
            MIDIChannelEvent *pEvent = m_vEvents[m_iStartPos];
            if ( pEvent->GetChannelEventType() != MIDIChannelEvent::NoteOn )
                m_OutDevice.PlayEvent( pEvent->GetEventCode(), pEvent->GetParam1(), pEvent->GetParam2() );
            else if ( !m_bMute && !m_vTrackSettings[pEvent->GetTrack()].aChannels[pEvent->GetChannel()].bMuted &&
                      ( m_eGameMode != Learn || m_iLearnOrdinal >= 0 ) )
                m_OutDevice.PlayEvent( pEvent->GetEventCode(), pEvent->GetParam1(),
                                       static_cast< int >( pEvent->GetParam2() * dVolumeCorrect + 0.5 ) );
            UpdateState( m_iStartPos );
            m_iStartPos++;
        }
```

* Possible Key Render on Key Press Logic (White Keys)
```
                bool bBadLearn = ( m_eGameMode == Learn && m_iLearnOrdinal >= 0 && ( iTrack != m_iLearnTrack || iChannel != m_iLearnChannel ) );
                ChannelSettings &csKBWhite = ( m_pInputState[i] == -2 || bBadLearn ||
                                               pEvent->GetInputQuality() == MIDIChannelEvent::Missed ? m_csKBBadNote :
                                               m_vTrackSettings[iTrack].aChannels[iChannel] );
                m_pRenderer->DrawRect( fCurX + fKeyGap1 , fCurY, m_fWhiteCX - fKeyGap, fTopCY + fNearCY - 2.0f,
                    csKBWhite.iDarkRGB | iAlpha, csKBWhite.iDarkRGB | iAlpha, csKBWhite.iPrimaryRGB | iAlpha, csKBWhite.iPrimaryRGB | iAlpha );
                m_pRenderer->DrawRect( fCurX + fKeyGap1 , fCurY + fTopCY + fNearCY - 2.0f, m_fWhiteCX - fKeyGap, 2.0f, csKBWhite.iDarkRGB | iAlpha );
```

* Possible Key Render on Key Press Logic (Sharps)
```
const bool bBadLearn = ( m_eGameMode == Learn && m_iLearnOrdinal >= 0 && ( iTrack != m_iLearnTrack || iChannel != m_iLearnChannel ) );
                const ChannelSettings &csKBSharp = ( m_pInputState[i] == -2 || bBadLearn ||
                                                     pEvent->GetInputQuality() == MIDIChannelEvent::Missed ? m_csKBBadNote :
                                                     m_vTrackSettings[iTrack].aChannels[iChannel] );
                m_pRenderer->DrawSkew( fSharpTopX1, fCurY + fSharpCY - fNewNear,
                                       fSharpTopX2, fCurY + fSharpCY - fNewNear,
                                       x + cx, fCurY + fSharpCY, x, fCurY + fSharpCY,
                                       csKBSharp.iPrimaryRGB | iAlpha, csKBSharp.iPrimaryRGB | iAlpha, csKBSharp.iDarkRGB | iAlpha, csKBSharp.iDarkRGB | iAlpha );
                m_pRenderer->DrawSkew( fSharpTopX1, fCurY - fNewNear,
                                       fSharpTopX1, fCurY + fSharpCY - fNewNear,
                                       x, fCurY + fSharpCY, x, fCurY,
                                       csKBSharp.iPrimaryRGB | iAlpha, csKBSharp.iPrimaryRGB | iAlpha, csKBSharp.iDarkRGB | iAlpha, csKBSharp.iDarkRGB | iAlpha );
                m_pRenderer->DrawSkew( fSharpTopX2, fCurY + fSharpCY - fNewNear,
                                       fSharpTopX2, fCurY - fNewNear,
                                       x + cx, fCurY, x + cx, fCurY + fSharpCY,
                                       csKBSharp.iPrimaryRGB | iAlpha, csKBSharp.iPrimaryRGB | iAlpha, csKBSharp.iDarkRGB | iAlpha, csKBSharp.iDarkRGB | iAlpha );
                m_pRenderer->DrawRect( fSharpTopX1, fCurY - fNewNear, fSharpTopX2 - fSharpTopX1, fSharpCY, csKBSharp.iDarkRGB | iAlpha );
                m_pRenderer->DrawSkew( fSharpTopX1, fCurY - fNewNear,
                                       fSharpTopX2, fCurY - fNewNear,
                                       fSharpTopX2, fCurY - fNewNear + fSharpCY * 0.35f,
                                       fSharpTopX1, fCurY - fNewNear + fSharpCY * 0.25f,
                                       csKBSharp.iPrimaryRGB | iAlpha, csKBSharp.iPrimaryRGB | iAlpha, csKBSharp.iPrimaryRGB | iAlpha, csKBSharp.iPrimaryRGB | iAlpha );
                m_pRenderer->DrawSkew( fSharpTopX1, fCurY - fNewNear + fSharpCY * 0.25f,
                                       fSharpTopX2, fCurY - fNewNear + fSharpCY * 0.35f,
                                       fSharpTopX2, fCurY - fNewNear + fSharpCY * 0.75f,
                                       fSharpTopX1, fCurY - fNewNear + fSharpCY * 0.65f,
                                       csKBSharp.iPrimaryRGB | iAlpha, csKBSharp.iPrimaryRGB | iAlpha, csKBSharp.iDarkRGB | iAlpha, csKBSharp.iDarkRGB | iAlpha );
```