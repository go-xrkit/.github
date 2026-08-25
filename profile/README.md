<p align="center"><img src="https://raw.githubusercontent.com/go-xrkit/brand/main/social/go-xrkit.png" alt="go-xrkit" width="720"></p>

# go-xrkit

🌐 **[Website](https://go-xrkit.github.io)** · 📚 **[Documentation](https://go-xrkit.github.io/docs/)**

[![License](https://img.shields.io/badge/license-BSD--3--Clause-blue.svg)](https://github.com/go-xrkit/xrkit/blob/main/LICENSE)
[![Website](https://img.shields.io/badge/website-go--xrkit.github.io-0284c7)](https://go-xrkit.github.io)
[![Docs](https://img.shields.io/badge/docs-mkdocs--material-0284c7)](https://go-xrkit.github.io/docs/)
[![Pure Go](https://img.shields.io/badge/pure%20Go-CGO%3D0-00ADD8?logo=go&logoColor=white)](https://github.com/go-xrkit/xrkit)

**The geometry an immersive video player and an XR virtual desktop need, as
pure Go:** orientation, stereo frame packing, the projections that turn a flat
frame into a world you can look around in, the 360° band screens sit on, and
a graded catalogue of what each headset can actually show — plus the player,
the desktop and the Android capture that put it to use on real XR glasses.

`CGO_ENABLED=0`, one third-party module (HCL, and only for the glasses
catalogue), 100% statement coverage, and every package testable without a
headset attached — which is the point. Sign and
axis-order mistakes are invisible in a still frame and awful to wear, so they
are pinned by tests against known directions rather than discovered by
putting the glasses on.

## Repositories

| Repo | What it is |
|------|------------|
| [**xrkit**](https://github.com/go-xrkit/xrkit) | the geometry: `pose` · `stereo` · `projection` · `warp` · `ribbon` · `glasses`, 100% coverage, 6 architectures |
| [**player**](https://github.com/go-xrkit/player) | `xrplay` — hardware decode, per-eye reprojection, full screen on the glasses' own display (macOS) |
| [**desk**](https://github.com/go-xrkit/desk) | several computer screens on a 360° ribbon, scrolled from the keyboard — real virtual displays macOS extends the desktop onto |
| [**android**](https://github.com/go-xrkit/android) | screen capture on Android as a CGO-free Go binary talking to a small Java host, and a documented account of the four ways Android 15 refuses to let an app create a display it can launch onto |
| [**docs**](https://github.com/go-xrkit/docs) | MkDocs Material documentation, served at [/docs/](https://go-xrkit.github.io/docs/) |
| [**go-xrkit.github.io**](https://github.com/go-xrkit/go-xrkit.github.io) | the Hugo landing page |
| [**brand**](https://github.com/go-xrkit/brand) | brand assets — logos & icons |

## Packages (`xrkit`)

| Package | Purpose |
|---------|---------|
| `pose`       | quaternions, the Euler convention head trackers report, recentring, smoothing |
| `stereo`     | how a frame packs two eyes — mono, side-by-side, top-bottom |
| `projection` | flat / equirectangular (360×180, VR180 180×180) / equidistant fisheye |
| `warp`       | the projection precomputed into a lookup table — both eyes at 4K cost ~3 ms, at 8K ~8 ms, zero allocations, against a 16.6 ms budget at 60 Hz |
| `ribbon`     | screens on a 360° band, composited by yaw — a yaw is a horizontal shift on an equirectangular panorama, which is why turning the band never rebuilds the 56.5 ms warp table |
| `glasses`    | which display is the headset, and how wide the view is — every entry graded `Observed` / `Enumerated` / `Published`, and a user catalogue in HCL so a wrong figure can be corrected without waiting for a release |

## Why there is no SDK here

XR glasses expose their 3D mode as an ordinary **display mode** — a VITURE
Beast reports 3840x1080 for side-by-side 3D and 1920x1200 for plain 2D. There
is no XR SDK anywhere in the stereo path: it is arithmetic on orientation, eye
layout and projection. And there is no head tracking yet, because the VITURE
Beast's IMU is not reachable over HID — its interfaces open and accept
commands, and emit nothing, proven against a control run that captured 481
reports from the same reader on other devices.

## Principles

- **Pure Go, `CGO_ENABLED=0`.** No XR SDK, no native shims.
- **One third-party dependency** in `xrkit`: HCL, for the user glasses catalogue, because a figure about a headset is only worth something with its provenance attached and HCL has *comments*. The geometry packages themselves pull in nothing.
- **100% test coverage**, including error branches, gated in CI.
- **Six architectures.** Build + test on all six of Go's 64-bit targets —
  amd64 / arm64 natively, loong64 / riscv64 / ppc64le / s390x under qemu.
- **BSD-3-Clause** on every source file.

## Status

`xrkit`'s six packages (`pose`, `stereo`, `projection`, `warp`, `ribbon`,
`glasses`) are complete and gated at 100% coverage. `player` plays
360°/VR180/3D files on XR glasses today, macOS only, with no head tracking and
no Matroska demuxing. `desk` puts virtual screens on the ribbon on macOS.
`android` captures on Android.

Every repository here states what it has **not** proven, and means it: no XR
glasses were ever attached to an Android device for `android`; the VITURE Luma
Ultra was enumerated over USB only; whole-display capture on macOS is not yet
proven, because the Screen Recording grant could not be obtained on the machine
this was built on. If you want a figure measured rather than quoted, **send us
the hardware**. See the
[roadmap](https://go-xrkit.github.io/docs/latest/roadmap/).
