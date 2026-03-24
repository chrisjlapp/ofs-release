# Open Fabric Studio — Release Notes v0.10.2

**Release Date:** 2026-03-24

---

## Overview

v0.10.2 builds on v0.9.3 with operator-facing drift detection, more granular deployment controls, expanded PTP support for NX-OS, and a cleaner settings and topology experience.


## What's New Since v0.9.3

### DHCP Discovery and Monitoring Enhancements
- Add brownfield DHCP intent import during discovery so existing pool configuration is captured from live devices
- Support per-pool DHCP server assignment and add a DHCP pool server selector in the intent modal
- Improve DHCP discovery parsing, broaden brownfield get-config filtering, and add safer handling for multi-server monitoring workflows
- Add a dedicated DHCP monitoring switch selector and auto-exclude SVI interface IPs from DHCP pool ranges

### Inter-VLAN Multicast Workflow Improvements
- Add multicast routing controls to the inter-VLAN workflow
- Fix inter-VLAN multicast toggle persistence so changes are retained across edit/save cycles
- Fix SVI PIM toggle visibility and prevent IGMP querier enablement when PIM is selected
- Preserve staged SVI multicast flags and improve deployment UX by hiding remove-staged actions during active deployments

### Configuration Drift Detection, Expanded Coverage, and Platform Fixes
- Add background drift detection that compares both saved intent and live running configuration against the last successful deployment
- Surface drift status in a persistent in-app banner with section-level summaries and per-device detail lines
- Allow operators to resolve detected drift directly from the banner with a full-intent redeploy action
- Expand drift checks to include ISLs and port channels and improve false-positive handling during intent comparisons
- Ignore ISL drift checks for interfaces excluded from topology using management-IP-based matching
- Normalize IOS-XE short/long interface name handling across discovery and telemetry flows
- Fix backend TypeScript type issues in admin/topology intent paths to stabilise builds

### Deployment Controls and Day-Two Operations
- Add Deployment Settings so each major intent section can be deployed in either **Full Push** or **Diff Only** mode
- Persist deployment-mode preferences in the UI so operators can tune change windows and reduce unnecessary pushes
- Improve staged deployment handling so unchanged interfaces are skipped more reliably during edge-port deployments
- Add a link-cleanup utility tab in Devices to help remove stale topology links during troubleshooting
- Expand admin reset behavior to clear edge devices and edge intent data along with baseline configuration state

### Telemetry, Discovery, and Topology Improvements
- Introduce IOS-XE gNMI telemetry ingestion for faster topology and neighbor-state updates
- Improve gNMI CDP/LLDP merge behavior and adjust transport defaults/subscription behavior for broader device compatibility
- Improve brownfield discovery so ISL, port-channel, and edge intent state are synchronized more consistently from discovered devices
- Improve topology auto-fit behavior so the canvas frames devices more consistently on page open and refresh

### PTP Enhancements
- Add per-interface NX-OS PTP deployment support for edge ports and related interface workflows
- Improve the PTP intent model and validation defaults so best-practice and manual modes stage more reliably
- Expand IOS-XE and NX-OS PTP implementation guidance with updated command-reference documentation

### Monitoring and Settings UX
- Reorganize the Settings page into clearer tabbed sections for easier navigation
- Add expandable DHCP monitoring views by pool for active DHCP server switches
- Brighten the application blue palette slightly for better contrast and readability
- Collapse intent-controlled edge interfaces by default to reduce noise in Edge Config workflows

### Platform Reliability Improvements
- Improve IOS-XE device polling by persisting OS type and backfilling detected license levels more reliably
- Normalize DHCP monitoring parsing to handle non-string XML values safely
- Update dependencies and resolve TypeScript and audit issues to stabilize development and release builds

---

## Bug Fixes

| Area | Fix |
|------|-----|
| Drift detection | Correct false-positive VLAN drift results and improve section comparison detail |
| Stage validation | Add defaults for backward-compatible boolean and PTP sub-fields so older intent shapes validate cleanly |
| Error handling | Prevent raw non-string API error objects from surfacing as `[object Object]` in the UI |
| Topology | Fix node-position resets and improve repeated auto-fit behavior on topology updates |
| Connectivity / polling | Improve IOS-XE Smart Licensing detection and preserve discovered device metadata more consistently |

---

## Upgrade Notes

Upgrade from v0.9.3 or earlier with the standard container refresh workflow:

```bash
git pull origin
docker compose pull
docker compose up -d
```

If you rely on persistent backend data, ensure the latest database migrations are applied during startup so drift-tracking metadata is available.

---

## Known Limitations

- Device drift checks currently focus on VLANs, SVIs, and edge ports; PTP, QoS, IGMP, ISLs, port channels, DHCP, and loopbacks are not yet fully read back for drift validation.
- NX-OS QoS and DHCP configuration are not yet supported (planned for a future release).
- Bulk device import via CSV is not yet available.
- The renderer bundle exceeds 500 kB — code-splitting is planned for a future optimisation release.

---

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

If you are self-hosting with a persistent database, ensure the latest Prisma migrations are applied as part of your normal backend startup or deployment workflow.

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
