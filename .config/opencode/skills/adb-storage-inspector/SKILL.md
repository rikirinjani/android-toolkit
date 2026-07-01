---
name: adb-storage-inspector
description: Inspect Android phone storage over ADB. Lists storage hogs, analyzes WhatsApp/DCIM/app data, identifies cleanup opportunities, executes safe deletes, and debloats pre-installed apps. Inspired by SD Maid SE. Universal across brands. Requires USB debugging enabled and device connected.
---

# ADB Storage Inspector

Inspect, clean, and debloat Android phones over ADB. Works across all brands.

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

# PART 1: Storage Analysis

## Overall Usage
```powershell
adb shell df -h /sdcard
```

## Top-Level Folder Sizes
```powershell
adb shell du -h -d 1 /sdcard
```

## Android Media (WhatsApp, Telegram, etc.)
```powershell
adb shell du -h -d 1 /sdcard/Android/media
adb shell du -h -d 1 /sdcard/Android/media/com.whatsapp/WhatsApp
adb shell du -h -d 1 /sdcard/Android/media/com.whatsapp/WhatsApp/Media
```

## App Data Hogs
```powershell
adb shell du -h -d 1 /sdcard/Android/data
```

## Camera & Media
```powershell
adb shell du -h -d 1 /sdcard/DCIM
adb shell du -h -d 1 /sdcard/Pictures
adb shell du -h -d 1 /sdcard/Download
```

## SD Maid SE Feature Equivalents

| SD Maid Feature | ADB Equivalent |
|---|---|
| File Analysis | `du -h -d 1` per folder |
| Largest Files | `find /sdcard -type f -exec ls -lh {} \; \| sort -k5 -rh \| head -20` |
| Corpse Finder | `ls /sdcard/Android/data` cross-ref with `pm list packages` |
| App Cache Cleaner | `adb shell pm trim-caches 999G` |
| Empty Folder Finder | `find /sdcard -type d -empty` |
| WhatsApp Media Sizer | per-type breakdown |
| WhatsApp DB Cleanup | identify old `Databases/` backups, keep latest |
| Duplicate Finder | `md5sum` group-by-hash across DCIM/WhatsApp/Pictures |

---

# PART 2: Debloat (Universal)

## Architecture

```
 Detect brand (getprop)
       |
       v
 Apply brand-specific bloat list (MIUI / One UI / ColorOS / etc.)
       + Universal 3rd-party bloat (Facebook, Netflix, Amazon...)
       - Universal danger list (launcher, phone, SMS...)
       |
       v
 Present categorized list -> user confirms -> pm uninstall/disable
```

## 1. Detect Brand

```powershell
adb shell getprop ro.product.manufacturer  # Xiaomi, samsung, OPPO, vivo, Google...
adb shell getprop ro.build.version.release  # Android version
```

## 2. List All Packages

```powershell
adb shell pm list packages
```

## 3. Universal 3rd-Party Bloat (safe across all brands)

These are pre-installed by carriers/manufacturers and safe to remove on any phone:

```
com.facebook.katana          - Facebook
com.facebook.appmanager
com.facebook.system
com.facebook.services
com.netflix.mediaclient      - Netflix
com.amazon.appmanager        - Amazon
com.reddit.frontpage         - Reddit
com.spotify.music            - Spotify
com.twitter.android          - Twitter/X
com.instagram.android        - Instagram
com.instagram.barcelona      - Threads
com.ss.android.ugc.trill     - TikTok
com.google.ar.lens           - Google Lens
com.google.android.apps.magazines - Google News
com.google.android.apps.wellbeing   - Digital Wellbeing
com.google.android.apps.googleassistant - Google Assistant
com.adobe.spark.post         - Adobe Express
com.microsoft.skype.teams    - Microsoft Teams (carrier installs)
com.linkedin.android         - LinkedIn
```

## 4. Universal Danger List (NEVER remove)

These break the phone if removed:

