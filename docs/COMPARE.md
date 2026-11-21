# Essaim OS vs HiveOS, RaveOS, minerstat & SimpleMining

An objective technical and cost comparison between **Essaim OS** and major cloud-managed cryptocurrency mining operating systems.

---

## Feature & Architecture Comparison

| Feature | **Essaim OS** | **HiveOS** | **RaveOS** | **minerstat** | **SimpleMining (SMOS)** |
|---|---|---|---|---|---|
| **Architecture** | **100% Self-Hosted LAN Hub (Rust)** | Cloud-dependent SaaS | Cloud-dependent SaaS | Cloud-dependent SaaS | Cloud-dependent SaaS |
| **Works if Cloud / Internet is Down** | **Yes** (LAN Hub + 10-day offline Ed25519 token) | Partial (no dashboard / control) | Partial (no dashboard / control) | Partial (no dashboard / control) | Partial (no dashboard / control) |
| **Hub Footprint** | ~20–30 MB RAM (Raspberry Pi, Docker, NAS, VM) | Cloud only | Cloud only | Cloud only | Cloud only |
| **Free Tier** | **1 GPU/CPU rig free forever** + **unlimited micro Solo ASICs** | 1–4 rigs (pool restrictions apply on 2–4) | 1 rig free | 1 worker free | None (trial only) |
| **Paid Cost per Rig / Month** | **$1 / month** (billed per active Worker-Day) | $3 / month | $2 / month | ~$2 / month | $2 / month |
| **Windows Mining (No Dual-Boot)** | **Yes (native WSL2 launcher)** | No (Linux USB only) | No (Linux USB only) | Yes (Windows client) | No (Linux USB only) |
| **Per-GPU Xid Fault Isolation** | **Yes** (per `PCI_BUS_ID`, healthy GPUs keep mining) | Rig-wide watchdog reboot | Rig-wide watchdog reboot | Rig-wide actions | Rig-wide reboot |
| **Supervised OC Trial & Auto-Tune** | **Yes** (10-min trial + chained per-GPU Auto-Tune) | Manual / external scripts | Manual | Manual / ClockTune | Manual |
| **Global Smart Electricity Tariffs** | **Native** (EDF Tempo, HC/HP, TOU, Octopus Agile, PVPC, aWATTar, Tibber + auto-pause/WoL) | Manual flat kWh rate | Manual flat kWh rate | Basic electricity cost | None |
| **Local Shelly Smart Plugs (Gen1–4)** | **Native LAN** (exact wall W/kWh, Graceful Off, Hardware Hard Reboot) | Third-party / manual | No | No | No |
| **Solo Micro ASICs (Bitaxe / NerdQAxe / Avalon Nano 3)** | **Native LAN** (free, AxeOS tuning, Best Diff, Block alerts) | Limited / ASIC Hub | No | Limited | No |
| **Native Linux RGB, Displays & AIOs** | **Built-in Rust** (RTX 50 Blackwell `0x75`, Gigabyte IT5711, Wraith Prism, XPG 62DA, `liquidctl`) | No | No | No | No |
| **Zero-Port Remote Access** | **Embedded Tailscale Android App + Private Telegram Bot** | Cloud web / app | Cloud web / app | Cloud web / app | Cloud web |
| **Custom Miner Sandboxing** | **Yes** (Linux namespaces + `nftables` LAN isolation) | Runs as root | Runs as root | Standard process | Runs as root |

---

## Monthly Fleet Cost Comparison (Active GPU/CPU Rigs)

*Assuming rigs mine on any pool of your choice (no forced dev-fee pool lock-in):*

| Active GPU/CPU Rigs | **Essaim OS** | **HiveOS** ($3/rig) | **RaveOS / SMOS** ($2/rig) | **Annual Savings vs HiveOS** |
|---:|---:|---:|---:|---:|
| **1 rig** (+ Solo ASICs) | **$0 / mo** | $0 / mo | $0–$2 / mo | $0 |
| **3 rigs** | **$2 / mo** | $9 / mo | $4–$6 / mo | **$84 / yr** |
| **5 rigs** | **$4 / mo** | $15 / mo | $8–$10 / mo | **$132 / yr** |
| **10 rigs** | **$9 / mo** | $30 / mo | $18–$20 / mo | **$252 / yr** |
| **25 rigs** | **$24 / mo** | $75 / mo | $48–$50 / mo | **$612 / yr** |

> On Essaim OS, credits are deducted in **Worker-Days** only on days an additional worker actually mines — if your farm pauses during expensive electricity hours or summer heat, you pay nothing for idle days.

Try the interactive cost simulator and live 5-GPU dashboard demo at [**essaimos.com/en/compare**](https://essaimos.com/en/compare) and [**essaimos.com/en/demo**](https://essaimos.com/en/demo).
