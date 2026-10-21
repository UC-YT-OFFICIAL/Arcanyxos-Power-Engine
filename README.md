# ⚡ ARCANYX Power Engine

**Intelligent charging control for MediaTek devices.** A KernelSU/Magisk module that manages charge-limit suspension, temperature protection, and charging modes — with a built-in WebUI for live monitoring and control.

![Platform](https://img.shields.io/badge/MTK-mt6375-blue) ![Status](https://img.shields.io/badge/r28-stable-brightgreen) ![License](https://img.shields.io/badge/license-Proprietary-red)

---

## ✨ Features

| Feature | Description |
|---|---|
| 🎯 **Charge Limit** | Pause charging at a set % (50–100), auto-resume ~2% below |
| 🌡 **Temperature Guard** | Stop at configured °C, auto-resume on cool-down |
| 🎛 **Charging Modes** | `auto` `slow` `fast` `limit` — kernel-managed or custom current |
| 📊 **Live WebUI** | Metallic-themed dashboard: battery ring, live stats, sliders, toggles, manual controls |
| 🔌 **Native Engine** | Compiled C binaries — no shell overhead, always-on supervision |
| 🔐 **Signed Release** | Ed25519 manifest signature; tamper-evident install |
| ⚡ **Fast-Charge Control** | Switched-cap fast-charge enable/disable |
| 🛡 **Stale-State Recovery** | Self-heals stuck charge-hold on boot |
| 🔗 **Channel Link** | Update button opens `t.me/uc_yt_official` |

---

## 📱 Requirements

- **Platform:** MediaTek or compatible PMIC sysfs layout)
- **Root:** KernelSU (recommended) or Magisk 20.4+
- **WebUI:** KernelSU app (Launch button)
- **Battery:** MTK charging with `primary_chg` + `enable_sc` + `enable_power_path` sysfs nodes

*On unsupported layouts the module reports "MISS" for missing nodes and stays safe.*

---

## 📦 Install

1. Download `arcanyx_power_publish_r28.zip`
2. Flash via **KernelSU → Modules → Install**, or `magisk --install-module`
3. **Reboot** — first boot self-decodes binaries and starts the supervisor

**Channel:** [t.me/uc_yt_official](https://t.me/uc_yt_official)

---

## 🖥 WebUI

Open **KernelSU → Modules → Arcanyx Power Engine → WebUI**.

- Battery ring + live %/temp/voltage/current
- Charge-limit slider (50–100%)
- Temp stop/resume sliders
- Engine + temp-protection toggles
- Stop/Resume manual buttons
- **↗ Update button** → opens the ARCANYX channel

---

## 🔌 Quick Commands

```sh
# status/health/config
/data/adb/modules/arcanyx_power_engine/bin/pwctl status
/data/adb/modules/arcanyx_power_engine/bin/pwctl health
/data/adb/modules/arcanyx_power_engine/bin/pwctl probe

# settings
/data/adb/modules/arcanyx_power_engine/bin/pwctl set CHARGE_LIMIT_PERCENT 85
/data/adb/modules/arcanyx_power_engine/bin/pwctl set TEMP_STOP_C 40
/data/adb/modules/arcanyx_power_engine/bin/pwctl set TEMP_RESUME_C 35
/data/adb/modules/arcanyx_power_engine/bin/pwctl mode limit

# manual
/data/adb/modules/arcanyx_power_engine/bin/pwctl charge_stop
/data/adb/modules/arcanyx_power_engine/bin/pwctl charge_resume
/data/adb/modules/arcanyx_power_engine/bin/pwctl limit 90

# convenience wrapper
sh /data/adb/modules/arcanyx_power_engine/scripts/charging_control.sh slow
sh .../charging_control.sh stop
sh .../charging_control.sh resume
```

**Config file** (`config/charging.conf`, auto-regenerated if missing):

```ini
CHARGING_ENABLED=1
CHARGING_MODE="limit"            # auto|slow|fast|limit
TEMP_LIMIT_ENABLED=1
TEMP_RESUME_C=35                 # °C
TEMP_STOP_C=40                   # °C
CHARGE_LIMIT_PERCENT=85          # 50-100
FAST_CHARGE_ENABLED=1
LOG_LEVEL="INFO"
```

---

## 🗂 Layout

```
/data/adb/modules/arcanyx_power_engine/
├── module.prop              # id/name/version/description
├── service.sh               # boot hook → exec bin/pwsvc
├── post-fs-data.sh          # perm restore + config seed + node perms
├── uninstall.sh             # cleanup + resume normal charging
├── manifest.sha256          # sorted SHA-256 of every file (Ed25519-signed)
├── manifest.sig
├── bin/
│   ├── pwctl                # engine CLI (native)
│   ├── pwsvc                # supervisor+worker daemon (native)
│   ├── pwsvc_stub           # launcher
│   ├── arcxdecode           # blob decoder
│   ├── arcxverify           # Ed25519+SHA-256 verifier
│   └── arcanyx_power.sh     # compatibility shim
├── arcx/*.bin               # obfuscated runtime binaries (XOR 0xA5)
├── scripts/
│   ├── charging_control.sh
│   └── temp_monitor.sh
├── config/charging.conf     # runtime state
└── webroot/index.html       # KSU WebUI
```

---

## 🔐 Release Integrity

Every publish ZIP ships `manifest.sha256` (sorted `<sha256> <relpath>`) signed with **Ed25519**. `bin/arcxverify` verifies signature + all file hashes before install commits.

- **Public-key SHA-256:** `2382015620e2bf4980571a7df81033e1ac997b5c1eb445846ded097181717105`
- **On tamper:** installer aborts, module untouched; runtime writes `integrity/FAILED` and exits cleanly

---

## 📜 Credits

- **Engine, WebUI, signing, packaging** — [@UCYTOFFICIAL](https://t.me/uc_yt_official)
- **ARCANYX project** — unified engine research
- **KernelSU** — modules framework
- **Nothing / MediaTek** — PMIC sysfs paths

**License:** Proprietary — see `LICENSE` in module root.

---

*ARCANYX Power Engine — charging under your control, not the kernel's.*
