# 🖨️ Klipper Config Backup — Ender 3 V3 SE

Complete Klipper configuration backup for the **Creality Ender 3 V3 SE**, running a full Mainsail stack on a Raspberry Pi 5, with automatic GitHub backups triggered from Home Assistant.

---

## 🧰 Stack

| Component | Role |
|---|---|
| [Klipper](https://github.com/Kailh/klipper) | 3D printer firmware |
| [Moonraker](https://github.com/Arksine/moonraker) | API server |
| [Mainsail](https://github.com/mainsail-crew/mainsail) | Web interface |
| [KlipperScreen](https://github.com/jordanruthe/KlipperScreen) | Touchscreen UI |
| [Spyglass](https://github.com/mainsail-crew/spyglass) | Camera streaming (Pi 5 camera module via flex cable) |
| [Moonraker-Timelapse](https://github.com/mainsail-crew/moonraker-timelapse) | Timelapse generation |

**Host:** Raspberry Pi 5

---

## 📁 File structure

```
Klipper-Backup/
│
├── printer.cfg          # Main Klipper config — steppers, bed, hotend, probing
├── macros.cfg           # Custom macros (START_PRINT, END_PRINT, PAUSE, etc.)
├── shell-macros.cfg     # Shell command macros — includes GitHub backup trigger
├── variables.cfg        # Persistent runtime variables (auto-updated by Klipper)
│
├── moonraker.conf       # Moonraker API configuration
├── mainsail.cfg         # Mainsail-specific settings
│
├── KlipperScreen.conf   # Touchscreen UI configuration
├── spyglass.conf        # Camera stream configuration (Pi camera module)
├── timelapse.cfg        # Timelapse plugin configuration
│
├── scripts/             # Shell scripts called by Klipper shell macros
│
└── .gitignore
```

---

## 🔄 Automatic backup system

Backups are triggered by a **Klipper shell macro** defined in `shell-macros.cfg`, which calls a script in `scripts/` that does a git commit and push.

It is invoked from three places:

- **On print start** — saves config state before each print
- **On standby** — saves final state after print completion
- **From Home Assistant** — via `button.impresora_3d_macro_backup_github_klipper`, allowing on-demand remote backup

The backup script is essentially:

```bash
cd ~/printer_data/config
git add -A
git commit -m "Klipper backup: $(date '+%Y-%m-%d %H:%M')"
git push origin main
```

The commit history in this repo reflects the actual usage history of the printer.

---

## ⚙️ Key configuration notes

- **Probe:** CR Touch — mesh bed leveling configured with `[bltouch]` and `[bed_mesh]`
- **Input shaper:** Resonance compensation tuned via `[resonance_tester]` with ADXL345
- **PID autotune:** Hotend and bed PIDs are recalibrated automatically when ambient temperature drifts significantly — triggered via Home Assistant
- **Pressure advance:** Tuned per filament type
- **Filament runout sensor:** Mapped to a pause macro with Alexa voice announcement via HA
- **Camera:** Raspberry Pi camera module connected via flex cable, streamed via Spyglass
- **Timelapse:** Rendered per-print via Moonraker-Timelapse using the Pi camera feed

---

## 🏠 Home Assistant integration

This printer is monitored and controlled from Home Assistant via the [Moonraker integration](https://github.com/marksie1988/ha-moonraker):

- Print state, progress and ETA exposed as HA sensors
- Print start/finish events send Telegram notifications with gcode thumbnail previews
- Filament runout triggers an Alexa announcement across all rooms
- Power managed via a Zigbee smart plug (`switch.ender_3_v3_se`)
- Automatic PID recalibration triggered when ambient temperature drifts > 4°C from the last calibration run

---

## 🚀 How to use this config

> ⚠️ This is a personal backup, not a plug-and-play config. Use it as reference — hardware offsets, PID values, and probe positions are specific to this machine and will need recalibration on yours.

1. Install Klipper, Moonraker and Mainsail on your Raspberry Pi (e.g. via [KIAUH](https://github.com/dw-0/kiauh))
2. Use this repo as a structural reference
3. **Always run PID autotune and bed mesh calibration on your own machine before printing**
4. Adjust `[stepper_*]` `rotation_distance` and `position_*` values to match your specific hardware

---

## ☕ Buy me a coffee

If this saved you time and you feel like it, a coffee is always welcome — but absolutely no pressure.

[→ paypal.me/javilopez83](https://www.paypal.me/javilopez83)

---

## 📄 License

MIT — reference and adapt freely.

---

*Ender 3 V3 SE running in Benidorm, Spain. Backed up automatically so nothing is ever lost.*
