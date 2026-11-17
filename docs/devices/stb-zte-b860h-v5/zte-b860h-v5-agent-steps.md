# ZTE B860H V5.0: steps for an agent

Use this with a coding agent that has a shell, on the computer that can reach the STB. Do one step per conversation. The agent shows the exact command, the effect, and the rollback, then waits. A "yes" for one step is not a "yes" for the next.

Nothing in the audit has been applied yet. Home is still ATV Launcher Pro (`ca.dstudio.atvlauncher.pro`). CleanYourTV is not installed, so a theme or a layout push has nowhere to land until step 4 finishes. Read [the session notes](zte-b860h-v5-change-notes.md) and [the launcher plan](zte-b860h-v5-launcher-plan.md) before step 3.

Write `<tv ip>` in chat. Do not put the STB address, a Wi-Fi name, or a serial number into a file that will be committed.

## Rules the agent keeps

- Disable with `pm disable-user --user 0`, one package per call, through `tools\disable.ps1`. No `pm uninstall`, no `pm hide`, no root, no firmware flash.
- Do not pass `protected-packages.txt` or `review-packages.txt` to `disable.ps1`. Those files are comments on purpose. An empty list makes `disable.ps1` stop and send nothing.
- Do not reuse the B866V2K or B866F lists. This box is `B860HV5_Telkom`, Android 9, `V83011303.0016`.
- Do not launch `com.zte.factorytesttool` or any package whose name says factory, even to look. Use `dumpsys package`.
- Leave enabled: the current Home, Netflix (`com.netflix.ninja`), system YouTube (`com.google.android.youtube.tv`), UseeTV (`com.useetv.stb`), Firefox (`org.mozilla.tv.firefox`), Live Channels (`com.google.android.tv`), Settings, Bluetooth, the remote, and every `com.zte.*` / `com.ztestb.*` package. Setup wizard (`com.google.android.tungsten.setupwraith`) had a saved-network `cname` on an earlier read. Do not disable it.
- Do not run `launcher\build-and-install.ps1` on this box. Without `-StockLauncher` it stops, because this Home is not in its known list. With `-StockLauncher` or `-StockLauncher none` it still calls `cmd package set-home-activity` for `com.gathin.tvhome`. Installing the APK is not approval to change Home. Disabling `ca.dstudio.atvlauncher.pro` is not approved.
- Do not run `tools\restore.ps1` until `disable.ps1` has written a record for this unit. A restore with no record aims Home at `com.google.android.tvlauncher`, which is already disabled here and is not the current Home.
- Do not set `FORCE_HOME`, `FORCE_SETUPWRAITH`, `TVINPUT_ALLOW`, or `HANDLERS_ALLOW`.
- Themes and `config.json` change CleanYourTV only. They do not restyle ATV Launcher Pro.

## Step 1 — Prepare the computer

Ask the agent to check, and to stop before installing anything system-wide:

```text
Read PROMPT.md, AGENTS.md, and docs/devices/stb-zte-b860h-v5/zte-b860h-v5-agent-steps.md.
From the repo root, check whether tools\venv\Scripts\python.exe exists.
If it does not, show the commands and wait:
  py -3 -m venv tools\venv
  tools\venv\Scripts\python.exe -m pip install -r tools\requirements.txt
Check JAVA_HOME, java on PATH, and %LOCALAPPDATA%\Android\Sdk.
Report what is missing for a launcher build (JDK 17, Android SDK platform 34, build-tools 34.0.0).
Do not install a JDK or an SDK, and do not touch the STB.
```

The venv is required before any later step that talks to the STB. The JDK and SDK are required only for step 4.

## Step 2 — Connect and confirm the box

