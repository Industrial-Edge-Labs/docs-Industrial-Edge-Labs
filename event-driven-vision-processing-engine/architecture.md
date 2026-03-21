# Event-Driven Vision Processing Engine

## Scope

This document describes the external architecture of the [event-driven-vision-processing-engine](https://github.com/Industrial-Edge-Labs/event-driven-vision-processing-engine) repository inside the [docs-Industrial-Edge-Labs](https://github.com/Industrial-Edge-Labs/docs-Industrial-Edge-Labs) repository.

The node transforms dense video ingress into sparse motion events so that downstream deterministic systems can react without evaluating every full frame.

## Repository Interfaces

- Upstream producer: [low-latency-video-stream-orchestrator](https://github.com/Industrial-Edge-Labs/low-latency-video-stream-orchestrator)
- Downstream consumer: [real-time-vision-decision-system](https://github.com/Industrial-Edge-Labs/real-time-vision-decision-system)
- Local implementation: [event-driven-vision-processing-engine](https://github.com/Industrial-Edge-Labs/event-driven-vision-processing-engine)

## Operational Flow

```mermaid
flowchart LR
    CAM[Industrial Camera Streams] --> LVO[Low-Latency Video Stream Orchestrator]
    LVO -->|tcp://127.0.0.1:6000| VPE[Event-Driven Vision Processing Engine]
    VPE --> DERIV[Temporal Derivative Gate]
    DERIV -->|score < threshold| DROP[Discard Dense Frame]
    DERIV -->|score >= threshold| EVENT[Build Sparse Event Payload]
    EVENT -->|tcp://127.0.0.1:5555| RTD[Real-Time Vision Decision System]
```

## Execution Stages

```mermaid
sequenceDiagram
    autonumber
    participant Upstream as Stream Orchestrator
    participant Engine as Vision Engine
    participant Gate as Temporal Gate
    participant Bus as Decision Bus

    loop Continuous execution
        Upstream->>Engine: Frame envelope or deterministic fallback ingest
        Engine->>Gate: Compute temporal-energy score
        alt Score below threshold
            Gate-->>Engine: Keep the sample local
        else Score above threshold
            Gate-->>Engine: Emit sparse event
            Engine->>Bus: Binary inference payload
        end
    end
```

## Current Runtime Model

The current implementation is intentionally split into two execution modes:

1. Integrated mode, where the node consumes upstream frame envelopes over ZeroMQ and publishes events to the decision layer.
2. Portable fallback mode, where the node keeps running deterministically even if the upstream video orchestrator is offline.

This keeps the node testable on a standalone workstation while preserving the same message contract used for integration.

## Binary Event Contract

```mermaid
classDiagram
    class InferencePayload {
        uint64 timestamp_ns
        uint32 object_id
        float confidence
        float x
        float y
        float dx
        float dy
    }
```

### Field Semantics

- `timestamp_ns`: monotonic event timestamp.
- `object_id`: monotonically increasing identifier for the emitted sparse event.
- `confidence`: normalized event confidence in the `[0, 1)` range.
- `x`, `y`: compact spatial location of the event.
- `dx`, `dy`: compact motion trend of the event.

## Upstream Envelope Contract

The current runtime expects a compact upstream frame envelope with:

- `timestamp`
- `frame_id`
- `width`
- `height`
- `channels`

If the upstream producer sends a geometry that does not match the configured ingest shape, the envelope is dropped instead of being forwarded into the event path. This keeps the hot path deterministic and avoids accidental drift between [low-latency-video-stream-orchestrator](https://github.com/Industrial-Edge-Labs/low-latency-video-stream-orchestrator) and [event-driven-vision-processing-engine](https://github.com/Industrial-Edge-Labs/event-driven-vision-processing-engine).

## Build Strategy

The runtime is designed to compile out of the box in standalone fallback mode, without forcing ZeroMQ, CUDA, or OpenCV when those libraries are not yet needed by the active code path.

```mermaid
flowchart TD
    START[Configure CMake] --> ZMQ{ZeroMQ enabled?}
    ZMQ -- No --> MOCK[Build standalone fallback mode]
    ZMQ -- Yes --> BUS[Enable upstream and downstream ZeroMQ sockets]
    BUS --> CUDA{CUDA enabled?}
    CUDA -- Yes --> CUDART[Link CUDA runtime]
    CUDA -- No --> SKIPCUDA[Skip CUDA linkage]
    CUDART --> OPENCV{OpenCV enabled?}
    SKIPCUDA --> OPENCV
    OPENCV -- Yes --> CVLINK[Link OpenCV]
    OPENCV -- No --> DONE[Build complete]
    CVLINK --> DONE
```

## Design Notes

- The node emits binary payloads instead of JSON in the hot path.
- The fallback ingest is deterministic so that dry-run behavior is reproducible.
- Geometry validation happens before a frame envelope is accepted into the event path.
- The current temporal-energy stage is a deterministic surrogate, not yet the final CUDA derivative kernel.

## Recommended Next Steps

1. Replace the deterministic surrogate with a true two-frame derivative kernel backed by pinned memory.
2. Version the upstream envelope contract explicitly so the interface with [low-latency-video-stream-orchestrator](https://github.com/Industrial-Edge-Labs/low-latency-video-stream-orchestrator) cannot drift silently.
3. Add a benchmark that reports discarded frames versus emitted events under fixed load.
4. Add a versioned event schema shared with [real-time-vision-decision-system](https://github.com/Industrial-Edge-Labs/real-time-vision-decision-system).
