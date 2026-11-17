# ZTE B860H V5.0 package verification

Read-only recheck on the same STB identified as `B860H V5.0` / `B860HV5_Telkom`, Android 9 / SDK 28, incremental `V83011303.0016`. No package was disabled. `approved-disable.txt` still has no package names.

The menu label B866V2 and the earlier firmware string `v83011301.0016` are still unresolved. They are not the ADB identity.

## Required applications

| Request | Package | Status | Evidence | Confidence |
|---|---|---|---|---|
| Netflix | `com.netflix.ninja` | PROTECTED, installed, enabled | Single `base.apk` under `/data/app`, leanback category from the earlier dump. Reconfirmed present with `pm path` | CONFIRMED present. Playback NOT TESTED |
| YouTube | `com.google.android.youtube.tv` | PROTECTED, installed, enabled | `pm path` returns only `/system/app/YouTubeLeanback/YouTubeLeanback.apk` | CONFIRMED present. Playback NOT TESTED |
| YouTube client also present | `com.teamsmart.videomanager.tv` | PROTECTED | Earlier dump contains `com.liskovsoft.smartyoutubetv2`. Single `base.apk` | INFERRED role from the class name. Playback NOT TESTED |
| UseeTV | `com.useetv.stb` | PROTECTED, installed, enabled | `pm path` returns `base.apk`, `split_config.hdpi.apk`, and `split_config.in.apk` | CONFIRMED present as a split app. Channel playback NOT TESTED |
| Firefox | `org.mozilla.tv.firefox` | PROTECTED, installed, enabled | Single `base.apk` under `/data/app` | CONFIRMED present. Navigation NOT TESTED |
| Live TV input | `com.google.android.tv` | PROTECTED | Earlier dump registers `TunerTvInputService`. `pm list features` includes `android.software.live_tv` | CONFIRMED feature and service declaration. Picture NOT TESTED |

No other package in the review set registered `TvInputService` or `TV_INPUT_HARDWARE`. That does not prove the IndiHome stack is only `com.google.android.tv`. `com.zte.iptvclient.android.stbdash` (`StbDashWithBlink.apk`) is still an unidentified ZTE system package and stays protected. `com.zte.zteplayer`, `zteplayer.service` (`SubtitleService.apk`), and `com.droidlogic.SubTitleService` stay protected for the same reason.

## Home and remote-related packages

Live HOME query, again: `ca.dstudio.atvlauncher.pro/...LauncherActivity` is the default. `com.android.tv.settings/.system.FallbackHome` is the only other returned activity. Setup wizard and `com.woono.util.whomeselector` still declare HOME in their earlier dumps and still did not appear in this query.

`com.google.android.apps.mediashell` declares `MirroringActivity`, `CastSettingsActivity`, and `MediaShellCastReceiverService`. That is Cast/mirroring evidence. It stays UNKNOWN as a dependency of YouTube, not a candidate.

`com.google.android.katniss` is an updated system app with splits `base.apk`, `split_config.armeabi_v7a.apk`, and `split_config.in.apk`. Earlier classes are under `com.google.android.apps.tvsearch`. Voice dependency is UNKNOWN. It stays review.

`com.google.android.leanbacklauncher` is a system launcher package with no HOME category in the earlier filtered dump. It is not a candidate. Disabling a second system launcher on this ZTE image was not tested.

## Candidates for a later decision

These four meet the candidate rule: the dump shows what they are, they are not a proven dependency of the required apps or of `TunerTvInputService`, and `pm enable --user 0 <package>` is the rollback if a future test disables them. They are not approved.

| Package | Evidence | Risk if later disabled | Rollback |
|---|---|---|---|
| `com.android.htmlviewer` | `HTMLViewerActivity`; path `/system/app/HTMLViewer/HTMLViewer.apk` | Local HTML viewing | `pm enable --user 0 com.android.htmlviewer` |
| `com.android.printspooler` | `PrintActivity`; path `/system/app/PrintSpooler/PrintSpooler.apk`; version 9 | Printing | `pm enable --user 0 com.android.printspooler` |
| `com.google.android.marvin.talkback` | Activities under `com.google.android.accessibility.talkback` | Screen reader stops | `pm enable --user 0 com.google.android.marvin.talkback` |
| `com.google.android.youtube.tvkids` | `com.google.android.apps.youtube.tvkids.activity.MainActivity` | YouTube Kids stops. System YouTube package stays | `pm enable --user 0 com.google.android.youtube.tvkids` |

The exact disable command, if one of these is later approved on its own, would be `pm disable-user --user 0 <package>`. It was not run.

## Still UNKNOWN

The other 31 names in `profiles/zte-b860h-v5/review-packages.txt` stay unknown. That includes camera, CTS shims, Basic Dreams, backdrop, backup transport, feedback, Assistant, recommendations, TTS, tutorials, the PitchBlack camera overlay, and the user leanback apps `iflix.play`, `com.vidio.android.tv`, `com.mibi.tivi`, `com.redstream.app`, `ar.tvplayer.tv`, `com.mxtech.videoplayer.pro`, `com.parzival.equalizerpro`, and `com.yablio.sendfilestotv`. A leanback category or a boot receiver is not a purpose. `iflix.play` still has Tencent classes from the earlier dump, which is not enough to call it safe to remove.

Disabled before this project, and left that way: `com.google.android.tvlauncher`, `com.atvxperience.appdrawer`, `com.yacinenwupdt.v2`.
