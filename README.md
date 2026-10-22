# Frigate NVR: Home Automation & AI Video Surveillance

Self-hosted home security stack running on Docker: **Frigate** for real-time AI object detection on camera streams, **Home Assistant** for automation, and **Mosquitto** as the MQTT broker connecting them.

![Docker](https://img.shields.io/badge/Docker%20Compose-2496ED?logo=docker&logoColor=white)
![Frigate](https://img.shields.io/badge/Frigate-0.15-blue)
![Home Assistant](https://img.shields.io/badge/Home%20Assistant-stable-41BDF5?logo=homeassistant&logoColor=white)

## Architecture

```
 USB webcam (/dev/video0)
          │
          ▼
 ┌─────────────────┐   MQTT events   ┌──────────────┐        ┌──────────────────┐
 │ Frigate  :5000  │ ──────────────▶ │ Mosquitto    │ ─────▶ │ Home Assistant   │
 │ AI detection    │                 │ :1883 / :9001│        │ :8123 automations│
 └─────────────────┘                 └──────────────┘        └──────────────────┘
```

| Service | Image | Port | Role |
|---|---|---|---|
| `frigate` | `ghcr.io/blakeblackshear/frigate:stable` | 5000 | NVR and real-time object detection |
| `mqtt` | `eclipse-mosquitto` | 1883, 9001 | Message broker between Frigate and Home Assistant |
| `homeassistant` | `ghcr.io/home-assistant/home-assistant:stable` | 8123 | Home automation, notifications, dashboards |

## Getting started

Requirements: a Linux host (tested on Debian 12) with Docker and Docker Compose, and a USB webcam.

```bash
git clone https://github.com/joeylukoji/Frigate-NVR-.git
cd Frigate-NVR-
cp .env.example .env        # then set a strong FRIGATE_RTSP_PASSWORD
docker compose up -d
```

- Frigate UI: http://localhost:5000
- Home Assistant: http://localhost:8123

## Configuration

- **Cameras and detection:** [`frigate/config/config.yaml`](frigate/config/config.yaml). The default config reads a USB webcam through V4L2 (`/dev/video0`) and runs detection at 1280×720, 5 fps. Adjust the resolution to what your webcam supports (`v4l2-ctl --list-formats-ext`).
- **Secrets:** `.env` (never committed). `FRIGATE_RTSP_PASSWORD` can be referenced in camera URLs as `{FRIGATE_RTSP_PASSWORD}`.
- **MQTT:** disabled in Frigate by default. To send events to Home Assistant, set `mqtt.enabled: true` and `host: mqtt` in the Frigate config, then add the Frigate integration in Home Assistant.

## Roadmap

- Connect Frigate events to Home Assistant notifications
- Add IP cameras (RTSP)
- Hardware-accelerated detection (Coral TPU / OpenVINO)

## Author

**Joy Lukoji**, Independent AI Researcher · [Portfolio](https://joyportfolio-six.vercel.app/)
