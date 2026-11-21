# Frequently Asked Questions (FAQ) — Essaim OS

Direct technical answers about **Essaim OS**, self-hosted GPU/CPU/ASIC mining, pricing, Windows WSL2, NVIDIA RTX 50-Series Blackwell, Shelly smart plugs, and smart electricity tariffs.

---

## General & Pricing

### What is Essaim OS?
**Essaim OS** is a self-hosted cryptocurrency mining operating system and local-LAN fleet hub written in **Rust**. It manages bare-metal Linux USB rigs, Windows WSL2 PCs, and Solo/CGMiner ASICs from a lightweight local Hub running on a Raspberry Pi, Docker, mini-PC, or Proxmox VM — with zero cloud dependency.

### How much does Essaim OS cost compared to HiveOS?
- **1st GPU/CPU worker**: **Free forever** per Hub (no account or credit card required).
- **Micro Solo ASICs** (Bitaxe, NerdQAxe, NerdMiner, Avalon Nano 3): **Free forever** and excluded from worker quota.
- **Additional active GPU/CPU workers**: **$1/month** per active rig (vs **$3/rig/month** on HiveOS — 3× cheaper), counted in **Worker-Days** so powered-off or idle rigs do not consume credits.
- See the detailed breakdown in [Comparison vs HiveOS, RaveOS, minerstat & SimpleMining](COMPARE.md) or [essaimos.com/en/tarifs](https://essaimos.com/en/tarifs).

### What happens if my Internet connection goes down?
Unlike cloud-managed mining OSes, **Essaim Hub runs locally on your LAN (`:8420`)**. Your dashboard, worker monitoring, thermal protection, Shelly smart plugs, and overclocking continue operating locally. Ed25519-signed license tokens remain valid offline for **10 days**.

---

## Workers, GPUs & Windows WSL2

### Can I mine on a Windows gaming PC without wiping Windows?
Yes. Essaim OS includes a dedicated **Windows WSL2 launcher** (`essaim-agent.exe` + `essaim-wsl.tar.gz`) served directly by your local Hub. It runs CUDA GPU and CPU workloads inside WSL2 while keeping Windows intact, and includes an optional **anti-freeze CPU thread guard** that reserves threads for Windows and lowers miner priority.

### Does Essaim OS support NVIDIA RTX 5090, RTX 5080, RTX 5070 Ti, and RTX 5060 (Blackwell)?
Yes. Bare-metal Linux workers include:
- **Live NVML hardware OC limit probing** (clocks, offsets, and power limits work immediately on new Blackwell cards without requiring a pre-hardcoded table or prior autotune).
- **Chained per-GPU Auto-Tune** (`balanced`, `eco`, `max`) and 10-minute supervised overclock trials with per-GPU `PCI_BUS_ID` Xid fault isolation.
- **Hot NVIDIA open-kernel driver switching** via [ESSAIM-OS/essaim-drivers](https://github.com/ESSAIM-OS/essaim-drivers) with automatic rollback.
- **Native Gigabyte / AORUS RTX 50-Series RGB control** over NVIDIA I²C `0x75` (also released standalone at [ESSAIM-OS/gigabyte-rtx50-rgb-linux](https://github.com/ESSAIM-OS/gigabyte-rtx50-rgb-linux)).

### Does Essaim OS support Bitaxe, NerdQAxe, NerdMiner, and Avalon Nano 3?
Yes. Essaim Hub polls Solo and CGMiner ASICs directly over your local LAN:
- **AxeOS** (Bitaxe Supra, Ultra, Gamma, Hex, NerdQAxe): live hashrate, J/TH efficiency, chip & VRM temperatures, Best Diff record tracking, Solo Block Found alerts, remote pool/wallet editing, and safe frequency/voltage tuning (`100–1200 MHz`, `900–1450 mV` anti-overvoltage guard).
- **NerdMiner v2** & **Canaan Avalon Nano 3** (CGMiner API).
- Micro Solo miners are **free** and do not count against your GPU/CPU worker quota.

---

## Smart Energy & Shelly Smart Plugs

### Which electricity tariffs and Spot APIs are built in?
Essaim Hub supports **9 currencies** and:
- Fixed kWh rate
- Simple Off-Peak / Peak (HC/HP)
- Global 2-tier and 3-tier Time-of-Use (TOU) presets (France, Spain `2.0TD`, UK Economy 7 / Octopus Go, US, Canada, Australia, Switzerland)
- **EDF Tempo** (France Blue / White / Red days) with automatic Red-Day shutdown and Wake-on-LAN or Shelly wake-up
- Live Spot APIs: **Octopus Agile** (UK), **REE PVPC** (Spain), **aWATTar** (DE/AT), and **Tibber**
- Configurable price-cap auto-pause and automatic resume when rates drop.

### How do Shelly smart plugs and energy meters work in Essaim OS?
Essaim Hub talks **100% locally over LAN HTTP** (no Shelly Cloud) to **Gen1** (`Plug S`, `1PM`, `2.5`, `EM`, `3EM`) and **Gen2/Gen3/Gen4** (`Plus Plug S`, `Plug S Gen3`, `Plus 1PM/2PM`, `Pro 1PM/2PM/4PM`, `Pro EM`, `Pro 3EM`, `PM Mini`) with Basic and SHA-256 Digest auth:
- Replaces manual non-GPU overhead estimates with exact **wall power (`W`)**, voltage (`V`), current (`A`), power factor (`PF`), plug temperature (`°C`), and cumulative energy (`kWh`).
- Supports **Read-Only mode** (blocks accidental power cuts), **Graceful Off** (Linux `/v1/poweroff` → waits for rig offline and `<15 W` for 10 s → cuts relay), and **Hardware Hard Reboot** (`toggle_after=10` / `timer=10` run by the plug's internal timer so power always restores even if the network drops).

---

## Hardware RGB, Cooler Displays & Liquid Cooling

### Can I turn off or control Gigabyte RTX 5080/5090 and motherboard RGB on Linux without OpenRGB?
Yes. Bare-metal Essaim OS agents include a built-in Rust driver (zero OpenRGB, zero Qt) supporting Gigabyte/AORUS Blackwell GPUs (`I2C 0x75`), RTX 20/30/40 GPUs, Gigabyte ITE motherboards (`IT5711`, `IT5702`, `IT8297`), AMD Wraith Prism (`2516:0051`), ASUS Aura Sync, and `/sys/class/leds` — with 1-click Night Blackout, live GPU temperature gauge, and 22 lighting effects. For non-Essaim Linux systems, use our standalone open-source driver [ESSAIM-OS/gigabyte-rtx50-rgb-linux](https://github.com/ESSAIM-OS/gigabyte-rtx50-rgb-linux).

### Are XPG MAESTRO PLUS 62DA cooler displays and AIO liquid coolers supported?
Yes:
- **XPG MAESTRO PLUS 62DA (`125f:7601`)**: hybrid native Rust `usbfs` driver + atomic `/run/essaim/display.json` bridge for [ESSAIM-OS/xpg-maestro-62da-linux](https://github.com/ESSAIM-OS/xpg-maestro-62da-linux) (modes: Auto GPU/CPU, CPU, GPU, Hybrid, 5s Cycle, Custom 3-zone, °C/°F, Night Mode).
- **AIO Liquid Coolers, USB Fan Hubs & USB PSUs**: powered by our Linux-optimized [ESSAIM-OS/liquidctl](https://github.com/ESSAIM-OS/liquidctl) fork (no Pillow, `<0.5 ms` sysfs pre-scan, 0 Python processes spawned when no AIO is attached, enforced pump safety floor `>=40%`, and `100%` emergency cooling at `>=48°C` liquid temp).

---

## Remote Access & Security

### Do I need to open ports on my router for mobile monitoring?
No. The Android app embeds **Tailscale (`tsnet`)** directly inside the app (no separate VPN app or port forwarding required), and the Telegram bot uses outbound-only long polling (`getUpdates`) with one-time pairing codes.

### What are the default SSH credentials for Hub disk images and bare-metal rigs?
Username `essaimos` (with `sudo`), default password `essaimos-pass` (forced change on first login). Direct `root` SSH is disabled and SSH is restricted to the local LAN.
