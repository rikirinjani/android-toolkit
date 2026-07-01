# ADB Storage Inspector

**ADB-powered Android phone storage inspector + debloater skill for Claude Code / OpenCode.**

Inspired by SD Maid SE — replicates its features using plain ADB commands. Works across all Android brands (Xiaomi, Samsung, OPPO, vivo, Nothing, Pixel, etc.) with brand-specific bloat lists.

## Features

- **Storage analysis** — `du` hierarchy, largest files, app data, WhatsApp breakdown
- **Debloat** — universal + brand-specific bloat lists for MIUI, One UI, ColorOS, Funtouch, Nothing OS
- **Safe deletes** — WhatsApp DB cleanup, camera roll, empty folders, corpse data
- **Duplicate detection** — `md5sum` group-by-hash
- **Backup before cleanup** — `adb pull` to PC first

## Quick Start

```powershell
adb shell df -h /sdcard
adb shell getprop ro.product.manufacturer
adb shell pm list packages
```

## Brand Support

| Brand | Bloat list | Tested |
|---|---|---|
| Xiaomi / MIUI | 25 packages | ✅ Real device |
| Samsung One UI | 18 packages | |
| OPPO / ColorOS / Realme | 14 packages | |
| vivo / Funtouch | 12 packages | |
| Nothing OS | 4 packages | |
| Universal 3rd-party | 18 packages (Facebook, Netflix, Amazon...) | ✅ |

## Usage (OpenCode)

```
skill adb-storage-inspector
```

## Installation

```bash
git clone https://github.com/rikirinjani/adb-storage-inspector.git
# Symlink into agent skills directory
```

## Structure

```
adb-storage-inspector/
  README.md
  SKILL.md                       # install anywhere
  .config/opencode/skills/adb-storage-inspector/SKILL.md   # OpenCode path
```

## License

MIT
