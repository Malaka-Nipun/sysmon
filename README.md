# SysMon

A real-time system resource monitor with two interfaces — a terminal-style **web dashboard** that runs in the browser, and a **Python CLI** tool that reads directly from `/proc` for accurate live metrics.

![License](https://img.shields.io/badge/license-MIT-green) ![Python](https://img.shields.io/badge/python-3.8+-blue) ![No dependencies](https://img.shields.io/badge/dependencies-none-brightgreen) ![Platform](https://img.shields.io/badge/platform-linux%20%7C%20macOS-lightgrey)

---

## Live Demo

**[Malaka-Nipun.github.io/sysmon](https://Malaka-Nipun.github.io/sysmon)**

---

## Features

- Live CPU usage — overall % and per-core breakdown
- Memory — used, free, cached, and swap
- Disk — usage for all mounted filesystems
- Network — real-time RX/TX speed per interface
- Top processes by RAM usage
- Load average (1 min / 5 min / 15 min)
- Uptime display
- Auto alerts when CPU, RAM, or disk hits critical levels
- Sparkline history charts (web UI)
- Zero dependencies — pure Python standard library

---

## Web Dashboard

Open `index.html` in any browser. No install, no server needed.

> **Note:** Browsers cannot read `/proc` directly, so the web dashboard runs on simulated data that behaves like a real system. For accurate real metrics, use the CLI tool.

---

## CLI Tool

### Install

```bash
mv sysmon.py ~/.local/bin/sysmon
chmod +x ~/.local/bin/sysmon
```

Add to PATH if needed (add to `~/.bashrc` or `~/.zshrc`):

```bash
export PATH="$HOME/.local/bin:$PATH"
```

### Usage

```bash
# One-shot snapshot
sysmon

# Output as JSON (pipe-friendly)
sysmon --json

# Plain text, no colors
sysmon --no-color

# Save to file
sysmon --no-color > stats.txt

# Pipe to jq
sysmon --json | jq .cpu
```

### Options

| Flag | Default | Description |
|------|---------|-------------|
| `--json` | off | Output all metrics as JSON |
| `--no-color` | off | Disable ANSI color output |
| `--interval` | 0.5s | CPU measurement window in seconds |

### Example output

```
  sysmon  uptime 02h 14m 37s  ·  load 0.82 0.74 0.61  ·  2026-06-11 14:22:01

  CPU
  ────────────────────────────────────────────
  overall       ████████░░░░░░░░░░░░  42.3%
  core 0        ██████░░░░░░░░░░  38.1%
  core 1        ████████░░░░░░░░  47.5%

  Memory
  ────────────────────────────────────────────
  RAM           ██████████░░░░░░░░░░  51.2%   4.1 GB / 8.0 GB
  cached        1.2 GB

  Disk
  ────────────────────────────────────────────
  /             ████████████░░░░░░░░  61.0%   155.6 GB / 256.0 GB

  Network
  ────────────────────────────────────────────
  eth0          ↓    1.2 MB/s  ↑    320.4 KB/s  total ↓14.3 GB ↑2.1 GB

  Top processes (RAM)
  ────────────────────────────────────────────
  PID     NAME                  STATE          RSS
  1842    chrome                S          512.3 MB
  2310    node                  S          210.7 MB
  891     postgres              S          180.2 MB
```

---

## How it works

The CLI reads system data directly from Linux's `/proc` virtual filesystem — no third-party libraries like `psutil` needed.

| Metric | Source |
|--------|--------|
| CPU | `/proc/stat` — measures tick delta over 0.5s |
| Memory | `/proc/meminfo` |
| Disk | `os.statvfs()` per mount |
| Network | `/proc/net/dev` |
| Processes | `/proc/[pid]/stat` + `/proc/[pid]/status` |
| Load / Uptime | `/proc/loadavg`, `/proc/uptime` |

---

## Roadmap

- [ ] Phase 2 — live `--watch` dashboard using `curses`
- [ ] Phase 3 — per-process CPU tracking with delta
- [ ] Phase 4 — threshold alerts + CSV logging

---

## Files

| File | Description |
|------|-------------|
| `index.html` | Web dashboard — open in any browser |
| `sysmon.py` | Python CLI tool |

---

## License

MIT — free to use, modify, and distribute.
