# Edge AI System Orchestrator

## Scope

This document describes the external architecture of the [edge-ai-system-orchestrator](https://github.com/Industrial-Edge-Labs/edge-ai-system-orchestrator) repository inside the [docs-Industrial-Edge-Labs](https://github.com/Industrial-Edge-Labs/docs-Industrial-Edge-Labs) repository.

The node is the deterministic coordinator between the decision layer and the actuation-facing runtime. It consumes `FsmPayload` from [real-time-vision-decision-system](https://github.com/Industrial-Edge-Labs/real-time-vision-decision-system), accepts `ControlConfig` messages from [vision-operations-control-plane](https://github.com/Industrial-Edge-Labs/vision-operations-control-plane), and maintains the emergency-stop and control-profile state required by the plant-facing loop.

The repository now supports both a ZeroMQ-enabled integration mode and a portable dry-run mode for scheduler validation on machines without the full runtime stack.

## Position In The Flow

```mermaid
flowchart LR
    RTD[Real-Time Vision Decision System] -->|FsmPayload| ORCH[Edge AI System Orchestrator]
    VOCP[Vision Operations Control Plane] -->|ControlConfig| ORCH
    ORCH -->|Actuation gate and scheduler state| MPCS[Multi-Physics Simulation and Control System]
    ORCH -.->|Telemetry and status| EOP[Edge Event Observability Platform]
```

## Execution Model

```mermaid
sequenceDiagram
    autonumber
    participant Decision as Decision System
    participant Control as Control Plane
    participant Orch as Orchestrator
    participant Plant as Physical or Simulated Plant

    loop Scheduler tick
        Decision->>Orch: FsmPayload
        Control->>Orch: ControlConfig
        Orch->>Orch: Validate contracts and latch safety state
        Orch->>Plant: Apply actuation gate and profile bounds
    end
```

## Input Contracts

### FSM Input

```mermaid
classDiagram
    class FsmPayload {
        uint64 timestamp
        uint8 current_state
    }
```

### Control Input

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

## Runtime Modes

```mermaid
flowchart TD
    Start[Process start] --> Mode{Dry-run?}
    Mode -->|Yes| Synthetic[Inject synthetic FSM and control updates]
    Mode -->|No| Live[Attach ZeroMQ decision and control sockets]
    Synthetic --> Validate[Validate payload semantics]
    Live --> Validate
    Validate --> Latch[Update scheduler state and emergency latches]
    Latch --> Gate{Emergency active?}
    Gate -->|Yes| Halt[Keep actuation gated]
    Gate -->|No| Run[Advance scheduler loop]
```

## Design Notes

- The default build is portable and does not require CUDA or ZeroMQ.
- `FsmPayload` and `ControlConfig` are validated semantically before they affect the scheduler.
- Decision-triggered emergency halt and control-plane emergency override are tracked independently, so a control-plane packet cannot clear a decision-latched stop by accident.
- The current repository exposes the deterministic actuation gate and profile state locally inside the orchestrator process; downstream plant and telemetry buses remain the next integration step.

## Recommended Next Steps

1. Export an explicit downstream actuation or telemetry payload for [multi-physics-simulation-and-control-system](https://github.com/Industrial-Edge-Labs/multi-physics-simulation-and-control-system) and [edge-event-observability-platform](https://github.com/Industrial-Edge-Labs/edge-event-observability-platform).
2. Extend the control contract if [vision-operations-control-plane](https://github.com/Industrial-Edge-Labs/vision-operations-control-plane) needs richer profile toggles or signed reset semantics.
3. Reconcile the plant-side subscriber topology so plant integration consistently flows through the orchestrator rather than subscribing directly to the decision bus.
