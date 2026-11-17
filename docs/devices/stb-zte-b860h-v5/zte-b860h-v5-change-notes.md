# ZTE B860H V5.0: session change notes

Device: ZTE B860H V5.0 / `B860HV5_Telkom` / IndiHome, Android 9 (SDK 28), firmware `V83011303.0016`.

**Nothing on the STB was changed.** The audit was read-only. No `pm disable-user`, no `pm uninstall`, no APK install, no Home change, no reboot, and no settings write.

The menu label B866V2 and the firmware string `v83011301.0016` are still not reconciled with the ADB identity. Technical work uses the ADB identity.

## Debloat

| Item | State |
|---|---|
| Packages disabled by this project | None |
| `profiles/zte-b860h-v5/approved-disable.txt` | Empty. `disable.ps1` refuses the file and sends nothing |
| Already disabled before this project, and left that way | `com.google.android.tvlauncher`, `com.atvxperience.appdrawer`, `com.yacinenwupdt.v2` |
| Protected, still enabled | 89 names in `protected-packages.txt`, including the current Home, Netflix, YouTube, UseeTV, Firefox, Live TV, Settings, Bluetooth, and every `com.zte.*` / `com.ztestb.*` package |
| Candidates, not approved | `com.android.htmlviewer`, `com.android.printspooler`, `com.google.android.marvin.talkback`, `com.google.android.youtube.tvkids` |
| Still UNKNOWN | 31 names in `review-packages.txt`. Not touched |

Rollback for one candidate, if it is later approved on its own: `pm enable --user 0 <package>`. That command was not run. The technical log is `profiles/zte-b860h-v5/debloat-changelog.md`.

## App installs

| Item | State |
|---|---|
| CleanYourTV (`com.gathin.tvhome`) | Not built. This computer has no JDK 17 and no Android SDK. Not installed |
| Netflix, system YouTube, UseeTV, Firefox, Live Channels | Already installed before the audit. Playback was not opened |
| Other YouTube client | `com.teamsmart.videomanager.tv` is installed. Playback was not tested |
| Apps on the recommendation list | None installed. The list is [zte-b860h-v5-app-recommendations.md](zte-b860h-v5-app-recommendations.md) |
| BitTV `v2.1.8` | The APK is in the local `APK/` folder. Not committed, not installed. Source, signature, and permissions are unverified, so it is not on the recommendation list |
| APK copies kept for rollback | In `backup/zte-b860h-v5/apks/` (Home, Netflix, YouTube, UseeTV plus both splits, Firefox, Live Channels, the already-disabled TV launcher, StbDash, TV Settings, SmartTube). Not app data, DRM, or firmware |

`backup/` and `APK/` stay out of Git. Hashes for the selected copies are in `profiles/zte-b860h-v5/backup-manifest.json`.

## Layout and launcher

| Item | State |
|---|---|
| Current Home | `ca.dstudio.atvlauncher.pro` / `LauncherActivity` (ATV Launcher Pro `0.1.5-pro`, `/vendor/app/ATV`) |
| Default Home | Not changed |
| Theme, wallpaper, dock, column count, `config.xml` | Not changed |
| `launcher/build-and-install.ps1` | Not run. Without `-StockLauncher` the script stops before `pm install`, because this vendor Home is not in the script's known-launcher list. `-StockLauncher` or `-StockLauncher none` can disable a launcher or call `set-home-activity`. Neither is approved |

The build plan and the install limits are in [zte-b860h-v5-launcher-plan.md](zte-b860h-v5-launcher-plan.md). Acceptance is **PARTIAL**: the baseline matches, and the required functional tests were not run ([zte-b860h-v5-final-validation.md](zte-b860h-v5-final-validation.md)).

## What goes to Git, and what stays on the computer

Part of the device-profile commit:

- `profiles/zte-b860h-v5/approved-disable.txt`
- `profiles/zte-b860h-v5/protected-packages.txt`
- `profiles/zte-b860h-v5/backup-manifest.json`
- `profiles/zte-b860h-v5/debloat-changelog.md`
- `docs/devices/stb-zte-b860h-v5/zte-b860h-v5-change-notes.md` (this file)
- `docs/devices/stb-zte-b860h-v5/zte-b860h-v5-app-recommendations.md`
- `.gitignore` ignores `*.apk` and the `APK/` folder

Stays local, because `work/` and `APK/` are gitignored:

- `work/local-prompts/TASK.md`
- `work/local-prompts/BUILD_AND_PREPARE_CLEANYOURTV.md`
- `work/local-prompts/CLEANYOURTV_PHASE_PROMPTS.md`
- `APK/BitTV-v2.1.8-agate.apk`
- `backup/zte-b860h-v5/` (package lists and APK copies)

The prompts in `work/local-prompts/` contain the home-network ADB address. That address is not written in the committed documents.
