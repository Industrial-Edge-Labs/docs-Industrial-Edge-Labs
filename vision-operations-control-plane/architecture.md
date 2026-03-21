# Vision Operations Control Plane

## Scope

This document describes the external architecture of the [vision-operations-control-plane](https://github.com/Industrial-Edge-Labs/vision-operations-control-plane) repository inside the [docs-Industrial-Edge-Labs](https://github.com/Industrial-Edge-Labs/docs-Industrial-Edge-Labs) repository.

The node is the operator-facing HTTP gateway for runtime control changes. It accepts signed operator requests, encodes `ControlConfig` for [edge-ai-system-orchestrator](https://github.com/Industrial-Edge-Labs/edge-ai-system-orchestrator), and proxies fleet-facing read operations to [edge-device-fleet-manager](https://github.com/Industrial-Edge-Labs/edge-device-fleet-manager).

## Position In The Flow

```mermaid
flowchart LR
    OP[Operators] -->|HTTP| VOCP[Vision Operations Control Plane]
    VOCP -->|ControlConfig / ZMQ REQ-REP| ORCH[Edge AI System Orchestrator]
    VOCP -->|HTTP proxy| FLEET[Edge Device Fleet Manager]
```

## HTTP Surface

```mermaid
classDiagram
    class VisionOperationsControlPlane {
        GET /healthz
        GET /api/v1/hardware/config
        POST /api/v1/hardware/config
        GET /api/v1/fleet/registry
        GET /api/v1/fleet/ota-manifest
    }
```

## Binary Contract

```mermaid
classDiagram
    class ControlConfig {
        double max_velocity_rad
        double min_vision_confidence
        uint32 tick_budget_us
        uint8 emergency_stop
        uint8 reserved[3]
        uint64 profile_revision
    }
```

## Design Notes

- The HTTP bind defaults to `127.0.0.1:8081` so it does not conflict with [edge-device-fleet-manager](https://github.com/Industrial-Edge-Labs/edge-device-fleet-manager) on `:8080`.
- The `ControlConfig` wire block remains fixed at 32 bytes and matches [edge-ai-system-orchestrator](https://github.com/Industrial-Edge-Labs/edge-ai-system-orchestrator).
- Fleet proxy routes are read-only in the current version, which keeps the boundary between runtime control and fleet rollout explicit.
- Applied configurations are persisted locally for restart continuity.

## Recommended Next Steps

1. Add authenticated rollout or drain operations once [edge-device-fleet-manager](https://github.com/Industrial-Edge-Labs/edge-device-fleet-manager) exposes stable write-side APIs.
2. Add request authentication middleware if the HTTP control plane will be exposed beyond localhost or an internal service mesh.
3. Export control-plane audit events to [edge-event-observability-platform](https://github.com/Industrial-Edge-Labs/edge-event-observability-platform).
