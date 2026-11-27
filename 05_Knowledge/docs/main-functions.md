# vrspark Main Functions

Distilled from `03_Sources/gemini/nreal-light-s24-xr-architecture-setup-guide.txt` (captured 2026-11-27; see `03_Sources/gemini/README.md`).

## 1. XR hardware & physical architecture

- Treat Nreal Light as a composite USB-C device over S24 Ultra DP Alt Mode: display sink (1920x1080 2D / 3840x1080 SBS 3D @60Hz), HID IMU (1000Hz), dual grayscale SLAM cams, center 5MP UVC RGB cam, USB audio.
- Account for power/thermal/optical constraints: charge-and-play splitter with DP Alt Mode + USB data passthrough; Samsung Game Booster "Pause USB Power Delivery" during heavy workloads; pure-black `#000000` transparent HUD backgrounds; 40px edge safe margin; magnetic light blocker for VR/SBS; protect non-detachable tether.

## 2. OS display/runtime mode management

- Resolve Samsung DeX vs Nebula conflict: disable DeX auto-start, sideload legacy Nebula v3.8.0/v3.8.1 for Nreal Light, grant required permissions, keep battery unrestricted, avoid power-button lock during AR Space (use Nebula screen-dimming touchpad).
- Support explicit XR modes:

| Mode | Runtime | Tracking | Primary use |
|---|---|---|---|
| AR / Spatial MR | Nebula v3.8.x or custom NRSDK 2.3.0 APK | 6DoF | Anchored screens, CV overlays |
| Productivity HUD | Samsung DeX Extend mode | 0DoF | Transparent desktop HUD / PWAs |
| VR / 3D Media | SBS 3840x1080, VLC/Moonlight/Artemis | 0DoF/3DoF | 3D media, low-latency streaming |

## 3. Dual-display productivity isolation

- Use DeX Extend mode (not mirroring). Disable auto touchpad takeover so S24 remains normal touchscreen (Display 0).
- Keep OpenCode Mobile on phone screen, connected over WireGuard/Tailscale to `opencode serve`; do not launch from DeX drawer.
- Launch custom multi-pane UI as borderless installed PWA in Chrome on DeX (Display 1), auto-hide taskbar.
- Prevent link hijacking (default browser on phone for OpenCode links) and lock audio routing/mic path via Quick Settings/Separate app sound.

## 4. Neurosity Crown BCI integration

- Avoid Android WebView Web Bluetooth limitations; use `@neurosity/sdk` over Wi-Fi/cloud WebSockets or backend relay.
- Prefer onboard-computed high-signal streams: `focus()` ~4Hz, `calm()` ~4Hz, `kinesis(label)` event-driven with threshold/debounce, `signalQuality()` ~1Hz, `status()`.
- Avoid streaming raw 8-channel 256Hz EEG over mobile links alongside video/SSE; use processed metrics for HUD/control.
- Map Kinesis triggers (e.g., `leftHandPinch` > 0.85, 1000ms debounce) to hands-free actions like diff approval or pane switching.

## 5. Multimodal fusion with NVIDIA DeepStream

- Use centralized broker (NATS JetStream or MQTT) and DeepStream as synchronization spine; avoid many direct browser WebSockets.
- Ingest Nreal center UVC + mic from S24 as RTSP/SRT/WebRTC into DeepStream (`nvurisrcbin`, `nvinfer`, `nvinferaudio`). Handle Android 15/16+ runtime camera permission before USB open.
- Relay Galaxy Watch Ultra biometrics (Samsung Health Sensor SDK: heart rate continuous + IBI/HRV, skin temp, accelerometer) via Wear OS MessageClient/DataClient to phone, then publish to broker.
- Bridge Neurosity Crown metrics to broker with NTP timestamps.
- Enable `attach-sys-ts=1`; align nearest EEG/watch samples to `NvDsFrameMeta.ntp_timestamp` in pad probe and attach as `NvDsUserMeta`.
- Publish unified HUD state via `nvmsgconv` + `nvmsgbroker` at 10-15 Hz to a single UI feed; HUD can collapse non-urgent notifications during high cognitive load (`focus > 0.70`).
