# Supported Hardware & Native Linux Drivers — Essaim OS

Complete hardware compatibility reference for **Essaim OS** (Hub, GPU/CPU workers, Solo ASICs, Shelly smart plugs, RGB lighting, cooler displays, PWM case fans, and AIO liquid coolers).

---

## 1. Hub Hosts (`essaim-hub`)

| Platform | Artifact | Notes |
|---|---|---|
| **Raspberry Pi 3 / 4 / 5 & ARM64 SBCs** | `docker-arm64.tar` | `<2%` CPU, ~20–30 MB RAM (`--memory 96m`), SD-friendly single-transaction SQLite WAL |
| **Linux Servers & NAS** (Debian, Ubuntu, OpenMediaVault, TrueNAS, Synology, Unraid) | `docker-amd64.tar` | `network_mode: host` for automatic LAN discovery, WoL, ASICs & Shelly plugs |
| **Dedicated Mini-PC / x86_64 Bare-Metal** | `amd64.img.xz` | UEFI & legacy BIOS, 8 GB min / 16–32 GB recommended |
| **Proxmox VE / QEMU / KVM** | `amd64.qcow2` | Bridge NIC on same LAN as workers (`qemu-img resize ... +12G`) |

---

## 2. GPUs & CPUs (`essaim-agent`)

