# ZTE B860H V5.0 launcher plan

The CleanYourTV APK was not built and was not installed. Default Home was not changed. `ca.dstudio.atvlauncher.pro` is still the resolved Home.

## Compatibility that is confirmed, and what is not

| Check | Result |
|---|---|
| Device SDK | 28, from `getprop ro.build.version.sdk` |
| Launcher `minSdk` / `targetSdk` | 26 / 34 in `launcher/app/build.gradle` |
| `minSdk <= 28` | True. This does not mean the APK will install or that Home can be handed over |
| Leanback feature | `android.software.leanback` and `android.software.leanback_only` are present |
| Live TV feature | `android.software.live_tv` is present |
| Wi-Fi feature | `android.hardware.wifi` is present |
| Touchscreen | Not required by the manifest (`required=false`) |
| ABI | Device is `armeabi-v7a`. The launcher is Java. Native ABI was not the blocker |
| `INTERNET` permission | The manifest does not declare it |
| HOME filter | `com.gathin.tvhome.MainActivity` declares `MAIN`, `HOME`, `COMMON_HOME`, and `DEFAULT` |
| Run beside the vendor launcher | Not tested. The installer script does not have a mode that only adds the APK |
| Android 9 `set-home-activity` | The install script already notes that Android 9 needs a component. Not tested here |

## Build environment

`JAVA_HOME` is empty, `java` is not on `PATH`, and `%LOCALAPPDATA%\Android\Sdk` does not exist. No JDK 17 and no Android SDK platform 34 were found. Nothing was installed. The build command was not started. There is no APK path, size, version, or signature to record.

## What `launcher/build-and-install.ps1` would change

The script was read and not run. On this box, Home resolves to `ca.dstudio.atvlauncher.pro`, which is not in the script's known-stock list, so a run without `-StockLauncher` stops after the SDK check and the Gradle build, before `pm install`. A build still needs the missing JDK and SDK.

If someone passes `-StockLauncher`, or `-StockLauncher none`, the script can then:

| Command | Effect |
|---|---|
| `pm install -r` of `app-release.apk` | Installs `com.gathin.tvhome` |
| `am force-stop com.gathin.tvhome` and `settings put secure` | Restarts the new app and can rewrite the accessibility list |
| `pm grant` of location | Grants the network-name pill |
| `cmd package compile` | Compiles the new app |
| `pm disable-user --user 0 <stock>` | Disables whatever `-StockLauncher` named. Naming the current Home would disable it |
| `cmd package set-home-activity --user 0 com.gathin.tvhome/.MainActivity` | Changes the default Home, including when `-StockLauncher none` |
| Optional `InputKeyService` / `HomeKeyService` | Turns on accessibility helpers and writes secure settings |
| `pm grant` or `pm revoke` of `WRITE_SECURE_SETTINGS` | Changes a protected permission |

Those steps need a separate approval. Installing the APK is not approval to change Home. Disabling `ca.dstudio.atvlauncher.pro` is not approved.

## Route back, untested

The recorded component is `ca.dstudio.atvlauncher.pro/ca.dstudio.atvlauncher.screens.launcher.LauncherActivity`. Putting Home back there, after a future approved switch, would be `cmd package set-home-activity --user 0` with that component, while the vendor package is still enabled. That command was not sent, so the route is documented and not verified.
