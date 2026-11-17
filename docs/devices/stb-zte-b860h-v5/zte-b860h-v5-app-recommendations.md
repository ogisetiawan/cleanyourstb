# App recommendations for the ZTE B860H V5.0

Device: ZTE B860H V5.0, Android 9 (SDK 28). Current Home is ATV Launcher Pro (`ca.dstudio.atvlauncher.pro`).

**None of the apps in this document were installed on the STB.** This is a list to test later, one app at a time, without changing Home and without disabling the current launcher. Android 9 does not mean every APK release will install. Before an install, check `minSdkVersion`, ABI (`armeabi-v7a` or `arm64-v8a`), signature, permissions, and remote navigation. Use a release from the developer or from a trusted repository.

## 1. Utilities

| App | Use on the STB | Note |
|---|---|---|
| [Material Files](https://github.com/zhanghai/MaterialFiles) | Folders, downloads, APKs, USB | Pick a release that supports Android 9 and a remote |
| [X-plore](https://www.lonelycatgames.com/apps/xplore) | Local files, USB, FTP, SMB, and a Google Drive trial | Drive access may need extra authentication |
| [Amaze](https://github.com/TeamAmaze/AmazeFileManager) | Another way to manage APKs and archives | Check the Android requirement of the chosen release |
| [Termux](https://github.com/termux/termux-app) | Network tools and scripts | A terminal is a poor fit for a TV remote |
| [Obtainium](https://github.com/ImranR98/Obtainium) | Watch official releases | Easier to configure from a phone |
| [Neo Store](https://github.com/NeoApplications/Neo-Store) | F-Droid catalogue | Remote navigation may be awkward |

## 2. Networking, casting, and transfer

| App | Use on the STB | Note |
|---|---|---|
| [FCast Receiver](https://fcast.org/) | Receive media or a screen from an FCast sender | Not Google Cast or Miracast. Needs a test on this firmware |
| [FCast Desktop Sender](https://github.com/futo-org/fcast) | Send from a laptop | The desktop features may still be beta |
| [CastLab](https://f-droid.org/packages/app.fedilab.castlab/) | Send from a phone | The STB still needs a matching receiver |
| [LocalSend](https://localsend.org/) | Send APKs, video, and documents over the LAN | Not a player and not screen mirroring |
| [Primitive FTPd](https://f-droid.org/packages/org.primftpd/) | Use the STB as an FTP/SFTP target | Keep it on the LAN and use authentication |
| [Tailscale](https://tailscale.com/) | Reach home devices from outside the LAN | Not a casting app. Check ABI and Android 9 |
| [TV Bro](https://github.com/truefedex/tv-bro) | A browser aimed at a TV remote | Check that the latest release still supports Android 9 |

## 3. Players and media centers

| App | Use on the STB | Note |
|---|---|---|
| [VLC](https://www.videolan.org/vlc/download-android.html) | Video from USB, local storage, and SMB | First choice for a film folder on Windows |
| [Kodi](https://kodi.tv/download/android/) | An SMB/UPnP library and subtitles | Needs initial setup. Add-ons only from a trusted source |
| [Jellyfin Android TV](https://jellyfin.org/downloads/clients/) | Watch a library on a Jellyfin server | The server runs on a PC or NAS, not on the STB |
| [SmartTube](https://github.com/yuliskov/SmartTube) | A YouTube client for TV | Not an official Google app. `com.teamsmart.videomanager.tv` is already on the STB and was not tested |
| [Aves](https://github.com/deckerst/aves) | A local photo and video gallery | Aimed at a touch screen. Test the remote first |

## 4. A film folder on Windows 11

The simplest path is Windows SMB plus VLC. No extra media server is required.

1. Put the laptop and the STB on the same home network.
2. Set that home network's Windows profile to **Private**.
3. Turn on Network discovery and File and printer sharing for the Private profile.
4. Share the film folder, for example `D:\Film`, as the share name `Film`, with **Read** permission.
5. Leave password-protected sharing on. Use a Windows account that has a password.
6. Note the laptop's IPv4 address from `ipconfig`.
7. In VLC on the STB, open SMB and enter the laptop IP, the share name, and the Windows credentials.

Do not turn on SMB 1.0. Use SMB 2/3. The laptop has to stay on and on the network while a file is playing. The laptop IP is not recorded in this repository.

## 5. Google Drive

X-plore can add Google Drive through Web Storage. Access to the whole Drive may need OAuth, as the developer documents it. Once connected, open a file with a compatible player. Direct playback depends on the format, the permission, and the connection. Not every Drive file plays without a temporary download.

Documentation: [X-plore](https://www.lonelycatgames.com/apps/xplore) and the [Google Drive guide](https://www.lonelycatgames.com/docs/xplore/google-drive).

## 6. Media servers

| Tool | Where it runs | Note |
|---|---|---|
| [Jellyfin Server](https://jellyfin.org/downloads/server/) | PC or NAS | The STB only gets the client |
| [rclone](https://rclone.org/drive/) | PC | A Google Drive bridge. The PC has to be on while files are read |
| [Termux](https://github.com/termux/termux-app) | The STB, if it is tested | A 24-hour server depends on Android's background-process rules |

## 7. Launchers

| App | Use | Note |
|---|---|---|
| [Projectivy](https://github.com/spocky/miproja1) | A different Home | Test the remote and Android 9 before making it Home |
| [FLauncher](https://github.com/FLauncher/flauncher) | A minimal Home | Check `minSdk` on the chosen release |
| ATV Launcher Pro | The Home in use now | Leave it enabled while new apps are tested |
| CleanYourTV | The launcher in this repository | Not built and not installed. See the [launcher plan](zte-b860h-v5-launcher-plan.md) |

## 8. Test order, if a later install is approved

| Order | App | Reason |
|---|---|---|
| 1 | VLC | Local files, USB, and an SMB folder |
| 2 | X-plore | Files, and a Google Drive trial |
| 3 | TV Bro | A browser for the remote |
| 4 | FCast Receiver | Test receiving a cast from another device |
| 5 | LocalSend | Transfer from a phone or laptop |
| 6 | Kodi | A film library, if VLC is not enough |
| 7 | Jellyfin Android TV | Client for a server on a PC or NAS |
| 8 | Projectivy or FLauncher | A Home experiment after the rest is stable |
| 9 | Termux or Primitive FTPd | Further networking |

Install and test one app at a time. Do not change the default Home in the same step.

## 9. DopamineTV and BitTV

Both were considered. The APK source, developer identity, signature, and permissions are not verified.

- They are not on the install list above.
- Do not download an APK from an unclear mirror, and do not turn off Play Protect to force an install.
- The local copy `APK/BitTV-v2.1.8-agate.apk` is not committed and was not installed.

## 10. Safety limits

1. Download from the developer site or from F-Droid.
2. Match the APK ABI to the STB CPU.
3. Check `minSdk` before installing.
4. Do not disable a service whose purpose is still unknown. The approved-disable list is still empty.
5. Do not turn on SMB 1.0.
6. Do not open ADB, FTP, or SMB to the internet.
7. Record a version that is known to work before adding the next app.
8. Keep ATV Launcher Pro as the way back while experimenting.
