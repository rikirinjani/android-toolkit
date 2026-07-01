---
name: android-toolkit
description: >-
  All-in-one Android phone toolkit over ADB. Inspect storage, debloat, battery doctor, crash
  analyzer, automation, system tuner, and network security check. Universal across brands.
  Requires USB debugging enabled and device connected.
---

# Android Toolkit — ADB-Powered Phone Utility

A comprehensive Android phone utility skill for AI coding agents. Works across all brands
(Xiaomi, Samsung, OPPO, vivo, Nothing, Pixel, etc.) using plain ADB commands.

## Prerequisites

- ADB installed (`adb --version`)
- Device connected with USB Debugging ON
- `adb devices` shows `device`

## Quick Start

```powershell
adb devices                          # verify connection
adb shell df -h /sdcard              # storage overview
adb shell getprop ro.product.manufacturer  # detect brand
```

---

# Module 1: Storage Inspection

### Overall usage
```powershell
adb shell df -h /sdcard
```

### Top-level folder sizes
```powershell
adb shell du -h -d 1 /sdcard
```

### Media apps (WhatsApp, Telegram, etc.)
```powershell
adb shell du -h -d 1 /sdcard/Android/media
adb shell du -h -d 1 /sdcard/Android/media/com.whatsapp/WhatsApp
adb shell du -h -d 1 /sdcard/Android/media/com.whatsapp/WhatsApp/Media
```

### App data hogs
```powershell
adb shell du -h -d 1 /sdcard/Android/data
```

### Camera, pictures, downloads
```powershell
adb shell du -h -d 1 /sdcard/DCIM
adb shell du -h -d 1 /sdcard/Pictures
adb shell du -h -d 1 /sdcard/Download
```

### SD Maid SE feature equivalents

| SD Maid Feature | ADB Equivalent |
|---|---|
| File Analysis | `du -h -d 1` per folder |
| Largest Files | `adb shell find /sdcard -type f -exec ls -lh {} \; \| sort -k5 -rh \| head -20` |
| Corpse Finder | cross-ref `ls /sdcard/Android/data` with `pm list packages` |
| App Cache Cleaner | `adb shell pm trim-caches 999G` |
| Empty Folder Finder | `adb shell find /sdcard -type d -empty` |
| WhatsApp Media Sizer | per-type breakdown |
| WhatsApp DB Cleanup | identify old Databases backups, keep latest |
| Duplicate Finder | md5sum group-by-hash across DCIM/WhatsApp/Pictures |

### Cleanup actions

WhatsApp DB (safe, regenerated):
```powershell
adb shell ls -lh /sdcard/Android/media/com.whatsapp/WhatsApp/Databases/
adb shell rm /sdcard/Android/media/com.whatsapp/WhatsApp/Databases/msgstore-OLD.db.crypt14
```

WhatsApp Images (backup first):
```powershell
adb pull "/sdcard/Android/media/com.whatsapp/WhatsApp/Media/WhatsApp Images" C:\backup
adb shell rm -rf "/sdcard/Android/media/com.whatsapp/WhatsApp/Media/WhatsApp Images"
```

Camera roll (backup first):
```powershell
adb pull /sdcard/DCIM/Camera C:\backup\DCIM
adb shell rm -rf /sdcard/DCIM/Camera
```

System caches:
```powershell
adb shell pm trim-caches 999G
```

Empty folders:
```powershell
adb shell find /sdcard -type d -empty -delete
```

---

# Module 2: Debloat

## Architecture

```
 Detect brand (getprop)
       |
       v
 Apply brand-specific bloat list + Universal 3rd-party bloat - Universal danger list
       |
       v
 Present categorized list -> user confirms -> pm uninstall/disable
```

### 1. Detect Brand

```powershell
adb shell getprop ro.product.manufacturer   # Xiaomi, samsung, OPPO...
adb shell getprop ro.build.version.release  # Android version
adb shell pm list packages
```

### 2. Universal 3rd-Party Bloat (safe on any phone)

