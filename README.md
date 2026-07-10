# 🤖 ROZ NanoBots v5

A self-healing Linux daemon that automatically detects and fixes system issues — like Iron Man's nanobots, but for your PC.

## What It Does

ROZ NanoBots runs in the background and automatically:

| Module | Description |
|---|---|
| Package Repair | Fixes broken dpkg/apt packages and stale locks |
| System Updates | Keeps packages up to date |
| Kernel Health | Verifies kernel image, initramfs, and modules — reinstalls if missing |
| GRUB Recovery | Regenerates bootloader config |
| GPU Healing | Detects and reloads crashed NVIDIA/AMD GPU drivers |
| SMART Monitoring | Watches disk health for failing sectors and SMART failures |
| Filesystem Check | Detects read-only root, schedules fsck on errors |
| Fstab Validation | Verifies and mounts missing fstab entries |
| Disk Cleanup | Emergency cleanup when disk > 90% full, removes old kernels |
| Inode Check | Prevents inode exhaustion |
| Service Healing | Restarts failed/crashed systemd services with retry limits |
| Critical Services | Monitors essential services (journald, logind, dbus, cron) |
| Zombie Killer | Finds and kills zombie processes |
| Runaway Process Killer | Kills processes stuck at >95% CPU for >5 minutes |
| OOM Protection | Detects out-of-memory events and tunes kernel |
| Memory Management | Clears caches when memory is critically low |
| Swap Recovery | Re-enables swap or creates emergency swapfile |
| Thermal Monitoring | Watches all CPU/GPU thermal zones |
| Network Repair | Restarts networking and brings up interfaces if connectivity drops |
| DNS Fallback | Fixes broken DNS with systemd-resolved restart and fallback nameservers |
| Security Audit | Detects SSH brute force, world-writable files, SUID in temp dirs |
| Firewall Check | Warns if UFW/iptables is inactive |
| Time Sync | Re-enables NTP if clock is drifting |
| Permission Healing | Fixes critical file permissions (/etc/shadow, /tmp, etc.) |
| Kernel Panic Detection | Catches critical kernel messages |
| Crash Recovery | Cleans crash dumps from /var/crash |
| Log Rotation | Prevents /var/log from filling up |
| Stats Tracking | Logs every fix for lifetime statistics |

## Installation

```bash
git clone https://github.com/zanicool/ROZ-nanobots-for-your-pc-.git
cd ROZ-nanobots-for-your-pc-
sudo ./install.sh
```

This installs NanoBots to `/opt/nanobot` as a systemd service that:

- Starts automatically on boot (early boot stage)
- Waits for network before running
- Restarts itself in 5 seconds if it crashes
- Shuts down gracefully on SIGTERM
- Runs a full healing cycle every hour with quick checks every 30 seconds

## Usage

```bash
# Check service status
sudo systemctl status nanobot

# View live logs
sudo journalctl -u nanobot -f

# View lifetime stats dashboard
python3 /opt/nanobot/nanobot.py status

# Run a one-shot full heal
sudo python3 /opt/nanobot/nanobot.py heal

# Run a quick check
sudo python3 /opt/nanobot/nanobot.py quick

# Generate default config
sudo python3 /opt/nanobot/nanobot.py config
```

## Uninstall

```bash
sudo ./uninstall.sh
```

## Requirements

- Linux (Debian/Ubuntu based)
- Python 3.6+
- Root access (for system repairs)

## Configuration

Edit `/etc/nanobot/config.json` (created on install):

| Variable | Default | Description |
|---|---|---|
| `interval` | `3600` | Seconds between full healing cycles |
| `realtime_interval` | `30` | Seconds between quick checks |
| `enable_updates` | `true` | Auto-update packages |
| `enable_security` | `true` | Run security audits |
| `enable_smart` | `true` | SMART disk monitoring |
| `enable_firewall_check` | `true` | Firewall status check |
| `enable_time_sync` | `true` | NTP sync check |
| `enable_permission_heal` | `true` | Fix critical file permissions |
| `critical_services` | `[journald, logind, dbus, cron]` | Services that must always be running |
| `watched_services` | `[]` | Additional services to monitor |
| `max_restart_attempts` | `3` | Max restarts per service per cycle |
| `disk_warn_pct` | `80` | Disk usage warning threshold |
| `disk_crit_pct` | `90` | Disk usage critical threshold |
| `mem_crit_mb` | `200` | Low memory threshold (MB) |
| `temp_warn_c` | `80` | Temperature warning (°C) |
| `temp_crit_c` | `90` | Temperature critical (°C) |
| `max_log_size_mb` | `1000` | Max /var/log size before rotation |

## License

MIT License — see [LICENSE](LICENSE)