```
com.android.launcher*        - Launcher (home screen)
com.android.phone            - Phone app
com.android.settings         - Settings
com.android.systemui         - System UI
com.android.providers.contacts - Contacts provider
com.android.providers.telephony - Telephony provider
com.android.providers.media   - Media provider
com.android.vending           - Play Store
com.google.android.gms        - Google Play Services
com.google.android.gsf        - Google Services Framework
com.sec.android.app.launcher* - Samsung launcher
com.xiaomi.account            - Xiaomi account (break Mi Cloud)
com.miui.securitycenter       - MIUI security (can break system)
com.mi.android.globallauncher - MIUI launcher
```

## 5. Brand-Specific Bloat Lists

### MIUI / Xiaomi (Android 13+)

```
-- SAFE TO REMOVE (user-level) --
com.miui.analytics              - Telemetry
com.miui.bugreport              - Bug reports
com.miui.msa.global              - MSA (ads engine)
com.miui.yellowpage              - Yellow Pages
com.miui.videoplayer             - Mi Video
com.miui.player                  - Mi Music
com.miui.fm                      - FM Radio
com.miui.screenrecorder          - Screen Recorder
com.miui.weather2                - Weather
com.miui.touchassistant          - Touch Assistant
com.miui.audiomonitor            - Audio Monitor
com.miui.cleaner                 - Cleaner
com.miui.powerkeeper             - Power Keeper
com.miui.phrase                  - Phrase shortcuts
com.miui.aod                     - Always-On Display
com.miui.miservice               - Mi Service
com.miui.freeform                - Freeform windows
com.miui.mishare.connectivity    - Mi Share
com.xiaomi.midrop                - Mi Drop
com.miui.backup                  - Local backup
com.miui.micloudsync             - Mi Cloud sync
com.xiaomi.payment               - Mi Pay
com.miui.notification            - Notification
com.miui.compass                 - Compass
com.duokan.phone.remotecontroller - IR Remote
com.xiaomi.discover              - App Vault / feed

-- DEPENDS ON USAGE (ask first) --
com.miui.gallery                 - Gallery (delete only if using Google Photos)
com.miui.screenshot              - Screenshot
com.miui.yellowpage              - Keep if you need caller ID
com.xiaomi.scanner               - Scanner
com.miui.weather2                - Weather (disable if using 3rd party)
com.miui.videoplayer             - Can break video playback from Files app
com.miui.player                  - Can break audio playback from Files app
```

### Samsung One UI

```
-- SAFE TO REMOVE --
com.samsung.android.bixby.wakeup  - Bixby
com.samsung.android.bixby.agent
com.samsung.android.bixbyvision.framework
com.samsung.android.visionintelligence
com.samsung.android.app.spage     - Samsung Free / Bixby Home
com.samsung.android.app.social    - Samsung Social
com.samsung.android.game.gametools - Game Tools
com.samsung.android.game.gos      - Game Optimizing Service
com.samsung.android.oneconnect    - SmartThings (if not used)
com.facebook.appmanager           - Facebook suite
com.facebook.katana
com.facebook.system
com.facebook.services
com.microsoft.skype.teams         - Carrier bloat
com.samsung.android.app.notes     - Samsung Notes (if using another notes app)
com.samsung.android.calendar      - Samsung Calendar
com.samsung.android.forest        - Digital Wellbeing (Samsung version)
com.samsung.kidsmode              - Kids Mode
com.sec.android.app.samsungapps   - Galaxy Store (may break system updates)
com.samsung.android.app.reminder  - Reminder

-- KEEP (break if removed) --
com.sec.android.app.launcher      - One UI Home (launcher)
com.samsung.android.phone          - Phone
com.samsung.android.messaging     - Messages
com.sec.android.gallery3d         - Gallery (system gallery provider)
com.samsung.android.providers.context  - Context provider
```

### ColorOS / OPPO / Realme / OnePlus

