# peek

A one-shot snapshot of what your Mac is doing right now. Type `peek` and after
about a second you get CPU, RAM, GPU and SSD usage, each with the top five
culprits. No TUI to quit, no sudo.

```
  peek  MacBook-Pro · 09:30:15 · 1188 procs · up 22h 30m
╭─ 🧠 CPU ─────────────────────── 21% ─╮ ╭─ 🐏 RAM ──────────────── 25.1/32 GB ─╮
│ ███████▏░░░░░░░░░░░░░░░░░░░░░░░░░░░░ │ │ ███████████████████████████▉░░░░░░░░ │
│ user 14%  sys 8%  load 5.4  10 cores │ │ app 11.6G  wired 4.1G  comp 9.4G     │
│ Safari ×12            ████████▌  66% │ │ Safari ×12            █████████ 8.6G │
│ WindowServer          ████▊      37% │ │ Xcode ×4              ███▍      2.2G │
│ ffmpeg                ███▉       30% │ │ Slack ×6              ███▏      2.1G │
╰──────────────────────────────────────╯ ╰──────────────────────────────────────╯
╭─ 🎮 GPU ─────────────────────── 25% ─╮ ╭─ 💾 SSD ──────────────── 586/995 GB ─╮
│ █████████░░░░░░░░░░░░░░░░░░░░░░░░░░░ │ │ █████████████████████▋░░░░░░░░░░░░░░ │
│ memory in use 858M                   │ │ ↓ 182K/s  ↑ 306K/s  free 409 GB      │
│ WindowServer          ███▋     13.9% │ │ mds_stores                    167K/s │
│ Safari ×12            █▌        5.7% │ │ Slack ×6                       18K/s │
╰──────────────────────────────────────╯ ╰──────────────────────────────────────╯
```

In a terminal the bars are coloured green, yellow or red by load.

## What it shows

- **CPU**: total use, user/system split, load average, and the processes using
  the most CPU over the sample window.
- **RAM**: memory used the way Activity Monitor counts it (app + wired +
  compressed), swap, memory pressure, and the biggest memory users.
- **GPU**: overall utilization, GPU memory in use, and each app's share of GPU
  time.
- **SSD**: space used and free, current read/write throughput, and the
  processes doing the disk I/O.

Helper processes are grouped under their app, so 50 browser helpers show up as
one line with `×50`.

## Install

Requires macOS on Apple Silicon and Python 3 (no packages needed).

```sh
curl -fsSL https://raw.githubusercontent.com/vedntp/peek/main/peek -o ~/.local/bin/peek
chmod +x ~/.local/bin/peek
```

Any directory on your `PATH` works in place of `~/.local/bin`. The section icons
are emoji, so no special font is needed.

## Usage

```
peek             # snapshot with the top 5 per resource
peek -n 10       # top 10 instead
peek -i 2        # sample over 2 seconds for steadier numbers
peek --json      # raw numbers for scripts
```

Set `NO_COLOR=1` to turn off colour.

## How it works

peek takes two readings about a second apart and reports the difference.

- Per-process CPU, memory and disk I/O come from `proc_pid_rusage` (via
  `ctypes`). Root-owned processes can't be read without sudo, so their CPU and
  memory fall back to `ps` and their disk I/O isn't counted.
- Per-app GPU time comes from the `AGXDeviceUserClient` entries in `ioreg`;
  overall GPU utilization from `IOAccelerator`.
- System CPU comes from `host_statistics`, memory from `vm_stat`, and disk
  throughput from `IOBlockStorageDriver`.

## License

MIT
