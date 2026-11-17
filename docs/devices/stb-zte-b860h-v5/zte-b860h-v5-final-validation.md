# ZTE B860H V5.0 final validation

Acceptance: **PARTIAL**. The STB matches the baseline that was recorded, and no protected package was changed. The required functional tests were not run. This is not a pass of debloat or of the launcher.

Device evidence used for the comparison: model `B860H V5.0`, device `B860HV5_Telkom`, Android 9 / SDK 28, display `ROGSTB2022_PI.20200628 release-keys`, incremental `V83011303.0016`. The earlier menu label B866V2 and the firmware string `v83011301.0016` remain unresolved.

## Changes made

| Item | Result |
|---|---|
| Packages disabled by this project | None |
| Packages already disabled, still disabled | `com.google.android.tvlauncher`, `com.atvxperience.appdrawer`, `com.yacinenwupdt.v2` |
| Home | Still `ca.dstudio.atvlauncher.pro/ca.dstudio.atvlauncher.screens.launcher.LauncherActivity` |
| Launcher APK | Not built. JDK and Android SDK are missing on the computer. Not installed |
| Reboot | Not performed |
| Settings writes | None |

`pm list packages -f` at the backup still has 127 lines. `pm list packages -d` is still those three packages. No unintended disable was observed. Protected apps that were checked with `pm path` are still installed.

## Tests

| Test | Result |
|---|---|
| Boot to Home | NOT TESTED. No reboot |
| Home button | NOT TESTED |
| D-pad, OK, Back | NOT TESTED |
| Volume | NOT TESTED |
| Remote connection | NOT TESTED. `com.droidlogic.BluetoothRemote` was left enabled and was not exercised |
| Wi-Fi and internet | NOT TESTED. An earlier `dumpsys wifi` showed a setup-wizard `cname` and a system `cname`. The active network was not identified, and no page was loaded |
| Netflix | BLOCKED. Package is installed. Playback was not opened |
| YouTube | BLOCKED. `YouTubeLeanback.apk` is installed. Playback was not opened |
| UseeTV | BLOCKED. Split APK is installed. Channels were not opened |
| Firefox | BLOCKED. Package is installed. Navigation was not opened |
| IPTV / Live TV | BLOCKED. `android.software.live_tv` is present and `TunerTvInputService` is declared. Picture was not checked. The ZTE dashboard package is still unidentified |
| TV input | NOT TESTED |
| HDMI picture and sound | NOT TESTED |
| Sleep / wake | NOT TESTED |

## Performance

No before/after measurement. Free RAM, boot time, storage, and launch time were not compared. No speed claim.

## Recovery

| Check | Result |
|---|---|
| Vendor launcher installed and enabled | PASS, from `pm path` and the live HOME query |
| Manifest readable | PASS. `profiles/zte-b860h-v5/backup-manifest.json` lists non-empty files and SHA-256 values |
| Copied APKs | PASS for the twelve files listed there, including both UseeTV splits |
| Full firmware backup | Not available |
| Rollback commands | Documented in `profiles/zte-b860h-v5/debloat-changelog.md` and `docs/devices/zte-b860h-v5-recovery-plan.md`. Not rehearsed |
| `restore.ps1` record | Absent, because nothing was disabled |
| ADB | Shell access worked with `ro.adb.secure=1`. Whether it survives a reboot was not tested |

## Outstanding risks

- Disabling the current vendor Home is untested and not approved. `com.android.tv.settings` holds `MASTER_CLEAR` and `ZteKeyInterceptor.LedReceiver`.
- `com.woono.util.whomeselector` and setup wizard declare HOME even though the live query does not return them.
- Setup wizard still had a saved-network `cname` on the earlier read.
- A no-record restore would aim Home at the disabled `com.google.android.tvlauncher`.
- Four candidate packages are waiting for a per-package decision. Approval of one is not approval of the others. Approval to install the APK, once a JDK and SDK exist, is not approval to change Home.
