# Vision Operations Control Plane Docs

This folder contains the external documentation for the [vision-operations-control-plane](https://github.com/Industrial-Edge-Labs/vision-operations-control-plane) repository.

## Documents

- [Architecture](./architecture.md)

## Highlights

- Canonical `ControlConfig` binary contract aligned with [edge-ai-system-orchestrator](https://github.com/Industrial-Edge-Labs/edge-ai-system-orchestrator).
- Fleet registry and OTA manifest proxy routes aligned with [edge-device-fleet-manager](https://github.com/Industrial-Edge-Labs/edge-device-fleet-manager).
- Default HTTP bind moved to `127.0.0.1:8081` to avoid colliding with fleet manager on `:8080`.

## Related Repositories

- [docs-Industrial-Edge-Labs](https://github.com/Industrial-Edge-Labs/docs-Industrial-Edge-Labs)
- [vision-operations-control-plane](https://github.com/Industrial-Edge-Labs/vision-operations-control-plane)
- [edge-ai-system-orchestrator](https://github.com/Industrial-Edge-Labs/edge-ai-system-orchestrator)
- [edge-device-fleet-manager](https://github.com/Industrial-Edge-Labs/edge-device-fleet-manager)
