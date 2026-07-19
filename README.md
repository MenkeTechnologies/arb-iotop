```
██╗ ██████╗ ████████╗ ██████╗ ██████╗ 
██║██╔═══██╗╚══██╔══╝██╔═══██╗██╔══██╗
██║██║   ██║   ██║   ██║   ██║██████╔╝
██║██║   ██║   ██║   ██║   ██║██╔═══╝ 
██║╚██████╔╝   ██║   ╚██████╔╝██║     
╚═╝ ╚═════╝    ╚═╝    ╚═════╝ ╚═╝     
                                      
```

[![arb](https://img.shields.io/badge/arb-package-05d9e8?style=flat-square)](https://github.com/MenkeTechnologies/arb)
![type](https://img.shields.io/badge/self--sourcing-dashboard-9b5de5?style=flat-square)
![license](https://img.shields.io/badge/license-MIT-ff2a6d?style=flat-square)

### `LIVE PER-PROCESS DISK I/O, STRAIGHT FROM IOTOP`

> *"Every process, ranked by the bytes it drags off the disk."*

A self-sourcing `arb` dashboard for per-process disk I/O. It runs `iotop -b -n 1` on its own timer and renders the resulting per-process read/write breakdown as a live list — nothing needs to be piped in. This is an `arb` package: a dashboard for [`arb`](https://github.com/MenkeTechnologies/arb), the pipeline-to-TUI language on the fusevm bytecode VM.

### [`arb`](https://github.com/MenkeTechnologies/arb) &middot; [`arb-registry`](https://github.com/MenkeTechnologies/arb-registry)

---

## Table of Contents

- [\[0x00\] Overview](#0x00-overview)
- [\[0x01\] Install](#0x01-install)
- [\[0x02\] Usage](#0x02-usage)
- [\[0x03\] The Spec](#0x03-the-spec)
- [\[0x04\] How It Works](#0x04-how-it-works)
- [\[0xFF\] License](#0xff-license)

## [0x00] OVERVIEW

`iotop` is a self-sourcing `arb` dashboard that surfaces per-process disk I/O. It shells out to `iotop -b -n 1` on a fixed interval and renders the batch snapshot as a scrolling list, so you get a continuously refreshed view of which processes are actually hitting the disk without wiring up any external feed.

## [0x01] INSTALL

```sh
arb install iotop    # from the arb-registry git index
arb search iotop
```

## [0x02] USAGE

```sh
arb iotop                       # run it (self-sourcing)
arb iotop --serve               # browser dashboard
arb iotop --html > iotop.html   # static HTML
```

## [0x03] THE SPEC

```arb
# iotop — Per-process disk I/O from iotop, self-sourced from `iotop -b -n 1`.
! iotop -b -n 1 every 10s
list .iotop
source .iotop { in }
grid .iotop -row 0 -col 0
```

- `! iotop -b -n 1 every 10s` — the self-source: `arb` runs `iotop -b -n 1` (batch mode, a single sample) every 10 seconds and feeds its stdout into the `.iotop` stream. No piping required.
- `list .iotop` — declares a `list` widget bound to the `.iotop` stream; each incoming line becomes a row.
- `source .iotop { in }` — the query pipeline for the stream; `in` passes the raw source lines through into `.iotop` as-is.
- `grid .iotop -row 0 -col 0` — places the `.iotop` widget at row 0, column 0 of the dashboard grid.

## [0x04] HOW IT WORKS

The `!` self-source fires `iotop -b -n 1` on the 10-second timer and pipes its stdout into the stream; the `source .iotop { in }` pipeline shapes that data and the `list` widget renders it at its grid cell. `arb` lowers the pipeline compute to the fusevm bytecode VM with Cranelift JIT, so the render loop stays cheap even as samples keep arriving.

## [0xFF] LICENSE

MIT.