```
com.facebook.katana              - Facebook
com.facebook.appmanager          - FB app manager
com.facebook.system              - FB system
com.facebook.services            - FB services
com.netflix.mediaclient          - Netflix
com.amazon.appmanager            - Amazon
com.reddit.frontpage             - Reddit
com.spotify.music                - Spotify
com.twitter.android              - Twitter/X
com.instagram.android            - Instagram
com.instagram.barcelona          - Threads
com.ss.android.ugc.trill         - TikTok
com.google.ar.lens               - Google Lens
com.google.android.apps.magazines - Google News
com.google.android.apps.wellbeing - Digital Wellbeing
com.google.android.apps.googleassistant - Google Assistant
com.adobe.spark.post             - Adobe Express
```

### 3. Universal Danger List (NEVER remove)

```
com.android.launcher*        - Launcher
com.android.phone            - Phone app
com.android.settings         - Settings
com.android.systemui         - System UI
com.android.providers.contacts - Contacts
com.android.providers.telephony - Telephony
com.android.providers.media  - Media provider
com.android.vending          - Play Store
com.google.android.gms       - Play Services
com.google.android.gsf       - Services Framework
```

### 4. Brand-Specific Lists

**MIUI / Xiaomi (tested on Android 13, Redmi Note 11)**

```
com.miui.analytics              - Telemetry
com.miui.bugreport              - Bug reports
com.miui.msa.global             - MSA (ads engine)
com.miui.yellowpage             - Yellow Pages
com.miui.videoplayer            - Mi Video
com.miui.player                 - Mi Music
com.miui.fm                     - FM Radio
com.miui.screenrecorder         - Screen Recorder
com.miui.weather2               - Weather
com.miui.touchassistant         - Touch Assistant
com.miui.audiomonitor           - Audio Monitor
com.miui.cleaner                - Cleaner
com.miui.powerkeeper            - Power Keeper
com.miui.phrase                 - Phrase
com.miui.aod                    - Always-On Display
com.miui.miservice              - Mi Service
com.miui.freeform               - Freeform windows
com.miui.mishare.connectivity   - Mi Share
com.xiaomi.midrop               - Mi Drop
com.miui.backup                 - Backup
com.miui.micloudsync            - Mi Cloud Sync
com.xiaomi.payment              - Mi Pay
com.miui.notification           - Notification
```

**Samsung One UI**

```
com.samsung.android.bixby.wakeup  - Bixby wakeup
com.samsung.android.bixby.agent   - Bixby agent
com.samsung.android.bixbyvision.framework
com.samsung.android.visionintelligence
com.samsung.android.app.spage     - Samsung Free
com.samsung.android.app.social    - Samsung Social
com.samsung.android.game.gametools
com.samsung.android.game.gos      - Game Optimizing Service
com.samsung.android.oneconnect    - SmartThings
com.samsung.android.app.notes     - Samsung Notes
com.samsung.android.calendar      - Samsung Calendar
com.samsung.android.forest        - Digital Wellbeing
com.samsung.kidsmode              - Kids Mode
com.sec.android.app.samsungapps   - Galaxy Store
```

**ColorOS / OPPO / Realme / OnePlus**

```
com.heytap.market                 - App Market
com.heytap.browser                - Browser
com.heytap.cloud                  - HeyTap Cloud
com.coloros.filemanager           - File Manager
com.coloros.gamespace             - Game Space
com.coloros.weather               - Weather
com.coloros.video                 - Video
com.coloros.music                 - Music
com.coloros.assistantscreen       - Smart Sidebar
com.opera.browser                 - Opera
```

**vivo / Funtouch OS**

```
com.vivo.weather                  - Weather
com.vivo.video                    - Video
com.vivo.music                    - Music
com.vivo.game                     - Game Center
com.vivo.vivokaraoke              - Karaoke
com.vivo.easyshare                - EasyShare
com.vivo.assistant                - Jovi assistant
com.vivo.browser                  - Browser
com.vivo.appstore                 - App Store
```

