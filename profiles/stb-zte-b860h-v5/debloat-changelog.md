# Debloat change log

Session notes (debloat, installs, layout): [zte-b860h-v5-change-notes.md](../../docs/devices/stb-zte-b860h-v5/zte-b860h-v5-change-notes.md).

No device-changing command was run.

`profiles/zte-b860h-v5/approved-disable.txt` contains no package names. Phase 4 allows disable only for a name on that list, and only after a separate approval for that one package. Running the phase is not that approval.

Baseline at the stop:

- Home: `ca.dstudio.atvlauncher.pro/ca.dstudio.atvlauncher.screens.launcher.LauncherActivity`
- Already disabled, and left disabled: `com.google.android.tvlauncher`, `com.atvxperience.appdrawer`, `com.yacinenwupdt.v2`
- Packages disabled by this project: none

Four names are candidates only. They are not approved, and these commands were not sent:

| Package | Command that was not run | Rollback that was not needed |
|---|---|---|
| `com.android.htmlviewer` | `pm disable-user --user 0 com.android.htmlviewer` | `pm enable --user 0 com.android.htmlviewer` |
| `com.android.printspooler` | `pm disable-user --user 0 com.android.printspooler` | `pm enable --user 0 com.android.printspooler` |
| `com.google.android.marvin.talkback` | `pm disable-user --user 0 com.google.android.marvin.talkback` | `pm enable --user 0 com.google.android.marvin.talkback` |
| `com.google.android.youtube.tvkids` | `pm disable-user --user 0 com.google.android.youtube.tvkids` | `pm enable --user 0 com.google.android.youtube.tvkids` |

Required-app playback, remote, Wi-Fi, HDMI, and IPTV checks were not run, because no package was changed. They stay NOT TESTED.