```text
Connect to the ZTE B860H V5.0 over ADB. I will give you <tv ip> in chat. Port 5555.
Tell me when the Allow USB debugging prompt will appear.
Read-only checks only:
  getprop ro.product.model
  getprop ro.product.device
  getprop ro.build.version.incremental
  cmd package resolve-activity --brief -a android.intent.action.MAIN -c android.intent.category.HOME
Stop if the model is not B860H V5.0 or the device is not B860HV5_Telkom.
Confirm Home is still ca.dstudio.atvlauncher.pro/...LauncherActivity.
Do not disable, install, reboot, or change settings.
```

Expected Home component: `ca.dstudio.atvlauncher.pro/ca.dstudio.atvlauncher.screens.launcher.LauncherActivity`.

## Step 3 — Debloat, one approved package at a time

Candidates only, not approved: `com.android.htmlviewer`, `com.android.printspooler`, `com.google.android.marvin.talkback`, `com.google.android.youtube.tvkids`. The other 31 names in `profiles/zte-b860h-v5/review-packages.txt` stay unknown.

```text
I approve disabling this one package, and no other: <package>.
Read its row in profiles/zte-b860h-v5/package-inventory.json and
docs/devices/stb-zte-b860h-v5/zte-b860h-v5-package-verification.md.
If it is not one of the four candidates, stop.
Show the disable command and this rollback, then run only that disable:
  pm enable --user 0 <package>
Use tools\disable.ps1 with a list that contains that single uncommented name.
Do not pass protected-packages.txt, review-packages.txt, or an empty approved-disable.txt.
After it returns, check pm list packages -d, then ask me to try the remote, Wi-Fi,
and whichever of Netflix, YouTube, UseeTV, Firefox, and Live TV I can open.
Append the result to profiles/stb-zte-b860h-v5/debloat-changelog.md.
If any check fails, run the rollback before anything else.
Do not continue to a second package in this turn.
```

`disable.ps1` writes the recovery record that a later `restore.ps1` needs. Wait about 15 seconds after the last disable in a session before any reboot, so Android can flush package state.

## Step 4 — Install CleanYourTV beside the current Home

This step needs the JDK and SDK from step 1, and a separate approval. The agent builds the APK, installs it as an extra app, and opens it by its component. It does not change the default Home and does not disable ATV Launcher Pro.

```text
Build launcher\app as release. Do not run launcher\build-and-install.ps1.
Report package id, version, minSdk, targetSdk, APK size, and path.
Stop and show the install command. After I approve that command only:
  pm install -r of the release APK
  am start -n com.gathin.tvhome/.MainActivity
Leave ca.dstudio.atvlauncher.pro enabled.
Do not call cmd package set-home-activity.
Do not pm disable-user the vendor launcher.
Ask me to move with the D-pad from every edge, then press Back and Home.
Home must still open ATV Launcher Pro. If the new app will not start, uninstall is not the next step: leave it installed, say what failed, and stop.
```

Rollback for the install, if you want the package gone: `pm uninstall com.gathin.tvhome`. That removes the sideloaded launcher only. It is not a substitute for putting Home back, because Home was not moved.

## Step 5 — Custom layout

Layout keys live in a theme `config.json`. They apply the next time CleanYourTV starts. On-screen column choices win until the next theme push or Follow theme. Edit a copy outside this checkout. `tools\manage.py set` refuses a folder under `themes\` or `examples\`.

Useful keys for this box, from `themes/README.md`:

| Key | What to set |
|---|---|
| `dock_apps` | Packages that should sit on the dock, in order. Names that are not installed are skipped |
| `columns` | Tiles per row, 1 to 8. `themes/six-columns` is the six-column example |
| `rows_below_dock` | Grid rows left visible under the dock, 0 to 3 |
| `hidden_apps` | Packages that must not appear as tiles |
| `reduced_motion` | `true` keeps animation short on a weak GPU |
| `icon_only_buttons` | Wi-Fi and Settings as round icons |
| `background_color` | `#RRGGBB` behind the tiles |

A dock that matches the apps this owner asked to keep:

