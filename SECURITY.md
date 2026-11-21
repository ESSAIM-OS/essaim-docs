# Security Policy — Essaim OS

## Security Architecture

Essaim OS is designed around a **local-first, zero-cloud** security model:

- **Private LAN Only (RFC 1918)**: Hub-to-agent, Hub-to-ASIC, and Hub-to-Shelly communications are strictly restricted to private IPv4 LAN ranges (`10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`) to prevent SSRF and public Internet exposure.
- **Zero Open Router Ports**: Remote access uses an embedded WireGuard-based Tailscale tunnel (`tsnet` in the Android companion app) and outbound-only Telegram long polling (`getUpdates`).
- **Signed Releases & OTA Verification**: Official disk images, Docker images, and Hub/agent OTA bundles are distributed with `SHA256SUMS` and detached **Ed25519** signatures (`SHA256SUMS.sig`) verified before activation, with automatic rollback on failed boot or service crash.
- **Custom Miner Sandboxing**: Third-party custom miners run under an unprivileged account inside isolated Linux namespaces with `nftables` rules blocking access to loopback services and private LAN hosts.
- **Hardened Maintenance SSH**: On Hub `.img.xz`/`.qcow2` and bare-metal USB rigs, direct `root` SSH login is disabled (`PermitRootLogin no`), SSH is limited to local LAN/Tailscale, and the default maintenance account (`essaimos`) enforces a password change on first login.
- **No Credential Exposure**: Worker bearer tokens, Telegram bot tokens, Tibber API tokens, and local Shelly plug passwords are never returned in plaintext by the Hub API.

## Reporting a Vulnerability

If you discover a security vulnerability in Essaim OS, its installers, or any `ESSAIM-OS` open-source Linux hardware driver:

1. **Do not** disclose exploitable details in a public GitHub issue.
2. Use GitHub's **Private Vulnerability Reporting** (Security tab → *Report a vulnerability*) on this repository, or contact the Essaim OS team via [essaimos.com](https://essaimos.com).
3. Include affected version, reproduction steps, and hardware/OS environment. We aim to acknowledge reports within 48 hours.
