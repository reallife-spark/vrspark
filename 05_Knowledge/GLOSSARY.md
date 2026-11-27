# Glossary

| Term | Meaning in vrspark |
|---|---|
| Nreal Light / XREAL Light | Tethered AR glasses acting as composite USB-C device (DP Alt Mode display, HID IMU, SLAM cams, UVC RGB cam, USB audio). |
| DP Alt Mode | USB-C DisplayPort Alternate Mode used to drive glasses display from S24 Ultra Adreno GPU. |
| 6DoF | Six degrees of freedom tracking (position + orientation), provided by SLAM + IMU in Nebula/NRSDK paths. |
| 0DoF / 3DoF | Head-locked or orientation-only display modes used for HUD/SBS/VR scenarios. |
| Nebula | XREAL spatial runtime; legacy v3.8.x needed for Nreal Light 6DoF AR Space. |
| Samsung DeX | Desktop shell that can conflict with Nebula by capturing USB display/HID interface; Extend mode used for HUD display. |
| OpenCode Mobile | Phone-side coding/agent UI kept on S24 touchscreen (Display 0) while custom UI runs in DeX (Display 1). |
| BCI | Brain-computer interface; here Neurosity Crown streams focus/calm/kinesis/signal quality. |
| NATS JetStream / MQTT | Broker options for synchronizing telemetry/video/audio/biometric streams. |
| NVIDIA DeepStream | GPU-accelerated stream analytics pipeline; used as synchronization/fusion spine via NvDs metadata. |
| NvDsUserMeta | DeepStream per-frame metadata attachment point for aligned EEG/biometric payloads. |
