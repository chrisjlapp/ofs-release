# Open Fabric Studio — Release Notes v0.9.2

**Release Date:** 2026-03-20

---

## Overview

v0.9.2 expands the platform with edge device inventory and topology support, introduces inter-VLAN routing intent management, improves discovery and deployment flows, and adds new system settings for debugging and OUI vendor overrides.

---

## What's New

### Edge Device Management
- Add dedicated edge device inventory APIs, persistence, and UI workflows for creating, editing, and deleting edge devices
- Render edge devices directly in topology and edge-configuration views so operator workflows can include non-switch endpoints
- Persist learned edge identity details to improve topology accuracy and day-two visibility

### Inter-VLAN Routing Intent
- Add an inter-VLAN routing modal and intent model updates for managing routed SVI relationships from the UI
- Extend staging, validation, and config export/import flows so inter-VLAN routing settings travel with the project intent
- Improve loopback and SVI helpers used by routing workflows to keep generated intent data consistent

### Discovery, Deployment, and Connectivity Improvements
- Expand discovery-engine and deployment handling for newer intent shapes, edge-aware workflows, and improved device state synchronisation
- Add NETCONF connection manager support to stabilise backend connectivity behaviour during repeated operations
- Improve device and settings APIs so system-wide configuration is persisted more reliably

### Settings and Diagnostics
- Add backend-backed system settings management, including debug-mode persistence
- Add support for user-managed OUI vendor overrides to improve endpoint labelling and troubleshooting
- Introduce a debug devices page to assist with validation and support workflows

### Version Update
- Update the application UI version label to `v0.9.2`

---

## Bug Fixes

| Area | Fix |
|------|-----|
| Edge interface staging | Correct staged edge interface updates so intent diffs include the expected changes |
| Config export/import | Align bundle handling with the expanded intent and settings model |
| Device management | Improve credential and device record handling during edits and rediscovery |
| Topology | Improve link, handle, and node rendering for edge-aware topology layouts |

---

## Upgrade Notes

Upgrade from v0.8.6a or earlier with the standard container refresh workflow:

```bash
docker compose pull
docker compose up -d
```



---

## Known Limitations

- NX-OS QoS and DHCP configuration are not yet supported (planned for a future release).
- Bulk device import via CSV is not yet available.
- The renderer bundle exceeds 500 kB — code-splitting is planned for a future optimisation release.

---

# Open Fabric Studio — Release Notes v0.8.6a

**Release Date:** 2026-03-18

---

## Overview

v0.8.6a is a maintenance update focused on cleaner topology discovery, safer IOS-XE intent deployment, better brownfield intent import from live devices, and more control over which interfaces appear in the topology map.

---

## What's New

### Brownfield VLAN, SVI, and Loopback Discovery
- Expand brownfield discovery so existing device VLANs, SVIs, and Loopbacks are merged into the global network intent
- Improve parsing and persistence for discovered SVIs and Loopbacks so real device state is reflected more reliably after discovery
- Preserve discovered management-facing SVI information while pruning stale orphaned Loopback intent entries

### Edge Port VLAN Dropdown Selection
- Replace manual access VLAN number entry in the edge-port workflow with a dropdown populated from the global VLAN intent
- Make edge-port staging faster and reduce invalid VLAN assignment mistakes during access-port configuration

### Per-Device Topology Interface Exclusions
- Add per-device interface exclusion patterns so management or unused ports can be omitted from topology and ISL views
- Support exact interface names and wildcard patterns (for example `GigabitEthernet1/0/*`)
- Preserve exclusion settings in device credentials and configuration export bundles

### Improved Device Configuration UX
- Add dedicated controls in the Add Device and Edit Device dialogs for managing manual interface exclusions
- Hide the exclusion textarea until the option is enabled to reduce visual clutter
- Continue to keep `GigabitEthernet0/0` excluded by default from topology rendering

### Version Update
- Update the application UI version label to `v0.8.6a`

---

## Bug Fixes

| Area | Fix |
|------|-----|
| Topology | Exclude matching interfaces from rendered topology links and ISL selections |
| Discovery | Prune orphaned loopback intents discovered on devices so stale loopbacks are removed cleanly |
| IOS-XE deployment | Restore SVI no-shutdown deployment handling so active SVIs come up correctly |
| Discovery / UI sync | Remove an unnecessary discovery intent refresh workaround from the WebSocket flow |

---

## Upgrade Notes

Upgrade from v0.8.5 or earlier with the standard container refresh workflow:

```bash
docker compose pull
docker compose up -d
```

No manual data migration is required for this update.

---

## Known Limitations

- NX-OS QoS and DHCP configuration are not yet supported (planned for a future release).
- Bulk device import via CSV is not yet available.
- The renderer bundle exceeds 500 kB — code-splitting is planned for a future optimisation release.

---

# Open Fabric Studio — Release Notes v0.8.5

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
