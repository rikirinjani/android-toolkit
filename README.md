# Android Toolkit

**All-in-one Android phone utility skill for Claude Code / OpenCode.**

7 modules over ADB: storage inspection, debloat, battery doctor, crash analyzer, automation, system tuner, and network security. Universal across brands.

## Modules

| # | Module | What it does |
|---|--------|-------------|
| 1 | **Storage Inspection** | `du` hierarchy, WhatsApp/DCIM breakdown, SD Maid SE features, safe cleanup |
| 2 | **Debloat** | Brand detection + universal + 5 brand lists (MIUI, Samsung, ColorOS, vivo, Nothing) |
| 3 | **Battery Doctor** | Per-app drain, wakelocks, doze analysis, fix recommendations |
| 4 | **Crash Analyzer** | logcat crash, bootloop, reboot reason, kernel panic |
| 5 | **Automation** | `input tap/swipe/text/keyevent`, activity launcher, practical scripts |
| 6 | **System Tuner** | Animation speed, WiFi scan interval, battery presets, restore defaults |
| 7 | **Network Security** | ADB over TCP, verified boot, unknown sources, scorecard |

## Brand Support

| Brand | Tested |
|---|---|
| Xiaomi / MIUI | ✅ Real device (Redmi Note 11, Android 13) |
| Samsung One UI | Lists compiled |
| ColorOS/OPPO/Realme | Lists compiled |
| vivo / Funtouch | Lists compiled |
| Nothing OS | Lists compiled |
| Universal 3rd-party | ✅ Verified on real device |

## Quick Start

```powershell
adb shell df -h /sdcard
adb shell getprop ro.product.manufacturer
```

## Usage (OpenCode)

```
skill android-toolkit
```

Or load per-session:
```
skill adb-storage-inspector
```

## Installation

```bash
git clone https://github.com/rikirinjani/adb-storage-inspector.git
# Symlink into agent skills directory
```

## Safety

- Backup before delete
- Never remove launcher, Play Services, or Phone/SMS providers
- Disable instead of uninstall if unsure
- All changes user-level; factory reset restores everything

## License

MIT
