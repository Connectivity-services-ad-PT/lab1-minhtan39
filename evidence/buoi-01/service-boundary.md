# Service Boundary - Team Camera Stream

## Service

- Team: `team-camera`
- Service name: `camera-stream`
- Public port for class demo: `8000`
- Required health endpoint: `GET /health`

## Actor

- Campus camera device: captures frames and sends frame payloads to Camera Stream.
- Security operator: reviews frame history and analysis results during a campus incident.
- AI Vision service: receives selected frames from Camera Stream for object/person detection.
- Analytics service: receives camera events for aggregate reporting and KPI dashboards.

## Responsibility

Camera Stream owns the ingestion of camera frames from campus cameras. It validates frame metadata, records accepted frames, triggers AI Vision when motion is detected, and publishes camera events to Analytics for aggregation.

## In Scope

- Accept frame metadata and base64 image payloads.
- Keep a lightweight in-memory frame history for lab testing.
- Call AI Vision through REST sync: `POST /api/v1/detect`.
- Publish camera analytics events through REST/queue-compatible payload: `POST /api/v1/events`.
- Return Problem Details for validation, auth, timeout, and dependency errors.

## Out of Scope

- Training or hosting a real computer-vision model.
- Long-term video storage.
- Real camera hardware integration.
- User management and role policy.

## Main Inputs

```json
{
  "camera_id": "CAM-A01",
  "location": "Main lobby",
  "frame_format": "jpeg",
  "image_base64": "dGVzdC1pbWFnZS1mcmFtZQ==",
  "captured_at": "2026-05-13T08:30:00+07:00",
  "motion_score": 0.82
}
```

## Main Outputs

```json
{
  "frame_id": "FR-20260513-0001",
  "camera_id": "CAM-A01",
  "accepted": true,
  "motion_level": "high",
  "created_at": "2026-05-13T01:30:00Z"
}
```

## Dependencies

| Consumer | Provider | Mechanism | Purpose |
|---|---|---|---|
| Camera Stream | AI Vision | REST sync | Send frame to `/api/v1/detect` when analysis is requested |
| Camera Stream | Analytics | Queue async compatible event payload | Feed camera events for aggregate KPI |

## Demo Readiness Notes

- Bind API to `0.0.0.0`.
- Publish `8000:8000` in Docker or Compose.
- Put partner URLs in `.env`, not source code.
- Use a 3 second upstream timeout so dependency failure does not hang the demo.
