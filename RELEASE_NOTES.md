# Open Fabric Studio — Release Notes v0.8.5c (ALPHA)

**Release Date:** 2026-03-17

---

## Overview

v0.8.5 is the first public release of Open Fabric Studio Pro AV Edition.  It delivers full dual-platform support (IOS-XE and NX-OS), a complete configuration import/export workflow, and dozens of deployment fixes validated against real hardware.

---

## What's New

### NX-OS Support (Cisco Nexus)
- Full NETCONF-based configuration deployment for NX-OS devices
- Interface discovery: ports, status, speed, LLDP/CDP neighbors, MAC table
- VLAN creation and access/trunk port assignment
- IGMP snooping — per-VLAN enable/disable, fast-leave, global version
- PTP boundary clock configuration
- Port-channel (LAG) creation and member assignment
- Inter-Switch Link (ISL / trunk) configuration
- Loopbacks and SVIs (Layer 3 VLAN interfaces)
- `copy running-config startup-config` commit step

### Multi-Device OS Selection
- Per-device OS type selector (IOS-XE / NX-OS) in the Edit Device modal
- Discovery engine automatically persists the detected OS type
- Deployment pipeline routes RPCs to the correct platform builder

### Configuration Import / Export
- Export a full JSON bundle containing all devices (with credentials) and the global network intent
- Import a bundle to restore configuration on a fresh installation
- Available in Settings → Configuration Management

### Unstage / Discard Staged Changes
- New "Discard Staged Changes" action restores the intent to the pre-stage snapshot
- Prevents accidental deployment of partially-edited intents
- Duplicate-intent validation added to staging workflow

---

## Bug Fixes

| Area | Fix |
|------|-----|
| IOS-XE deployment | Resolve VLAN deployment failures; auto-stage newly added devices |
| NX-OS IGMP | Isolate IGMP version RPC from snooping RPCs; align with hardware examples |
| NX-OS IGMP | Send VLAN config mode before snooping RPCs; add `nc:operation=merge` |
| NX-OS ISL | Use short-form interface name (`Eth1/1` not `Ethernet1/1`) in PhysIf tDn |
| NX-OS port-channels | Use short-form interface name in port-channel member tDn |
| Topology | Prevent CDP from downgrading an LLDP link on mixed IOS-XE/NX-OS links |
| Topology | Fetch real hostname from device during discovery (replaces CDP stub) |
| Topology | Emit `topology:update` after device deletion so node disappears immediately |
| Topology | Normalise NX-OS `eth→Ethernet` port names to prevent duplicate links |
| Topology | Solid cyan for UP links, red dashed for DOWN links |
| Topology | Only render port handles for connected ports |
| Deployment | Remove `copy running-config startup-config` from the deploy path (use Commit) |
| Deployment | Make commit non-fatal on NX-OS |
| Settings | Require typing `reset` to confirm configuration wipe |
| Admin | Return readable error message on user creation validation failure |

---

## Upgrade Notes

This is the initial public release.  No migration is required.

For future upgrades from v0.8.5:

```bash
docker compose pull
docker compose up -d
```

---

## Known Limitations

- NX-OS QoS and DHCP configuration are not yet supported (planned for a future release).
- Bulk device import via CSV is not yet available.
- The renderer bundle exceeds 500 kB — code-splitting is planned for a future optimisation release.
- Currently working on PTP deployment

---

## Quick Reference

| Command | Purpose |
|---------|---------|
| `./install.sh` | First-time setup |
| `docker compose pull && docker compose up -d` | Upgrade to a newer release |
| `docker compose ps` | Check service status |
| `docker compose logs -f` | View live logs |
| `docker compose down` | Stop all services (data preserved) |
| `docker compose down -v` | Stop and delete all data (irreversible) |

---

## Files in This Release

| File | Description |
|------|-------------|
| `docker-compose.yml` | Production stack definition (pinned to `v0.8.5` images) |
| `install.sh` | One-shot installer — generates secrets, pulls images, starts stack |
| `.env.example` | Reference for all supported environment variables |
| `README.md` | Deployment guide |
| `RELEASE_NOTES.md` | This file |
