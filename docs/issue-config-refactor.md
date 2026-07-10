# Refactor config.json: replace 78 `enable_` flags with grouped modules

## Problem

The current `config.json` has **78 individual `enable_` boolean flags**. This is hard to read, hard to maintain, and makes it difficult to apply presets (e.g. "headless server" vs "desktop").

Example of current state:

```json
{
  "enable_updates": false,
  "enable_security": true,
  "enable_smart": true,
  "enable_firewall_check": true,
  "enable_time_sync": true,
  "enable_permission_heal": true,
  "enable_docker": true,
  "enable_usb_monitor": true,
  "enable_xorg_heal": true,
  "enable_audio_heal": true,
  "enable_bluetooth_heal": true,
  ...73 more flags...
}
```

## Proposal

Group checks into logical modules with a single enable/disable per group:

```json
{
  "profile": "server",
  "interval": 3600,
  "realtime_interval": 30,

  "modules": {
    "network": {
      "enabled": true,
      "intrusion_detection": true,
      "arp_spoof_detect": true,
      "dns_leak_check": true,
      "speed_check": true,
      "mac_spoof_detect": true
    },
    "storage": {
      "enabled": true,
      "smart": true,
      "smart_selftest": true,
      "disk_latency": true,
      "disk_scheduler": true,
      "failed_mount_retry": true,
      "warn_pct": 80,
      "crit_pct": 90
    },
    "docker": {
      "enabled": true
    },
    "desktop": {
      "enabled": false,
      "xorg": true,
      "audio": true,
      "bluetooth": true,
      "display_manager": true,
      "screen_lock": true,
      "font": true
    },
    "security": {
      "enabled": true,
      "firewall": true,
      "ssh_harden": true,
      "permission_heal": true,
      "rootkit_check": true,
      "config_watchdog": true,
      "apparmor": true
    },
    "hardware": {
      "enabled": true,
      "usb_monitor": true,
      "battery": false,
      "fan_monitor": true,
      "lid_switch": false,
      "acpi": true
    },
    "system": {
      "enabled": true,
      "updates": false,
      "time_sync": true,
      "kernel_module_check": true,
      "sysctl_heal": true,
      "cron_heal": true,
      "tmpfiles": true
    }
  },

  "profiles": {
    "server": {
      "desktop.enabled": false,
      "hardware.battery": false,
      "hardware.lid_switch": false,
      "disk_warn_pct": 95
    },
    "desktop": {
      "desktop.enabled": true,
      "hardware.battery": true
    },
    "minimal": {
      "modules_enabled": ["network", "storage", "system"]
    }
  }
}
```

## Benefits

1. **Readability** — grouped by concern, not a flat list of 78 booleans
2. **Presets/profiles** — `"profile": "server"` disables all desktop stuff in one line
3. **Module-level disable** — `"desktop": {"enabled": false}` skips all desktop checks without listing each one
4. **Extensibility** — new checks go into their module, no config migration needed
5. **Documentation** — each module can have its own README section

## Migration path

1. Keep flat `enable_` flags working (backward compat)
2. Add module-based config as alternative
3. If both exist, module config wins
4. Add `nanobot.py config --migrate` to convert old → new format

## Alternatives considered

- **TOML/YAML instead of JSON** — less noisy syntax, but adds dependency
- **Separate config files per module** — too fragmented for a single-binary tool
- **CLI flags** — doesn't persist, not suitable for daemon

## Context

When configuring ROZ for a headless server (Intel NUC), I had to manually set 15+ flags to `false` for desktop features that don't apply. A `"profile": "server"` would have been one line.
