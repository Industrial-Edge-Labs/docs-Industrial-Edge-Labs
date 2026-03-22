# Low-Latency Video Stream Orchestrator Docs

This folder contains the external documentation for the [low-latency-video-stream-orchestrator](https://github.com/Industrial-Edge-Labs/low-latency-video-stream-orchestrator) repository.

## Documents

- [Architecture](./architecture.md)

## Current Implementation Notes

- The repository is split into a small library plus a thin `main.rs` entrypoint.
- The canonical frame contract lives in `src/contracts.rs`.
- Runtime parsing and defaults live in `src/config.rs`.
- The ingest loop and router coordination live in `src/pipeline.rs`.
- The ZeroMQ adapter lives in `src/publisher.rs`.
- Unit tests are embedded next to the modules to preserve behavior while the internals evolve.

## Related Repositories

- [docs-Industrial-Edge-Labs](https://github.com/Industrial-Edge-Labs/docs-Industrial-Edge-Labs)
- [low-latency-video-stream-orchestrator](https://github.com/Industrial-Edge-Labs/low-latency-video-stream-orchestrator)
- [event-driven-vision-processing-engine](https://github.com/Industrial-Edge-Labs/event-driven-vision-processing-engine)
- [industrial-visual-inspection-engine](https://github.com/Industrial-Edge-Labs/industrial-visual-inspection-engine)