**Nothing OS**

```
com.nothing.weather               - Nothing Weather
com.nothing.recorder              - Nothing Recorder
```

### 5. Commands

```powershell
adb shell pm uninstall -k --user 0 <package.name>       # uninstall
adb shell pm disable-user --user 0 <package.name>        # disable (fallback)
adb shell pm enable <package.name>                      # re-enable
```

### 6. Android Version Notes

| Feature | Min Android |
|---|---|
| `pm uninstall -k --user 0` | 4.0 |
| `pm disable-user --user 0` | 5.0 |
| WhatsApp at `Android/media/` | 11+ (pre-11: `/sdcard/WhatsApp/`) |
| `pm trim-caches` | 7.0+ |
| Scoped storage restricts `ls` on `Android/data/` | 11+ |

**Gotchas:** Android 14+ restricts uninstall more. MIUI 14+ some packages fail with
`Failure [-1000]` but accept `disable-user`. Samsung One UI 6+ Bixby packages may be
protected.

---

# Module 3: Battery Doctor

### Stats

```powershell
adb shell dumpsys battery                              # level, temp, voltage, health
adb shell dumpsys batterystats --charged                # per-app drain
adb shell dumpsys batterystats --wakelocks              # what keeps phone awake
adb shell dumpsys power                                 # doze state, screen-on
adb shell dumpsys deviceidle                            # deep sleep stats
```

### Live data example

Battery: level, temperature (convert /10 = deg C), voltage, technology, health (2=good).
Doze: light-idle / deep-idle transitions. Wakelocks: app names + duration.

### Top drainers to check

- Apps with wakelock >10s background partial
- Apps killed for CPU use
- Apps with frequent alarms firing
- High mobile radio active percentage

### Recommendations

- Restrict background activity for high-drain apps
- Disable unused system apps with wakelocks
- Reduce screen brightness, turn off always-on display
- If device never deep-idles, check for misbehaving apps
- WiFi scan interval can be tuned (see Module 6)

---

# Module 4: Crash Analyzer

### When phone is alive but has issues

```powershell
adb shell logcat -b crash -d                          # recent Java crashes
adb shell logcat -b system -d | grep -i fatal         # system-level fatal errors
adb shell getprop ro.boot.reboot_reason                # last reboot cause
adb shell dumpsys dropbox --times=5                    # last 5 crash dropbox entries
adb shell dmesg | grep -i panic                        # kernel panics
```

### When phone is partially dead

| State | Can ADB help? |
|---|---|
| Bootloop | Yes - `logcat -b crash` during boot |
| Soft brick (recovery) | Yes - `adb shell` works in recovery |
| Black screen but powered | Yes - `bugreport`, `logcat -b all` |
| Random reboots | Yes - `batterystats` shows reboot reason |
| Hard brick (no power) | No - needs EDL/ISP flash |

### Commands

```powershell
adb bugreport C:\bugreport.zip                         # full dump (large)
adb shell dumpsys dropbox --times=5                    # recent crash snippets
adb shell logcat -b crash -d | tail -50                # last 50 crash lines
```

---

# Module 5: Automation

### Input simulation

```powershell
adb shell wm size                                      # get screen dimensions
adb shell input tap X Y                                # tap at coordinates
adb shell input swipe X1 Y1 X2 Y2                      # swipe
adb shell input text "string"                          # type text
adb shell input keyevent KEYCODE_HOME                  # home button
adb shell input keyevent KEYCODE_APP_SWITCH            # recent apps
adb shell input keyevent KEYCODE_BACK                  # back
adb shell input keyevent KEYCODE_POWER                 # power button
adb shell input keyevent KEYCODE_VOLUME_DOWN           # volume down
adb shell input keyevent KEYCODE_CAMERA                # camera
```

### Launch activities

