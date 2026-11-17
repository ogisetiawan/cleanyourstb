# ZTE B860H V5.0 recovery plan

This is a configuration record plus copies of selected APKs. It is not a full firmware backup, not an app-data backup, and it was not rehearsed by restoring onto the live STB.

The current Home is still `ca.dstudio.atvlauncher.pro/ca.dstudio.atvlauncher.screens.launcher.LauncherActivity`. Nothing in this project has disabled a package or changed Home, so the working route back is the box as it is now. ADB shell commands succeeded with `ro.adb.secure=1`, so authorization was complete at the time of the copy. After a reboot, that authorization is not guaranteed. Reboot was not performed.

## What was saved

| Layer | Where | What it can do |
|---|---|---|
| File record | `backup/zte-b860h-v5/state/packages-f.txt` (127 lines), `packages-d.txt`, `home.txt` | Shows the package paths, the three already-disabled packages, and the Home component. Hashes are in `profiles/zte-b860h-v5/backup-manifest.json` |
| APK copies | `backup/zte-b860h-v5/apks/` | Bytes of the current Home, Netflix, system YouTube, UseeTV (base plus both splits), Firefox, Live Channels, the disabled Google TV launcher, `StbDashWithBlink.apk`, TV Settings, and the SmartTube client. Each file is non-empty and has a SHA-256 |
| Inventory | `profiles/zte-b860h-v5/package-inventory.json` | Classification and evidence. Not a substitute for the package list |
| Recovery record used by `restore.ps1` | none | `disable.ps1` has not run, so `~/.cleanyourtv` has no record for this box |

UseeTV is a split app. All three paths from `pm path` were copied. Copying only `base.apk` would have been an incomplete APK. The other copied packages returned one path each. Certificate hashes were not re-parsed from the copied files. App data, accounts, DRM, and IndiHome provisioning were not copied. The other installed APKs were not copied. Partitions were not imaged.

`backup/` is gitignored. The manifest in `profiles/` is the committed index.

## What `restore.ps1` would do

`tools/recovery.py` stores, when `disable.ps1` actually runs, the enabled/disabled/absent state of each listed package, the Home component, and three animation scales. Replay re-enables only names that were enabled at save time, leaves names that were already disabled, and returns Home to the recorded component when that component is not `com.gathin.tvhome`.

The stock-launcher fallback is `com.google.android.tvlauncher` unless the caller passes another package. On this box that package is already disabled, and it is not the current Home. A restore with no record would enable every name in the list it is given and aim Home at that Google launcher. Do not run that fallback here.

`pm enable --user 0 <package>` is the rollback for a later `pm disable-user --user 0 <package>`. It needs ADB. It does not restore app data that a disable did not delete. Disable does not uninstall. This was not executed.

If a replacement launcher were later set as Home and then crashed, the untested route back would be: keep `ca.dstudio.atvlauncher.pro` enabled, then `cmd package set-home-activity --user 0 ca.dstudio.atvlauncher.pro/ca.dstudio.atvlauncher.screens.launcher.LauncherActivity`. Android 9 needs the component, not only the package. `build-and-install.ps1` will not do that on its own: this Home package is not in its known-stock list, and `-StockLauncher none` still calls `set-home-activity` for `com.gathin.tvhome`. Neither was run.

## Full firmware recovery

Not available from these files. There is no partition image, no firmware package, and no tested factory-reset procedure. Factory reset is not a recovery step for this project. `com.android.tv.settings` holds `MASTER_CLEAR` and a `ZteKeyInterceptor` receiver that was not started.

## Script changes not made

- No-record restore should refuse to assume `com.google.android.tvlauncher` when that package is disabled or is not the resolved Home.
- `ca.dstudio.atvlauncher.pro` should not be added to the installer's known-stock list, because that list is what the installer disables.
- The Home guard's fail-closed name list does not include this vendor launcher or `com.woono.util.whomeselector`.
