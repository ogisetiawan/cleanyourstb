# ZTE B860H V5.0 (`B860HV5_Telkom`), Android 9: audit profile, no debloat list

Read-only audit of one IndiHome STB on 2026-11-14. Nothing was disabled, installed, uninstalled, rebooted, or set as Home, and no setting was written. This is not a tested `examples/` list and it does not apply to any other ZTE box. `profiles/zte-b860h-v5/approved-disable.txt` contains no package names.

The box that answered is `B860H V5.0` / `B860HV5_Telkom`, not the Android 14 `B866V2K` or `ZXV10 B866F` notes. Do not reuse those lists.

## Identify the unit

| Field | Observed value |
|---|---|
| `ro.product.manufacturer` / `ro.product.brand` | `ZTE` / `IndiHome` |
| `ro.product.model` / `ro.product.device` | `B860H V5.0` / `B860HV5_Telkom` |
| Android / SDK | 9 / 28 |
| Fingerprint | `IndiHome/B860HV5_Telkom/B860HV5_Telkom:9/PI/V83011303.0016:user/release-keys` |
| `ro.build.display.id` | `ROGSTB2022_PI.20200628 release-keys` |
| Board / hardware / ABI | `AMLS905X2-BU` / `amlogic` / `armeabi-v7a` |
| `ro.adb.secure` | `1` (port 5555 was already open; it was left as found) |

`pm list packages` returned 127 packages. Classification below uses `pm list packages -f/-s/-3/-d` and a filtered `dumpsys package` for each name. Where the dump had no label and no matching component, the purpose is recorded as not established.

## Home

The live `MAIN` / `HOME` query returned two activities:

- `ca.dstudio.atvlauncher.pro/ca.dstudio.atvlauncher.screens.launcher.LauncherActivity`, priority 0, and this is the resolved Home. Path `/vendor/app/ATV`, version `0.1.5-pro`, flags include `SYSTEM` and `PERSISTENT`.
- `com.android.tv.settings/.system.FallbackHome`, priority -1000.

These packages declare `HOME` in their dumps and were **not** returned by that query: `com.google.android.tungsten.setupwraith` (`.MainActivity` and `.RecoveryActivity`) and `com.woono.util.whomeselector` (`/vendor/app/Selector`, version `1.0.2`, `targetSdk` 15, holds `RESTART_PACKAGES`). Whether either would take Home after the vendor launcher was disabled was not tested. `com.google.android.tvlauncher` also declares `HOME` and is already disabled, so it is not a current candidate.

`launcher/build-and-install.ps1` only auto-detects `com.google.android.tvlauncher`, `com.google.android.apps.tv.launcherx`, `com.google.android.leanbacklauncher`, and `com.mitv.tvhome`. It would stop before pushing an APK on this Home. It was not run. `-StockLauncher none` would still call `set-home-activity`. Passing `ca.dstudio.atvlauncher.pro` as `-StockLauncher` would disable the only enabled system Home the query returns. Neither flag was used.

## What must stay

Owner requirements, matched only where the dump supports the match:

| Requirement | Package | Evidence used | Limit |
|---|---|---|---|
| Current Home | `ca.dstudio.atvlauncher.pro` | Resolved HOME component, `/vendor/app/ATV`, `SYSTEM`+`PERSISTENT` | Do not disable |
| YouTube | `com.google.android.youtube.tv` | Path `YouTubeLeanback.apk`, leanback category, `libraries.youtube.tv` class | Not opened |
| YouTube client also installed | `com.teamsmart.videomanager.tv` | Leanback category and `com.liskovsoft.smartyoutubetv2` class | Not opened. Separate from the system YouTube package |
| Netflix | `com.netflix.ninja` | User app, version `8.3.5`, leanback category | No label in the dump. Not opened |
| UseeTV | `com.useetv.stb` | User app, version `1.10.4`, leanback category | No label in the dump. Not opened |
| Firefox | `org.mozilla.tv.firefox` | User app, version `3.10`, leanback category | No label in the dump. Not opened |
| Live TV input | `com.google.android.tv` | `TunerTvInputService`, `BIND_TV_INPUT`, versionName contains `live_channels` | The only package whose dump registered `TvInputService` |
| Settings and ZTE key hook | `com.android.tv.settings` | `android.settings.SETTINGS`, `HOME`, `sharedUser android.uid.system` | See the interceptor below |

`TV_INPUT_HARDWARE` appeared only on the framework package `android`, which also registers `MasterClearReceiver` for `FACTORY_RESET`. Bluetooth evidence is the system `Bluetooth.apk`, `/vendor/app/BluetoothRemote/BluetoothRemote.apk` with `BootCompletedReceiver`, and `AtvRemoteService` with `BootReceiver`. The Latin IME is `/system/app/LatinIME`. A second keyboard package is `/system/app/LeanKey/LeanKey.apk`. Wi-Fi ownership was not re-tested in this pass; the earlier read-only `dumpsys wifi` on this same unit showed `cname=com.google.android.tungsten.setupwraith` and `cname=android.uid.system:1000`. Which of those is the active network was not determined.

## ZTE packages and the factory-reset notes

`com.control.device` is not installed. `/system/framework/FrameworkCustomization.jar` and the `ControlDevice.apk` paths from [zte-b866f.md](zte-b866f.md) are absent. No sampled ZTE package dump contained a `FACTORY_RESET` intent filter. That does not clear this firmware.

`com.android.tv.settings` is the place a ZTE hook actually showed up. It runs as `android.uid.system`, `MASTER_CLEAR` is granted, and `ZteKeyInterceptor.LedReceiver` listens for `BOOT_COMPLETED` plus `com.zte.ZteKeyInterceptor.LedService` and `com.zte.ZteKeyInterceptor.RestartService`. The receiver was not started. What it does on boot is unverified. Leave Settings enabled.