```powershell
adb shell am start -a android.intent.action.VIEW -d https://example.com
adb shell am start -a android.intent.action.CALL -d tel:123456789
adb shell am start -a android.intent.action.MAIN -n com.whatsapp/.Main
adb shell am start -a android.settings.APPLICATION_DETAILS_SETTINGS -d package:com.example
```

### Practical use cases

- Accept EULA on first boot: find button coordinates + tap
- Auto-swipe through onboarding screens
- Bulk dismiss notifications: swipe each away
- Screen recording with automated taps for bug reproduction
- Open wifi/camera/flashlight/dnd with one command
- Script app-specific workflows (login, fill form, etc.)

---

# Module 6: System Tuner

### Read/write system settings

```powershell
adb shell settings list global                         # all global settings
adb shell settings list system                          # system settings
adb shell settings list secure                          # secure settings
adb shell settings put global <key> <value>            # modify
adb shell settings delete global <key>                 # reset to default
```

### Safe performance presets

```powershell
# Performance mode - zero animations, faster UI
adb shell settings put global window_animation_scale 0.0
adb shell settings put global transition_animation_scale 0.0
adb shell settings put global animator_duration_scale 0.0

# Battery saver - reduce background scans
adb shell settings put global wifi_scan_interval 300
adb shell settings put global bluetooth_scan_interval 300
adb shell settings put global location_mode 2
adb shell settings put global adaptive_battery_management 1

# Restore defaults
adb shell settings delete global window_animation_scale
adb shell settings delete global transition_animation_scale
adb shell settings delete global animator_duration_scale
adb shell settings delete global wifi_scan_interval
```

### Useful hidden toggles

```powershell
adb shell settings put global tether_dun_required 0
adb shell settings put global wifi_on 0/1
adb shell settings put global bluetooth_on 0/1
adb shell settings put global stay_on_while_plugged_in 0
```

---

# Module 7: Network Security

### Security checks

```powershell
adb shell settings list global | findstr -i adb
adb shell settings list secure | findstr install
adb shell getprop ro.boot.bootloader
adb shell getprop ro.boot.verifiedbootstate
adb shell dumpsys wifi
adb shell dumpsys connectivity
adb shell dumpsys netstats
adb shell cm list users
```

### Risk indicators

| Check | What it means |
|---|---|
| ADB over TCP enabled | Phone can be controlled from any network - HIGH risk |
| install_non_market_apps=1 | Unknown sources enabled - MEDIUM risk |
| Bootloader unlocked | Easier for malware to gain root |
| Verified boot: red | Device tampered - HIGH risk |
| Verified boot: yellow | Bootloader unlocked - MEDIUM risk |
| Verified boot: green | Secure |
| Unknown VPN profiles | Possible traffic interception |
| High outbound data from unknown app | Possible data exfiltration |

### Scorecard output format

```
[PASS] ADB over TCP: disabled
[PASS] Bootloader: locked
[PASS] Verified boot: green
[FAIL] Unknown sources: enabled
[PASS] No guest users
[INFO] VPN: none
[PASS] No suspicious data spikes
```

---

# Appendix: What's NOT Included

| Feature | Reason excluded |
|---|---|
| App management (install/uninstall APKs) | Adequate via storage + debloat modules |
| Screen capture / recording | Better done with native tools on phone |
| Full data backup (adb backup) | Unreliable on Android 11+ (Google deprecated) |
| Bulk permission manager | Too dangerous; revoking wrong permission breaks apps |
| APK extraction | Limited usefulness; already covered by `pm path` |
| Custom ROM / OS upgrade/downgrade | Requires fastboot + unlocked bootloader, not ADB.
                                          Anti-rollback can permanently brick device. |


# Safety Rules

1. Always backup before delete - adb pull to PC first
2. WhatsApp Databases - keep the most recent, delete older
3. Games - ask user before touching
4. System apps - never touch (see Universal Danger List)
5. Settings changes - note the original value before modifying
6. Disable instead of uninstall if unsure
7. Verify - adb shell df -h /sdcard after each action

# Verdict

```powershell
adb shell df -h /sdcard
```
