# Industrial Edge Labs Compatibility Matrix

This document defines the intended upstream and downstream compatibility contracts across the Industrial Edge Labs repositories.

## Principles

- Every repository must build and run coherently on its own.
- Every repository must also declare a stable upstream and downstream contract.
- Cross-repository communication should use explicit, documented binary or textual interfaces.
- No silent contract drift should be tolerated between repositories.

## Repository Compatibility Map

| Repository | Upstream Dependencies | Downstream Dependencies | Primary Contract |
| --- | --- | --- | --- |
| [low-latency-video-stream-orchestrator](https://github.com/Industrial-Edge-Labs/low-latency-video-stream-orchestrator) | Camera or NIC ingress | [event-driven-vision-processing-engine](https://github.com/Industrial-Edge-Labs/event-driven-vision-processing-engine), [industrial-visual-inspection-engine](https://github.com/Industrial-Edge-Labs/industrial-visual-inspection-engine) | `UpstreamFrameEnvelope` |
| [event-driven-vision-processing-engine](https://github.com/Industrial-Edge-Labs/event-driven-vision-processing-engine) | [low-latency-video-stream-orchestrator](https://github.com/Industrial-Edge-Labs/low-latency-video-stream-orchestrator) | [real-time-vision-decision-system](https://github.com/Industrial-Edge-Labs/real-time-vision-decision-system) | `InferencePayload` |
| [industrial-visual-inspection-engine](https://github.com/Industrial-Edge-Labs/industrial-visual-inspection-engine) | [low-latency-video-stream-orchestrator](https://github.com/Industrial-Edge-Labs/low-latency-video-stream-orchestrator) | [real-time-vision-decision-system](https://github.com/Industrial-Edge-Labs/real-time-vision-decision-system), [industrial-mlops-data-lake-pipeline](https://github.com/Industrial-Edge-Labs/industrial-mlops-data-lake-pipeline) | `InspectionAnomalyPayload` and low-confidence event export |
| [real-time-vision-decision-system](https://github.com/Industrial-Edge-Labs/real-time-vision-decision-system) | [event-driven-vision-processing-engine](https://github.com/Industrial-Edge-Labs/event-driven-vision-processing-engine), [industrial-visual-inspection-engine](https://github.com/Industrial-Edge-Labs/industrial-visual-inspection-engine), [formal-specification-to-system-implementation](https://github.com/Industrial-Edge-Labs/formal-specification-to-system-implementation) | [edge-ai-system-orchestrator](https://github.com/Industrial-Edge-Labs/edge-ai-system-orchestrator) | `FsmPayload` |
| [symbolic-to-numeric-computation-pipeline](https://github.com/Industrial-Edge-Labs/symbolic-to-numeric-computation-pipeline) | Mathematical source expressions | [multi-physics-simulation-and-control-system](https://github.com/Industrial-Edge-Labs/multi-physics-simulation-and-control-system), [edge-ai-system-orchestrator](https://github.com/Industrial-Edge-Labs/edge-ai-system-orchestrator) | `FastRK4.hpp`, `kernel_manifest.json` |
| [formal-specification-to-system-implementation](https://github.com/Industrial-Edge-Labs/formal-specification-to-system-implementation) | Formal state models | [real-time-vision-decision-system](https://github.com/Industrial-Edge-Labs/real-time-vision-decision-system), [edge-ai-system-orchestrator](https://github.com/Industrial-Edge-Labs/edge-ai-system-orchestrator) | `StateAutomata.tla`, `StateAutomata.cfg`, verified safety and liveness properties |
| [edge-ai-system-orchestrator](https://github.com/Industrial-Edge-Labs/edge-ai-system-orchestrator) | [real-time-vision-decision-system](https://github.com/Industrial-Edge-Labs/real-time-vision-decision-system), [vision-operations-control-plane](https://github.com/Industrial-Edge-Labs/vision-operations-control-plane) | [multi-physics-simulation-and-control-system](https://github.com/Industrial-Edge-Labs/multi-physics-simulation-and-control-system), [edge-event-observability-platform](https://github.com/Industrial-Edge-Labs/edge-event-observability-platform), [edge-device-fleet-manager](https://github.com/Industrial-Edge-Labs/edge-device-fleet-manager) | `FsmPayload`, `ControlConfig`, telemetry streams |
| [edge-device-fleet-manager](https://github.com/Industrial-Edge-Labs/edge-device-fleet-manager) | [vision-operations-control-plane](https://github.com/Industrial-Edge-Labs/vision-operations-control-plane), [industrial-mlops-data-lake-pipeline](https://github.com/Industrial-Edge-Labs/industrial-mlops-data-lake-pipeline), deployed edge services | [vision-operations-control-plane](https://github.com/Industrial-Edge-Labs/vision-operations-control-plane), deployed edge services | `HeartbeatPayload`, `OTAManifest`, persisted registry snapshots |
| [multi-physics-simulation-and-control-system](https://github.com/Industrial-Edge-Labs/multi-physics-simulation-and-control-system) | [edge-ai-system-orchestrator](https://github.com/Industrial-Edge-Labs/edge-ai-system-orchestrator), [symbolic-to-numeric-computation-pipeline](https://github.com/Industrial-Edge-Labs/symbolic-to-numeric-computation-pipeline) | [industrial-digital-twin-dashboard](https://github.com/Industrial-Edge-Labs/industrial-digital-twin-dashboard), [edge-event-observability-platform](https://github.com/Industrial-Edge-Labs/edge-event-observability-platform) | Plant state vectors and control telemetry |
| [vision-operations-control-plane](https://github.com/Industrial-Edge-Labs/vision-operations-control-plane) | Operators and administrative clients | [edge-ai-system-orchestrator](https://github.com/Industrial-Edge-Labs/edge-ai-system-orchestrator), [edge-device-fleet-manager](https://github.com/Industrial-Edge-Labs/edge-device-fleet-manager) | `ControlConfig`, deployment requests |
| [edge-event-observability-platform](https://github.com/Industrial-Edge-Labs/edge-event-observability-platform) | [real-time-vision-decision-system](https://github.com/Industrial-Edge-Labs/real-time-vision-decision-system), [industrial-visual-inspection-engine](https://github.com/Industrial-Edge-Labs/industrial-visual-inspection-engine) | [industrial-digital-twin-dashboard](https://github.com/Industrial-Edge-Labs/industrial-digital-twin-dashboard), [vision-operations-control-plane](https://github.com/Industrial-Edge-Labs/vision-operations-control-plane) | `FsmPayload`, `InspectionAnomalyPayload`, Prometheus exposition, telemetry snapshot HTTP API |
| [industrial-digital-twin-dashboard](https://github.com/Industrial-Edge-Labs/industrial-digital-twin-dashboard) | [edge-event-observability-platform](https://github.com/Industrial-Edge-Labs/edge-event-observability-platform) | Human operators | Relay snapshot HTTP API, Socket.IO telemetry, rendered state |
| [industrial-mlops-data-lake-pipeline](https://github.com/Industrial-Edge-Labs/industrial-mlops-data-lake-pipeline) | [industrial-visual-inspection-engine](https://github.com/Industrial-Edge-Labs/industrial-visual-inspection-engine), selected outputs from [event-driven-vision-processing-engine](https://github.com/Industrial-Edge-Labs/event-driven-vision-processing-engine) | Retraining jobs and artifact publication | Low-confidence frame events and training artifacts |

## Immediate Canonical Contracts

1. `UpstreamFrameEnvelope`: `timestamp_ns`, `frame_id`, `width`, `height`, `channels`
2. `InferencePayload`: `timestamp`, `object_id`, `confidence`, `x`, `y`, `dx`, `dy`
3. `InspectionAnomalyPayload`: `timestamp_ns`, `frame_id`, `confidence`, `x`, `y`, `width`, `height`, `class_id`
4. `FsmPayload`: `timestamp`, `current_state`
5. `ControlConfig`: `max_velocity_rad`, `min_vision_confidence`, `tick_budget_us`, `emergency_stop`, `reserved[3]`, `profile_revision`
6. Symbolic numeric artifacts: `FastRK4.hpp`, `kernel_manifest.json`

## Working Rule

When a repository is updated, its direct neighbors in this matrix must be checked for compatibility before the repository is considered closed.
