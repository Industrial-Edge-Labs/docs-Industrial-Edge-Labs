# Industrial Digital Twin Dashboard

This node is the operator-facing visualization surface of the Industrial Edge Labs runtime. It does not consume ZeroMQ control streams directly; instead, it renders the normalized snapshot relay derived from [Edge Event Observability Platform](https://github.com/Industrial-Edge-Labs/edge-event-observability-platform).

## Upstream And Downstream Neighbors

- Upstream: [Edge Event Observability Platform](https://github.com/Industrial-Edge-Labs/edge-event-observability-platform)
- Downstream: human operators and diagnostics users

## Runtime Surfaces

- Relay health: `GET /healthz`
- Relay snapshot: `GET /api/v1/telemetry/snapshot`
- Browser telemetry stream: Socket.IO relay sourced from the normalized observability snapshot

## Runtime Modes

- Frontend: `npm run dev`
- Relay only: `npm run start-relay`
- Combined local run: `npm run start-all`
- Production bundle: `npm run build`

## Design Notes

- The dashboard depends on the `#7` snapshot API, not on the decision ZeroMQ bus.
- 64-bit timestamps and counters remain strings across the relay so browser clients preserve precision.
- The relay normalizes decision-state and inspection-state data before the browser consumes it.
