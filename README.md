# m0play

Video player for PS4 homebrew: HLS/IPTV live streams and MP4 playback,
built on FFmpeg, with a controller-driven GLES2 interface.

**Status: 1.0.0-beta.** Ran a multi-hour soak (MP4 up to 1080p60, HLS ABR,
HE-AAC, PTS discontinuities, repeated pause/resume and channel zapping)
without errors on firmware 11.00. Wider firmware coverage relies
on your reports - template below.

## Features

- MP4 (H.264/AAC) and HLS, including master playlists / ABR variants
- HE-AAC, PTS discontinuities (ad insertion), audio-only streams
- HTTPS with certificate verification against a bundled CA store
- M3U8 playlists: `group-title` categories, `tvg-logo` channel artwork
- Card-grid UI, focus navigation, marquee for long titles
- DualShock 4 speaker output, toggled from the playback bar
- Configuration via on-screen keyboard or a PIN-protected LAN web interface (see Configuration)

## Technical notes

- FFmpeg 8.0.1 does demuxing and software decoding; H.264 High 1080p60
  plays in real time on the CPU (no hardware decoder involved).
- A/V sync uses the audio clock as master with present-or-repeat/drop
  per frame; long sessions accumulate no drift.
- TLS is mbedTLS 3.6.5; the handshake path uses the console's AES units.
- Rendering is GLES2 on Piglet; text and icons are SDF atlases, so the UI
  is resolution-independent and sharp at any scale.

## Requirements

- PS4 with **GoldHEN 2.x or newer**
- Firmware: tested on 11.00; expected to run wherever GoldHEN runs. Please file a firmware report either way.

## Installation

1. Download the release zip. Copy `m0play-<version>.pkg` to USB, or push
   it to `/data/pkg/` over FTP.
2. Install via GoldHEN's package installer.
3. Start m0play, press OPTIONS → Settings, set your playlist URL.

## Controls

**Channel browser**

| Button | Action |
|---|---|
| D-Pad | Navigate the card grid |
| Cross | Play the focused channel |
| Circle | Back |
| L1 / R1 | Previous / next category |
| OPTIONS | Settings |

**During playback**

| Button | Action |
|---|---|
| OPTIONS | Show / hide the playback bar |
| Cross | On the playback bar: activate the focused chip (pause/play, mute, DS4 speaker) |
| Triangle | Toggle mute directly |
| D-Pad left / right | Volume −/+ (bar closed) · move chip focus (bar open) |
| L2 / R2 (hold) | Seek backward / forward - release to jump (VOD only) |
| Circle | Stop and return to the browser |

## Configuration

Set the playlist URL either directly on the console (Settings → on-screen
keyboard) or from another device via the **LAN web interface**: enable
"Web server" in Settings, then open `http://<console-ip>:32123` in a
browser on the same network. Changing the URL requires the 6-digit PIN
shown on the TV - the PIN is regenerated on every server start and the
server only runs while you enable it. Settings are stored in
`/data/m0play/config.ini`.

## Firmware compatibility

| Firmware | Status |
|---|---|
| 11.00 | tested (developer console, full soak) |
| others | unknown - report yours |

File a [firmware report](../../issues/new?template=firmware-report.yml)
with firmware + GoldHEN version and whether playback works.

## Credits

m0play stands on other people's work:

- **[OpenOrbis](https://github.com/OpenOrbis/OpenOrbis-PS4-Toolchain)** -
  the entire PS4 homebrew toolchain this project is built with.
- **[GoldHEN](https://github.com/GoldHEN/GoldHEN)** - the homebrew enabler;
  its klog was also our single most important debugging tool.
- **[shadPS4](https://github.com/shadps4-emu/shadPS4)** - emulator whose
  source answered questions Sony's SDK never would (audio port types,
  video-out semantics).
- **flatz** - foundational Piglet/GLES research for the PS4.
- **[FFmpeg](https://ffmpeg.org)** (LGPL v3) and
  **[mbedTLS](https://github.com/Mbed-TLS/mbedtls)** (Apache-2.0) - the
  playback and TLS engines. License texts in `LICENSES/`.
- **[Inter](https://rsms.me/inter/)** by Rasmus Andersson (SIL OFL) - the
  UI typeface; **Mozilla** - the bundled CA certificate store.

UI icons and channel-card artwork are project-own.

## Licenses

FFmpeg is built without `--enable-gpl` (LGPL v3 applies). Each release zip
contains the full license texts (`LICENSES/`), the libraries' exact build
recipe (`build-recipe/`), and the application's link objects (`relink/`)
satisfying the LGPL relinking provision for static builds. The
application's own source is not published at this time.
