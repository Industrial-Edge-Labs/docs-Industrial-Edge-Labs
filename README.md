# Industrial Edge Labs: Master Architecture

Welcome to the centralized documentation cluster for the **Industrial Edge Labs** ecosystem. This nexus details the topological synchronization of 13 independent micro-repositories operating collectively to process multi-modal computational vision at the Edge, verify control constraints formally using Temporal Logic, and actuate hard real-time physical mechanisms.

See also: [Compatibility Matrix](./COMPATIBILITY_MATRIX.md)

## Ecosystem Network Topology (ZeroMQ DAG)

```mermaid
graph TD
    %% 👁️ Perception Layer
    subgraph Perception["👁️ Perception & Vision Layer"]
        M3["#3 Video Orchestrator (Rust)<br/>PUB: 6000"]
        M1["#1 Vision Engine (C++)<br/>PUB: 5555"]
        M4["#4 Inspection (TensorRT)<br/>PUB: 6001"]
        M3 -- Raw Frames --> M1
        M3 -- Raw Frames --> M4
    end

    %% 🧠 Decision Layer
    subgraph Decision["🧠 Decision & Logic Layer"]
        M2["#2 Decision System (C++)<br/>PUB: 5556"]
        M9["#9 Symbolic Pipeline (Py)"] -. Generates Math For .-> M13
        M10["#10 Formal TLA+ Specs"] -. Mathematically Bounds .-> M2
        
        M1 -- Events --> M2
        M4 -- Anomalies --> M2
    end

    %% ⚙️ Actuation Layer
    subgraph Actuation["⚙️ Control & Actuation"]
        M5["#5 System Orchestrator (C++)<br/>REP: 5557"]
        M13["#13 Physics Simulator (C++)<br/>1kHz Hard-Real-Time"]
        M11["#11 Fleet Manager (Go)<br/>HTTP/OTA: 8080"]
        
        M2 -- FSM State --> M5
        M2 -- Halt Command Intercept --> M13
    end

    %% 📊 Observability
    subgraph Observability["📊 Telemetry & MLOps"]
        M8["#8 Digital Twin (React)<br/>WS: 3001"]
        M7["#7 Rust Prometheus Agent<br/>HTTP: 9090"]
        M12["#12 MLOps Data Lake (MinIO)<br/>S3: 9000"]
        M6["#6 Operations HMI (Go)"]
        
        M5 -- WebSocket Proxy --> M8
        M2 -- Passive Scraping --> M7
        M4 -- Raw Image Uploads --> M12
        M6 -- Auth Configuration --> M5
    end
    
    style Perception fill:#0a0a0c,stroke:#00f0ff,stroke-width:2px,color:#fff
    style Decision fill:#0a0a0c,stroke:#ffaa00,stroke-width:2px,color:#fff
    style Actuation fill:#0a0a0c,stroke:#ff3333,stroke-width:2px,color:#fff
    style Observability fill:#0a0a0c,stroke:#9d4edd,stroke-width:2px,color:#fff
```

## The 13-Node Distributed Mapping

### 👁️ Perception & Vision
1. **[Event-Driven Vision Processing Engine](https://github.com/Industrial-Edge-Labs/event-driven-vision-processing-engine)** (`C++`/`CUDA`): Ingests fast-path zero-copy memory frames bypassing CPU overheads to evaluate bounds rapidly.
3. **[Low-Latency Video Stream Orchestrator](https://github.com/Industrial-Edge-Labs/low-latency-video-stream-orchestrator)** (`Rust`): Low-level binding pushing raw GigE network buffers down to ZeroMQ Publisher TCP pipelines.
4. **[Industrial Visual Inspection Engine](https://github.com/Industrial-Edge-Labs/industrial-visual-inspection-engine)** (`Python`/`TensorRT`): Quantized deep-learning inferences executing exclusively on GPU CUDA Cores to flag perimeter abnormalities.

### 🧠 Decision & Logic
2. **[Real-Time Vision Decision System](https://github.com/Industrial-Edge-Labs/real-time-vision-decision-system)** (`C++`): Multiplexes threaded streams originating from Vision engines and maps constraints to determine critical physical actions (E.g., `EMERGENCY_HALT`).
9. **[Symbolic-to-Numeric Computation Pipeline](https://github.com/Industrial-Edge-Labs/symbolic-to-numeric-computation-pipeline)** (`Python`): *Meta-Programming* SymPy abstract syntax trees to compile non-linear physical theorems directly into SIMD unrolled C++.
10. **[Formal Specification to System Implementation](https://github.com/Industrial-Edge-Labs/formal-specification-to-system-implementation)** (`TLA+`): A mathematical Model Checking bound preventing dynamic concurrency deadlocks from manifesting across C++ boundaries.

### ⚙️ Actuation & Orchestration
5. **[Edge AI System Orchestrator](https://github.com/Industrial-Edge-Labs/edge-ai-system-orchestrator)** (`C++20`): High-affinity threading core coordinating logical checkpoints and issuing hard stop commands.
11. **[Edge Device Fleet Manager](https://github.com/Industrial-Edge-Labs/edge-device-fleet-manager)** (`Go`): Massive Thread-Safe REST interface distributing OTA (Over-The-Air) firmware revisions and polling camera health.
13. **[Multi-Physics Simulation & Control System](https://github.com/Industrial-Edge-Labs/multi-physics-simulation-and-control-system)** (`C++20`): $1\text{kHz}$ simulator driving strictly controlled physical robotic actuators dynamically reacting to Vision Engine verdicts.

### 📊 Observability & Subsystems
6. **[Vision Operations Control Plane](https://github.com/Industrial-Edge-Labs/vision-operations-control-plane)** (`Go`): HTTP/REST administration gateways pushing locked config blocks (`ZMQ_REQ`) down into native processes.
7. **[Edge Event Observability Platform](https://github.com/Industrial-Edge-Labs/edge-event-observability-platform)** (`Rust`): Ultra-passive `Hyper/ZMQ` background exporter generating immediate visual dashboards via Prometheus mappings.
8. **[Industrial Digital Twin Dashboard](https://github.com/Industrial-Edge-Labs/industrial-digital-twin-dashboard)** (`React`/`WebGL`): Gorgeous WebGL node executing `ThreeJS` frames matching zero-latency physical hardware statuses through WebSockets.
12. **[Industrial MLOps Data Lake Pipeline](https://github.com/Industrial-Edge-Labs/industrial-mlops-data-lake-pipeline)** (`Python`/`MinIO`): Broad S3 Object Storage sink designed to funnel physical defection imagery back into the Training Clusters iteratively.

---
> ⚠️ Engineered precisely under **Hard Real-Time Bounds ($<1\text{ms}$)** to support Autonomous Manufacturing Frameworks.