Every `com.zte.*`, `com.ztestb.*`, and `zteplayer.service` package stays protected. Several are still unidentified: `StbDashWithBlink.apk` has a boot receiver and no TV-input or leanback filter in the dump, and `zteplayer.service`'s code path is `SubtitleService.apk`, which does not match the package id. `FactoryTestTool.apk` receives `BOOT_COMPLETED` and `com.zte.stb.Activity2Front`. Do not launch it. `ZteRemoteOta.apk` is kept so the firmware-update path is still there; its `versionName` was null and an update was not run.

`com.ztestb.basecomp` (`ZTEMcspBase.apk`) is the same package name the B866F note says not to disable. This box did not show that note's watchdog.

## Already disabled

These three were already disabled (`pm list packages -d`, `enabled=2`). Who disabled them is unknown. This audit does not enable them and does not count them as an approved disable.

- `com.google.android.tvlauncher` (`/system/priv-app/TVLauncher`, declares `HOME`)
- `com.atvxperience.appdrawer` (`/vendor/app/AppDrawer`, version `2.1.1`, leanback category)
- `com.yacinenwupdt.v2` (user app under `/data/app`, version `2.0`, stopped). No label. It was not launched.

## Profile files

| File | What it is |
|---|---|
| `profiles/zte-b860h-v5/package-inventory.json` | All 127 packages: path, system/user, enabled state, purpose, evidence, risk, action, confidence |
| `profiles/zte-b860h-v5/protected-packages.txt` | 89 names that stay enabled. Commented so `disable.ps1` sees no packages and stops |
| `profiles/zte-b860h-v5/review-packages.txt` | 4 candidates awaiting approval, and 31 names that stay unknown. Not approval |
| `profiles/zte-b860h-v5/approved-disable.txt` | No package names |
| `profiles/zte-b860h-v5/backup-manifest.json` | Hashes for the configuration record and the selected APK copies. Not a firmware image |
| `profiles/zte-b860h-v5/debloat-changelog.md` | Disable log. Empty of actions: no `pm disable-user` was sent |
| [zte-b860h-v5-change-notes.md](zte-b860h-v5-change-notes.md) | Session note: debloat, installs, and layout. Nothing on the STB was changed |
| [zte-b860h-v5-app-recommendations.md](zte-b860h-v5-app-recommendations.md) | Apps considered for later. None of them were installed |

The review set is where a later question belongs: other leanback apps (`iflix.play`, `com.vidio.android.tv`, `com.mibi.tivi`, `com.redstream.app`, `ar.tvplayer.tv`, `com.mxtech.videoplayer.pro`, YouTube Kids), Assistant (`com.google.android.katniss`, classes under `com.google.android.apps.tvsearch`), `com.google.android.apps.mediashell`, recommendation and backdrop packages, TalkBack, and the system Leanback launcher. Leanback launcher has a boot receiver and no `HOME` category in its dump. None of these were disabled.

`iflix.play` also contains `com.tencent.qqlivetv` and `com.tencent.oma.push` classes. That identifies a Tencent stack inside the package. It does not identify it as safe to remove.

## Recovery

No `~/.cleanyourtv` record exists for this STB, because `disable.ps1` has not run. A record is what preserves the original enabled/disabled states and the original Home component.

With a record, `tools/recovery.py` re-enables only packages that were enabled when the record was written, leaves packages that were already disabled, and puts Home back on the recorded component when that component is not `com.gathin.tvhome`. On this box that component is `ca.dstudio.atvlauncher.pro/...LauncherActivity`. The known-stock list inside `recovery.py` does not include that package. The Home field of the record is what would put it back, not the stock-launcher default.

Without a record, `restore.ps1` enables every name in the list it is given and sets Home toward `com.google.android.tvlauncher` unless another package is passed. That Google launcher is currently disabled here. A no-record restore would turn it on and try to make it Home. Do not run restore for this box until a record from this unit exists, and do not point restore at another model's list.

`tools/disable.sh`, `disable.ps1`, `restore.sh`, `restore.ps1`, `recovery.py`, and `launcher/build-and-install.ps1` were not modified. Changes worth a maintainer decision, not made here:

- The no-record restore fallback should not assume `com.google.android.tvlauncher` when that package is disabled or is not the resolved Home. This box is that case.
- Do not add `ca.dstudio.atvlauncher.pro` to `KNOWN_STOCK_LAUNCHERS`. That would make the installer disable the current Home by itself.
- The Home guard's fail-closed list of known launchers does not include `ca.dstudio.atvlauncher.pro` or `com.woono.util.whomeselector`. If a future list names them and the TV does not answer the Home query, the guard would not skip those two names on its known-launcher path.

## Not verified

Playback of Netflix, YouTube, UseeTV, Firefox, and IndiHome; HDMI; the remote; which saved network is in use; whether `pm disable-user` is accepted; whether a `HOME` sideload is accepted; what `ZteKeyInterceptor.LedReceiver` does; what `com.yacinenwupdt.v2` is. SDK 28 is above the launcher `minSdk` 26. That is only a version gate.

## Later phases

Package evidence, the backup limit, and the decision to stop before any STB change are in [the verification note](zte-b860h-v5-package-verification.md), [the recovery plan](zte-b860h-v5-recovery-plan.md), [the final validation](zte-b860h-v5-final-validation.md), and [the session change notes](zte-b860h-v5-change-notes.md). No package was disabled and the launcher was not installed. This computer has no JDK and no Android SDK, so the APK was not built.
