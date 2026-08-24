# Changelog

All notable changes to m0play are tracked here. Dates are `YYYY-MM-DD`.

## [1.0.0-beta] - 2026-08-24

First public beta - the work that took the player from a rough prototype
to a release, condensed from the internal session history.

### Playback core
- Fixed a discontinuity-triggered use-after-free (ad breaks / variant
  switches) and several threading races around stream open, artwork
  loading and audio submission.
- A/V sync now uses the audio clock as master (present-or-repeat/drop per
  frame); long sessions no longer accumulate drift or trigger hard
  recoveries.
- Fixed a preroll deadlock, wrong frame colors (GPU row-alignment), a
  pause watchdog bug that turned any longer pause into a buffering error,
  and pad-disconnect / EOF handling.
- Enabled HTTP keep-alive for playlist and segment fetches - previously
  every request paid a fresh TCP + TLS handshake, the largest single
  latency cost when opening streams.
- Real-time 1080p60 H.264 software decode validated on hardware across
  MP4, HLS/ABR, HE-AAC, PTS-discontinuity and audio-only streams.

### Audio
- DualShock 4 speaker output, toggled from the playback bar, running in
  parallel to unchanged TV audio (uses the undocumented PadSpk audio
  port; open-once-per-stream strategy to respect a firmware port limit).

### Security
- TLS certificate verification enabled on both network stacks, backed by
  a bundled Mozilla CA store.
- FFmpeg protocol whitelist restricted to http/https/tcp/tls.
- LAN configuration web server hardened (PIN, origin/method checks);
  debug test URLs removed.

### UI
- Full GUI overhaul: box/anchor layout system, channel-browser card grid
  with per-channel artwork (M3U8 `tvg-logo`) and disk cache, redesigned
  HUD and dialogs, soft shadows, subtle fade transitions.
- Text and icons render from higher-resolution SDF atlases (crisp at all
  sizes); icon set is custom-drawn for the project.
- Marquee scrolling for long channel titles on the focused card.
- Playback-bar times draw on a fixed digit grid, so the progress bar no
  longer shifts as digits change.

### Performance
- Removed per-frame diagnostic overhead from the frame loop - measured on
  hardware at 5-6 dropped frames per 10 s at 1080p60 before the fix,
  0-1 after.

### Build & release
- The FFmpeg 8.0.1 / mbedTLS 3.6.5 static libraries are reproducible:
  the exact build recipe ships as `build-recipe/` in every release zip,
  alongside `LICENSES/` and the LGPL relink objects (`relink/`).
- App name **m0play**, version **1.0.0-beta** (PARAM.SFO `01.00`);
  `TITLE_ID`/`CONTENT_ID` unchanged for install continuity.

### Known limitations
- Firmware coverage beyond 11.00 is unverified - please file a firmware
  report (issue template).
- Streams with many ABR variants can take several seconds to open;
  faster async opening is planned post-beta.
- Zero-copy GPU video upload is deferred to post-1.0.
