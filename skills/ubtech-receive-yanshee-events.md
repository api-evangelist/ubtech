---
name: Receive Yanshee robot events
description: >-
  Subscribe to and receive motion, sensor, vision, and voice events pushed by
  a UBTech Yanshee educational humanoid robot via the Open ADK subscription
  callback surface.
api: openapi/ubtech-yanshee-openadk-openapi-original.yml
operations:
  - putMotions
  - putSensorsSubscription
  - putSensorsSubscriptionSensorsUltrasonic
  - putSensorsSubscriptionSensorsInfrared
  - putSensorsSubscriptionSensorsEnvironment
  - putSensorsSubscriptionSensorsTouch
  - putSensorsSubscriptionSensorsPressure
  - putVisionSubscriptionVisions
  - putVoiceIatSubscriptionVoiceIAT
  - putVoiceAsrSubscriptionsVoiceASR
  - putTTSSubscriptionsVoiceTTS
generated: '2026-07-21'
method: generated
---

# Receive Yanshee robot events

The Yanshee robot pushes events to a callback server you host on your machine
(the Open ADK spec documents the callback host as `127.0.0.1:10101`, base path
`/v1`). The robot's own REST API is reached at `http://<robot-ip>:9090/v1`.

## Setup

1. Install the official Python SDK:
   `pip install git+https://github.com/UBTEDU/Yanshee_OpenADK.git`
2. Point the SDK at your robot:
   `configuration.host = 'http://<robot-ip>:9090/v1'`
3. Run a local HTTP server on port 10101 that accepts `PUT /v1/...` callbacks.

## Event channels (real operationIds from the spec)

- `putMotions` — `PUT /subscriptions/motions`: motion status `idle | run | pause`,
  with motion `name` and Unix `timestamp`.
- `putSensorsSubscription` — `PUT /subscriptions/sensors/gyro`: gyro, accel,
  compass, and euler x/y/z floats.
- `putSensorsSubscriptionSensorsUltrasonic` — ultrasonic distance in mm.
- `putSensorsSubscriptionSensorsInfrared` — infrared distance in mm.
- `putSensorsSubscriptionSensorsEnvironment` — temperature, humidity, pressure.
- `putSensorsSubscriptionSensorsTouch` — touch value 0-3 (which buttons touched).
- `putSensorsSubscriptionSensorsPressure` — pressure in N.
- `putVisionSubscriptionVisions` — vision results: `recognition`, `tracking`,
  `gender`, `age_group`, `quantity`, `color_detect`.
- `putVoiceIatSubscriptionVoiceIAT` / `putVoiceAsrSubscriptionsVoiceASR` /
  `putTTSSubscriptionsVoiceTTS` — voice IAT/ASR/TTS results and status.

## Rules

- Every payload uses the envelope `{code, data, msg}`; `code: 0` means success —
  always check `code` before using `data`.
- There is no authentication on the device-local API (LAN only) — never expose
  the robot or your callback port beyond the local network.
- There is no idempotency or retry contract; treat repeated pushes with the
  same `timestamp` as duplicates.
- Sensor entries carry `id` (I2C address, 1-127) and optional `slot` (1-6) —
  key readings by `id`.