```
-- SAFE TO REMOVE --
com.heytap.market                 - App Market
com.heytap.browser                - Browser
com.heytap.cloud                  - HeyTap Cloud
com.coloros.filemanager           - File Manager
com.coloros.gamespace             - Game Space
com.coloros.weather               - Weather
com.coloros.video                 - Video player
com.coloros.music                 - Music player
com.coloros.compass               - Compass
com.coloros.soundrecorder         - Sound Recorder
com.coloros.assistantscreen       - Smart Sidebar
com.coloros.children              - Children's Space
com.opera.browser                 - Opera (carrier bloat)
com.opera.mini.native             - Opera Mini
com.facebook.katana               - Facebook suite
com.oppo.launcher                 - DANGER (launcher)

-- KEEP --
com.oppo.launcher                 - Launcher
com.android.incallui              - Phone UI
com.oneplus.camera                - Camera (OnePlus)
com.heytap.speechassist           - Voice assistant (may cause FC if removed)
```

### vivo / Funtouch OS

```
-- SAFE TO REMOVE --
com.vivo.weather                  - Weather
com.vivo.video                    - Video player
com.vivo.music                    - Music player
com.vivo.game                     - Game Center
com.vivo.vivokaraoke              - Karaoke
com.vivo.easyshare                - EasyShare
com.vivo.assistant                - Jovi assistant
com.facebook.katana               - Facebook suite
com.vivo.browser                  - Browser
com.vivo.appstore                 - App Store
com.vivo.digitalwellbeing         - Digital Wellbeing (vivo version)

-- KEEP --
com.vivo.launcher                 - Launcher
com.bbk.account                   - Account system
com.vivo.upslide                  - Up slide (may be tied to launcher)
```

### Nothing OS

Minimal bloat - mostly just Google + Nothing widgets:

```
-- SAFE TO REMOVE --
com.nothing.weather               - Nothing Weather
com.nothing.recorder              - Nothing Recorder
com.facebook.katana               - Facebook (if present)

-- KEEP --
com.nothing.launcher              - Nothing Launcher
com.nothing.glyph                 - Glyph interface (Nothing Phone)
```

## 6. Debloat Commands

### Uninstall (user-level, reversible via factory reset)
```powershell
adb shell pm uninstall -k --user 0 <package.name>
```

### Disable (if uninstall fails with Failure [-1000])
```powershell
adb shell pm disable-user --user 0 <package.name>
```

### Re-enable (undo disable)
```powershell
adb shell pm enable <package.name>
```

## 7. Debloat Safety Rules

1. Never remove the launcher - you will lose home screen access
2. Never remove Play Services (com.google.android.gms) - most apps will crash
3. Never remove the keyboard - you will lose typing ability
4. Never remove Phone / SMS / Contacts providers - calls and texts will break
5. If unsure, disable instead of uninstall - reversible with pm enable
6. Uninstalling is user-level only - factory reset restores everything
7. Debloating frees little storage (<500 MB) - it reduces RAM/CPU/ads, not space

---

# PART 3: Cleanup Actions

## WhatsApp Database backups (safe - regenerated by WhatsApp)
```powershell
adb shell ls -lh /sdcard/Android/media/com.whatsapp/WhatsApp/Databases/
adb shell rm /sdcard/Android/media/com.whatsapp/WhatsApp/Databases/msgstore-OLD.db.crypt14
```

## WhatsApp Images (backup to PC first)
```powershell
adb pull "/sdcard/Android/media/com.whatsapp/WhatsApp/Media/WhatsApp Images" C:\backup
adb shell rm -rf "/sdcard/Android/media/com.whatsapp/WhatsApp/Media/WhatsApp Images"
```

## Camera roll (backup first)
```powershell
adb pull /sdcard/DCIM/Camera C:\backup\DCIM
adb shell rm -rf /sdcard/DCIM/Camera
```

## App caches (system-level)
```powershell
adb shell pm trim-caches 999G
```

## Empty folders
```powershell
adb shell find /sdcard -type d -empty -delete
```

## Corpse data (directories for uninstalled apps)
```powershell
adb shell pm list packages > installed.txt
adb shell ls /sdcard/Android/data > data_dirs.txt
```

---

# Safety Rules

1. Always backup before delete - adb pull to PC first
2. WhatsApp Databases - keep the most recent, delete older
3. Games - ask user before touching
4. System apps - never touch
5. Verify - adb shell df -h /sdcard after each action

---

# Verdict

```powershell
adb shell df -h /sdcard
```