```json
{
  "dock_apps": [
    "com.netflix.ninja",
    "com.google.android.youtube.tv",
    "com.useetv.stb",
    "org.mozilla.tv.firefox",
    "com.google.android.tv"
  ],
  "columns": 5,
  "rows_below_dock": 1,
  "reduced_motion": true,
  "icon_only_buttons": true,
  "background_color": "#000000"
}
```

```text
CleanYourTV is installed and is not the default Home.
Copy themes\minimal to $env:USERPROFILE\cleanyourtv-b860h (outside this repo).
Replace its config.json with the dock and column values I paste.
Do not edit launcher\app\src\main\res\values\config.xml.
Do not add a folder under themes\.
Show the file, then push it and wait for my approval:
  powershell -ExecutionPolicy Bypass -File tools\theme.ps1 -TvHost <tv ip> $env:USERPROFILE\cleanyourtv-b860h
Open com.gathin.tvhome/.MainActivity and take a screenshot with tools\tv.py --screenshot.
Do not change the default Home.
```

Changing `config.xml` instead would need a rebuild and would change the default for every later build. A private theme does not.

## Step 6 — Switch to a theme that already ships

Same requirement: CleanYourTV is installed. Pick a folder that already has `config.json` and `screenshot.jpg`. `themes/README.md` is the catalogue. `minimal`, `six-columns`, and `aurora` are quiet starting points. A theme with a festive skin (`diwali`, `halloween`, `dussehra`) turns itself off after the date in its config.

```text
Push this existing theme and no other change:
  powershell -ExecutionPolicy Bypass -File tools\theme.ps1 -TvHost <tv ip> themes\<name>
Restart is part of that script. Open the launcher, screenshot Home, and stop.
To go back to the built-in config.xml:
  powershell -ExecutionPolicy Bypass -File tools\theme.ps1 -TvHost <tv ip> -Reset
Do not pass a theme folder and -Reset in the same command. The script refuses that and sends nothing.
Do not change the default Home.
```

A push replaces the theme already on the STB and clears pictures the previous push put there.

## Step 7 — Extra apps

The list is [zte-b860h-v5-app-recommendations.md](zte-b860h-v5-app-recommendations.md). None of those apps are installed. Install one, from the developer or from F-Droid, then test it before the next. DopamineTV and the local BitTV APK stay out: source, signature, and permissions are not verified.

```text
I approve installing this one APK: <path or official URL>.
Check minSdk, ABI armeabi-v7a or arm64-v8a, and the signing certificate.
Show the pm install command and wait.
Do not disable Play Protect. Do not disable any package. Do not change Home.
```

## Step 8 — Make CleanYourTV the default Home

Not approved. Do not start this step because an earlier step succeeded.

Before it can even be proposed, all of these have to be true: the APK is installed, D-pad and Back work inside it, ATV Launcher Pro is still enabled, and you have pressed Home on the real remote and seen ATV Launcher Pro. The way back, untested, is:

```text
cmd package set-home-activity --user 0 ca.dstudio.atvlauncher.pro/ca.dstudio.atvlauncher.screens.launcher.LauncherActivity
```

Android 9 needs that component, not only the package name. The vendor package has to still be enabled. Do not disable it in the same pass as the Home switch, and do not enable a remote-key helper in that same pass.

## Step 9 — Close ADB

When the session is over, ask the agent to turn Wireless debugging and Network/ADB debugging off, and USB debugging too unless you want it left on. Then check with `tools\adb-closed.py`. Only that tool reporting the port closed counts. Reboot only after you say so, and only after package state has had time to flush.

## What the agent writes down

After any step that changed the STB, update [zte-b860h-v5-change-notes.md](zte-b860h-v5-change-notes.md): what ran, what stayed, and what you still have not tested. A disable also gets a line in `profiles/stb-zte-b860h-v5/debloat-changelog.md` with the command output and the rollback.
