# Real-Time Vision Decision System

## Scope

This document describes the external architecture of the [real-time-vision-decision-system](https://github.com/Industrial-Edge-Labs/real-time-vision-decision-system) repository inside the [docs-Industrial-Edge-Labs](https://github.com/Industrial-Edge-Labs/docs-Industrial-Edge-Labs) repository.

The node is the deterministic convergence point for the early perception branch. It ingests motion events from [event-driven-vision-processing-engine](https://github.com/Industrial-Edge-Labs/event-driven-vision-processing-engine) and inspection anomalies from [industrial-visual-inspection-engine](https://github.com/Industrial-Edge-Labs/industrial-visual-inspection-engine), then emits FSM state transitions to [edge-ai-system-orchestrator](https://github.com/Industrial-Edge-Labs/edge-ai-system-orchestrator).

The repository supports both a ZeroMQ-enabled live topology and a portable dry-run mode that exercises both input branches without external dependencies.

## Position In The Flow

```mermaid
flowchart LR
    VPE[Event-Driven Vision Processing Engine] -->|InferencePayload| RTD[Real-Time Vision Decision System]
    IVI[Industrial Visual Inspection Engine] -->|InspectionAnomalyPayload| RTD
    RTD -->|FsmPayload| ORCH[Edge AI System Orchestrator]
```

## Execution Model

```mermaid
sequenceDiagram
    autonumber
    participant Vision as Event-Driven Vision
    participant Inspect as Inspection Engine
    participant Decision as Decision System
    participant FSM as Finite State Machine
    participant Orchestrator as Edge AI System Orchestrator

    Vision->>Decision: InferencePayload
    Decision->>FSM: Evaluate motion event
    Inspect->>Decision: InspectionAnomalyPayload
    Decision->>FSM: Evaluate inspection anomaly
    FSM->>Orchestrator: FsmPayload
```

## Input Contracts

### Vision Input

```mermaid
classDiagram
    class InferencePayload {
        uint64 timestamp
        uint32 object_id
        float confidence
        float x
        float y
        float dx
        float dy
    }
```

### Inspection Input

```mermaid
classDiagram
    class InspectionAnomalyPayload {
        uint64 timestamp_ns
        uint64 frame_id
        float confidence
        float x
        float y
        float width
        float height
        uint32 class_id
    }
```

## Output Contract

```mermaid
classDiagram
    class FsmPayload {
        uint64 timestamp
        uint8 current_state
    }
```

By default the output is published on `tcp://127.0.0.1:5556`, which matches [edge-ai-system-orchestrator](https://github.com/Industrial-Edge-Labs/edge-ai-system-orchestrator).

## Runtime Modes

```mermaid
flowchart TD
    Start[Process start] --> Mode{Dry-run?}
    Mode -->|Yes| Synthetic[Generate synthetic vision and inspection inputs]
    Mode -->|No| Live[Poll ZeroMQ subscribers]
    Synthetic --> Validate[Validate payload contracts]
    Live --> Validate
    Validate --> FSM[Evaluate finite state machine]
    FSM --> Publish{State publication enabled?}
    Publish -->|Yes| Out[Publish FsmPayload to orchestrator]
    Publish -->|No| Local[Keep transitions local]
```

## State Progression

```mermaid
stateDiagram-v2
    [*] --> IDLE
    IDLE --> PERIMETER_BREACH: vision motion threshold crossed
    PERIMETER_BREACH --> AUTHORIZATION_PENDING: sustained motion or moderate inspection anomaly
    AUTHORIZATION_PENDING --> EMERGENCY_HALT: severe anomaly or emergency motion event
    PERIMETER_BREACH --> EMERGENCY_HALT: severe anomaly
    EMERGENCY_HALT --> EMERGENCY_HALT: latched stop
```

## Design Notes

- The runtime treats the inspection branch as an explicit contract, not as a generic byte trigger.
- The runtime validates payload semantics, not only payload size, before evaluating transitions.
- FSM state publication now follows the same state transitions stored internally.
- The default build remains portable without requiring ZeroMQ on the local machine.
- The ZeroMQ-enabled build preserves the live integration topology used by the rest of the system.

## Recommended Next Steps

1. Integrate formal constraints from [formal-specification-to-system-implementation](https://github.com/Industrial-Edge-Labs/formal-specification-to-system-implementation) directly into state transition guards.
2. Define a richer state taxonomy if [edge-ai-system-orchestrator](https://github.com/Industrial-Edge-Labs/edge-ai-system-orchestrator) needs more than perimeter breach and emergency halt.
3. Add a replayable test harness that feeds recorded `InferencePayload` and `InspectionAnomalyPayload` samples into the FSM.