- **NVIDIA GPUs (Bare-Metal Linux USB)**: Full NVML overclocking (core lock/offset, memory lock/offset, power limit, fan target/curve, live hardware bounds probing), RTX 50-Series Blackwell (`RTX 5090`, `5080`, `5070 Ti`, `5070`, `5060 Ti`, `5060`, `5050`), RTX 40/30/20/16 series, GTX 10-series, Quadro/RTX Workstation. Hot open-kernel driver switching via [ESSAIM-OS/essaim-drivers](https://github.com/ESSAIM-OS/essaim-drivers).
- **AMD Radeon GPUs (Bare-Metal Linux USB)**: `amdgpu` sysfs monitoring & tuning + hot ROCm OpenCL userspace switching (`essaim-rocm-compat`).
- **Windows WSL2 Workers**: NVIDIA CUDA GPUs + CPU mining inside Windows 10/11 WSL2 without dual-booting (Windows keeps GPU OC & driver control).
- **CPUs (AMD Ryzen / EPYC, Intel Core / Xeon)**: `XMRig` & `SRBMiner` with automatic Huge Pages (2 MB / 1 GB), MSR optimization, hardware temperature selection (`k10temp`, `zenpower`, `coretemp`, Super I/O), thermal safety cutoff (`>=95°C` for 60 s), and optional anti-freeze thread reservation on shared/WSL2 PCs.

---

## 3. Solo & Industrial ASIC Miners (Direct LAN)

| Family | Supported Models | Protocol | Capabilities | Quota |
|---|---|---|---|---|
| **AxeOS Solo Miners** | Bitaxe Supra, Ultra, Gamma, Hex, NerdQAxe | Local HTTP REST (`/api/system/info`, `PATCH /api/system`) | Live hashrate, J/TH, chip/VRM temps, pool/wallet editing, safe freq/voltage tuning (`100–1200 MHz`, `900–1450 mV`), Best Diff & Solo Block alerts | **Free** |
| **NerdMiner** | NerdMiner v2 | Local HTTP | Hashrate, shares, Best Diff, uptime | **Free** |
| **Canaan Avalon Nano** | Avalon Nano 3 | CGMiner TCP `:4028` | Hashrate, power, temperatures, fan RPM | **Free** |
| **Industrial CGMiner ASICs** | Antminer / Avalon CGMiner-compatible | CGMiner TCP `:4028` | Multi-hashboard monitoring, power, temps, fans | Standard |

---

## 4. Shelly Smart Plugs & Energy Meters (Local LAN, Zero Cloud)

| Generation | Supported Models | Protocol & Auth | Capabilities |
|---|---|---|---|
| **Gen2 / Gen3 / Gen4** | Shelly Plus Plug S, Plug S Gen3, Plus 1PM, 1PM Mini Gen3, Plus 2PM, **Pro 1PM / 2PM / 4PM** (multi-channel), Pro EM-50, **Pro 3EM**, PM Mini Gen3 | Local HTTP RPC (`/shelly`, `/rpc/Shelly.GetStatus`, `/rpc/Switch.Set`) + **HTTP Digest SHA-256** | Exact wall `W`, `V`, `A`, `PF`, `Wh`, internal `°C`, relay ON/OFF, Read-Only mode, Graceful Off (`<15 W`), Hardware Hard Reboot (`toggle_after=10`), 1 HTTP request per multi-channel device |
| **Gen1** | Shelly Plug S, Shelly Plug, Shelly 1PM, Shelly 2.5, Shelly EM, Shelly 3EM | Local HTTP REST (`/shelly`, `/status`, `/relay/<ch>`) + **HTTP Basic** | Wall `W`, `Wh` (`W·min/60` & `emeters`), `°C`, relay ON/OFF, Read-Only mode, Graceful Off, Hardware Hard Reboot (`timer=10`) |

---

## 5. Native RGB Lighting, Cooler Displays, AIOs & PWM Case Fans (Bare-Metal Linux)

Zero OpenRGB or Qt daemon required — direct Rust hardware drivers + Linux-optimized `liquidctl` bridge:

| Category | Models & Chips | Interface / ID | Capabilities |
|---|---|---|---|
| **XPG Cooler Display** | XPG MAESTRO PLUS 62DA (3-zone 7-segment display) | USB HID (`125F:7601`) | Hybrid native Rust `usbfs` + bridge to [ESSAIM-OS/xpg-maestro-62da-linux](https://github.com/ESSAIM-OS/xpg-maestro-62da-linux) (Auto GPU/CPU, CPU, GPU, Hybrid, 5s Cycle, Custom 3-zone, °C/°F, Night Mode) |
| **AIO Liquid Coolers, USB Hubs & PSUs** | NZXT Kraken X/Z/Elite & Smart Device, Corsair iCUE Commander / Hydro / HX-RMi, Aquacomputer D5 Next / Quadro / Octo, ASUS ROG Ryujin / Ryuo, MSI CoreLiquid, Lian Li Galahad II / Uni Fan | USB HID / Bulk ([ESSAIM-OS/liquidctl](https://github.com/ESSAIM-OS/liquidctl)) | Liquid temp, pump & radiator RPM, PSU watts, pump control (safety floor `>=40%`, `100%` emergency at `>=48°C` liquid), RGB sync, `<0.5 ms` sysfs pre-scan (0 Python processes if no AIO attached, no Pillow) |
| **Gigabyte / AORUS RTX 50-Series GPUs** | NVIDIA Blackwell RTX 5090 / 5080 / 5070 Ti / 5070 | NVIDIA I²C (`0x75`) | 6 zones, static, Night blackout, GPU temp gauge, animations (standalone: [ESSAIM-OS/gigabyte-rtx50-rgb-linux](https://github.com/ESSAIM-OS/gigabyte-rtx50-rgb-linux)) |
| **Gigabyte / AORUS RTX 20/30/40 GPUs** | Gaming / Master / Vision / Eagle RGB Fusion 2 | NVIDIA I²C (`0x62, 0x63, 0x70, 0x71, 0x72`) | Static color, Night blackout, GPU temp gauge, hive status |
| **Gigabyte / AORUS Motherboards** | ITE `IT5711`, `IT5702`, `IT8297` (B850, X870, Z890, B650, X670, Z790…) | USB HID (`048D:*`) | 11 onboard zones + 6x 57-LED addressable ARGB strips (automatic GRB/RGB calibration) |
| **AMD Wraith Prism Cooler** | Cooler Master / AMD Wraith Prism (internal USB cable) | USB HID (`2516:0051`) | Per-LED 15-LED ring, fan blades, and AMD logo |
| **ASUS Aura Sync** | ROG / TUF / Prime USB controllers & addressable hubs | USB HID (`0B05:18F3, 1939, 19AF, 1872, 1867`) | Static color, Night blackout, GPU temp gauge, hive status |
| **Generic Linux Kernel LEDs** | Any `*rgb*`, `*multicolor*`, `*aura*`, `*aorus*`, `multi_intensity` device | `/sys/class/leds` | Color / brightness & Night blackout |
| **Motherboard PWM Case Fans** | `nct6775`, `it87` (`it8628`/`it8686`), `gigabyte-wmi`, `asus-wmi-sensors`, `asus-ec-sensors`, `dell-smm-hwmon` | `/sys/class/hwmon` | BIOS Auto, Fixed 20–100%, or **GPU-temp target** (prevents BIOS regulating case fans on idle CPU temp) |
