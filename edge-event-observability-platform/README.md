# Edge Event Observability Platform

This node is the passive telemetry gateway of the Industrial Edge Labs runtime. It consumes the canonical `FsmPayload` emitted by [Real-Time Vision Decision System](https://github.com/Industrial-Edge-Labs/real-time-vision-decision-system) and the canonical `InspectionAnomalyPayload` emitted by [Industrial Visual Inspection Engine](https://github.com/Industrial-Edge-Labs/industrial-visual-inspection-engine), then republishes observability data through Prometheus and a stable HTTP snapshot API.

## Upstream And Downstream Neighbors

- Upstream: [Real-Time Vision Decision System](https://github.com/Industrial-Edge-Labs/real-time-vision-decision-system)
- Upstream: [Industrial Visual Inspection Engine](https://github.com/Industrial-Edge-Labs/industrial-visual-inspection-engine)
- Downstream: [Industrial Digital Twin Dashboard](https://github.com/Industrial-Edge-Labs/industrial-digital-twin-dashboard)
- Downstream: [Vision Operations Control Plane](https://github.com/Industrial-Edge-Labs/vision-operations-control-plane)

## Contracts

`FsmPayload`

- `timestamp` `u64`
- `current_state` `u8`

`InspectionAnomalyPayload`

- `timestamp_ns` `u64`
- `frame_id` `u64`
- `confidence` `f32`
- `x` `f32`
- `y` `f32`
- `width` `f32`
- `height` `f32`
- `class_id` `u32`

`HTTP observability surfaces`

- `GET /healthz`
- `GET /metrics`
- `GET /api/v1/telemetry/snapshot`
- 64-bit counters and timestamps are serialized as strings in the JSON snapshot to preserve precision in JavaScript clients.

## Runtime Modes

- Portable mode: `cargo run -- --dry-run`
- ZeroMQ mode: `cargo run --features zeromq -- --decision-endpoint tcp://127.0.0.1:5556 --inspection-endpoint tcp://127.0.0.1:6001`
- Container services: `docker compose up -d`

## Design Notes

- The ingestion boundary remains binary and fixed-width.
- The HTTP snapshot is outside the hot path and exists for operators, dashboards, and service integration.
- The optional `agent/` crate is intentionally isolated for future Linux eBPF deployment and is not required for the portable runtime.
