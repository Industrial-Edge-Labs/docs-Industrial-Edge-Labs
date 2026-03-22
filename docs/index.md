# Industrial Edge Labs Documentation

This MkDocs entry mirrors the canonical documentation overview in the repository root.

See also:

- [Master Architecture](../ARCHITECTURE.md)
- [Compatibility Matrix](../COMPATIBILITY_MATRIX.md)

```mermaid
flowchart TD
    subgraph Perception["Perception and Vision"]
        LVO[Low-Latency Video Stream Orchestrator]
        VPE[Event-Driven Vision Processing Engine]
        VIE[Industrial Visual Inspection Engine]
        LVO --> VPE
        LVO --> VIE
    end

    subgraph Decision["Decision and Logic"]
        RTD[Real-Time Vision Decision System]
        SYM[Symbolic-to-Numeric Computation Pipeline]
        FORMAL[Formal Specification to System Implementation]
        VPE --> RTD
        VIE --> RTD
        SYM -.-> MPCS
        FORMAL -.-> RTD
    end

    subgraph Actuation["Actuation and Orchestration"]
        ORCH[Edge AI System Orchestrator]
        FLEET[Edge Device Fleet Manager]
        MPCS[Multi-Physics Simulation and Control System]
        RTD --> ORCH
        ORCH --> MPCS
    end

    subgraph Operations["Observability and Operations"]
        VOCP[Vision Operations Control Plane]
        EOP[Edge Event Observability Platform]
        TWIN[Industrial Digital Twin Dashboard]
        MLOPS[Industrial MLOps Data Lake Pipeline]
        VOCP --> ORCH
        VOCP --> FLEET
        RTD -.-> EOP
        VIE -.-> EOP
        EOP --> TWIN
        VIE --> MLOPS
        MLOPS --> FLEET
    end
```
