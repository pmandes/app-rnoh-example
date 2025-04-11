# Troubleshooting

Application should compile and run without issues on the HarmonyNEXT system.
However, when using the current version 0.72 in Open Harmony, problems may occur.

The application was running on the DayU 200 development board with OpenHarmony 5.0.

## Issues:

- [Filename longer than 260 characters](#filename-longer-than-260-characters)
- [Couldn't create bindings between ETS and CPP](#couldnt-create-bindings-between-ets-and-cpp)
- [Kit '@kit.ShareKit' has no corresponding config file in ArkTS SDK](#kit-kitsharekit-has-no-corresponding-config-file-in-arkts-sdk)
- [Error loading shared library librawfile.z.so](#error-loading-shared-library-librawfilezso)
- [Register type windowRectChange failed](#register-type-windowrectchange-failed)

After resolving the issues below, the project compiled and ran correctly:

## Filename longer than 260 characters

Compilation of the project by DevEco ends with the following error:

```shell
> hvigor WARN: ninja: error: Stat(rn/patches/react_native_core/react/renderer/textlayoutmanager/CMakeFiles/react_render_textlayoutmanager.dir/C_/Projects/MyReactNativeApp/oh_modules/@rnoh/react-native-openharmony/src/main/cpp/third-party/rn/ReactCommon/react/renderer/textlayoutmanager/TextMeasureCache.cpp.o): Filename longer than 260 characters

> hvigor ERROR: Failed :entry:default@BuildNativeWithNinja... 
> hvigor ERROR: Exceptions happened while executing: ninja: Entering directory `C:\Projects\MyReactNativeApp\entry\.cxx\default\default\debug\arm64-v8a'
[0/2] Re-checking globbed directories...
ninja: error: Stat(rn/patches/react_native_core/react/renderer/textlayoutmanager/CMakeFiles/react_render_textlayoutmanager.dir/C_/Projects/MyReactNativeApp/oh_modules/@rnoh/react-native-openharmony/src/main/cpp/third-party/rn/ReactCommon/react/renderer/textlayoutmanager/TextMeasureCache.cpp.o): Filename longer than 260 characters

> hvigor ERROR: BUILD FAILED in 2 s 45 ms
```

### Cause:

- The name and path of the compiled file exceed 260 characters. This limitation is imposed by Windows:
 https://learn.microsoft.com/en-us/windows/win32/fileio/maximum-file-path-limitation

- The issue might also be caused by the ninja.exe program:
 https://github.com/ninja-build/ninja/issues/1900

### Solution:

- Make registry changes according to the instructions provided on Microsoft's pages.
- Check if a newer version of the ninja.exe program resolves the issue.
- Move the project directory to the root folder of the hard drive.
- OR: Build the application on macOS or Linux.

## Couldn't create bindings between ETS and CPP

The application compiles but immediately crashes after start:

```javascript
Device info:OpenHarmony 3.2
Build info:OpenHarmony 5.0.0.71
Fingerprint:59107f8d9f3eb72ce161c09a75801513b584c54a4e3cb7a3c25211769024282b
Module name:com.rnoh.tester
Version:1.0.0
VersionCode:1000000
PreInstalled:No
Foreground:Yes
Pid:24187
Uid:20010046
Reason:Error
Error name:Error
Error message:Couldn't create bindings between ETS and CPP. libRNOHApp is undefined.
Stacktrace:
    at RNOHError (react_native_openharmony/src/main/ets/RNOH/RNOHError.ts:35:5)
    at FatalRNOHError (react_native_openharmony/src/main/ets/RNOH/RNOHError.ts:8:10)
    at onInit (react_native_openharmony/src/main/ets/RNOH/NapiBridge.ts:98:19)
    at create (react_native_openharmony/src/main/ets/RNOH/RNInstancesCoordinator.ets:127:7)
    at onCreate (react_native_openharmony/src/main/ets/RNOH/RNAbility.ets:96:35)
```

### Cause

The libRNOHApp library actually corresponds to the dynamic library librnoh_app.so, which is the result of compiling C++ as a dynamic library. The error may occur for two reasons:

- librnoh_app.so does not actually exist.  
- The library is present, but the issue is caused by other factors.

### Solution

To check if the librnoh_app.so file is available, navigate to the root directory of the Harmony project and:

- Check the folder:  
    \entry\build\default\intermediates\libs\default\arm64-v8a\  
    to see if the appropriate .so file is present; 

- Or check if in the package XXX.hap located in:  
    \entry\build\default\outputs\default\  
    the libs/arm64-v8a directory contains the appropriate .so file.


Edit the configuration file entry/build-profile.json5 in the buildOptions section by adding the externalNativeOptions option.


[...]

## Kit '@kit.ShareKit' has no corresponding config file in ArkTS SDK

[...]


## Error loading shared library librawfile.z.so

[...]


## Register type windowRectChange failed

The application starts, the splash screen appears, but does not proceed further. An error appears in the logs:

```
08-05 17:47:27.604   16270-16270   A0beef/#RNOH_ARK                com.examp...loworld5  I     █____  
                                                                                               ██████╗ ███╗   ██╗ ██████╗ ██╗  ██╗
                                                                                               ██╔══██╗████╗  ██║██╔═══██╗██║  ██║
                                                                                               ██████╔╝██╔██╗ ██║██║   ██║███████║
                                                                                               ██╔══██╗██║╚██╗██║██║   ██║██╔══██║
                                                                                               ██║  ██║██║ ╚████║╚██████╔╝██║  ██║
                                                                                               ╚═╝  ╚═╝╚═╝  ╚═══╝ ╚═════╝ ╚═╝  ╚═╝
                                                                                               █____ — MAIN
                                                                                               _█___ — BACKGROUND
                                                                                               __█__ — JS
                                                                                               ___█_ — WORKER
                                                                                               ____█ — CLEANUP
                                                                                               ==================
08-05 17:47:27.609   16270-16270   A0beef/#RNOH_CPP                com.examp...loworld5  I     █____ RNOHAppNapiBridge.cpp:99> onInit (LOG_VERBOSITY_LEVEL=0)
08-05 17:47:27.626   16270-16270   A0beef/#RNOH_CPP                com.examp...loworld5  I     █____ ArkTSBridge.cpp:23> ArkTSBridge::ArkTSBridge
08-05 17:47:27.652   16270-16270   A0beef/#RNOH_ARK                com.examp...loworld5  D     █____ RNAbility::onWindowStageCreate  START
08-05 17:47:27.652   16270-16270   A0beef/#RNOH_ARK                com.examp...loworld5  D     █____ RNAbility::onWindowSetup  START
08-05 17:47:27.673   16270-16270   A0beef/#RNOH_ARK                com.examp...loworld5  E     █____ RNAbility::onWindowSetup  Failed to setup window {"code":801}
```

RNAbility log:

```
08-05 17:47:27.672   16270-16270   C04200/JsRegisterManager        com.examp...loworld5  I     <340>[NAPI]Method windowRectChange has not been registerted
08-05 17:47:27.672   16270-16270   C04200/JsRegisterManager        com.examp...loworld5  E     <385>RegisterListener: [NAPI]Register type windowRectChange failed
```

### Workaround:

**Temporary solution:** comment out in the code in the file RNAbility.ets (line 150):

```typescript
      mainWindow.on('windowSizeChange', onWindowSizeChange)
      //mainWindow.on('windowRectChange', onWindowReactChange)
      windowStage.on('windowStageEvent', onWindowStageChange)
```
