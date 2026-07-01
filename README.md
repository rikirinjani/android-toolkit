# ADB Storage Inspector

ADB-powered Android phone storage inspector skill for **Claude Code** / **OpenCode**.

Inspired by SD Maid SE — replicates its key features (`du` hierarchy, largest files, WhatsApp cleanup, app data analysis, corpse detection, empty folder cleaning) using plain ADB commands.

## Usage

Load the skill in any session:

```
skill adb-storage-inspector
```

Or install as a permanent skill by symlinking into your agent's skills directory:

```bash
# Claude Code
ln -s "$PWD" ~/.claude/skills/adb-storage-inspector

# OpenCode
ln -s "$PWD/.config/opencode/skills/adb-storage-inspector" ~/.config/opencode/skills/
```

## Prerequisites

- Android device with **USB Debugging** enabled
- ADB installed on host machine
- Device connected via USB and authorized (`adb devices` shows `device`)

## What it does

| Feature | ADB equivalent |
|---|---|
| Overall storage overview | `df -h` |
| Folder size analysis | `du -h -d 1` |
| Largest files | `find + sort` |
| WhatsApp media breakdown | Per-type size (Images, Video, Audio, Documents) |
| WhatsApp DB backup cleanup | Keep latest, delete old |
| App data hogs | `du -h -d 1 /sdcard/Android/data` |
| Corpse finder | Orphaned data dirs for uninstalled apps |
| App cache trim | `pm trim-caches` |
| Empty folder cleanup | `find -type d -empty -delete` |
| Duplicate detection | `md5sum` group-by-hash |
| Safe backup before delete | `adb pull` to PC first |

## Structure

```
adb-storage-inspector/
├── README.md
└── .config/opencode/skills/adb-storage-inspector/
    └── SKILL.md          # The skill payload
```

## License

MIT
