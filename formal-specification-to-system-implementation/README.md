# Formal Specification To System Implementation

This node captures the canonical control-state safety rules of the runtime in TLA+. It formalizes the same state progression used by the decision engine and the orchestrator, including the sticky decision-emergency latch.

## Upstream And Downstream Neighbors

- Upstream: formal state and safety requirements
- Downstream: [Real-Time Vision Decision System](https://github.com/Industrial-Edge-Labs/real-time-vision-decision-system)
- Downstream: [Edge AI System Orchestrator](https://github.com/Industrial-Edge-Labs/edge-ai-system-orchestrator)

## Formal Contract

- Spec module: `specs/StateAutomata.tla`
- TLC configuration: `specs/StateAutomata.cfg`
- Verified states: `IDLE`, `PERIMETER_BREACH`, `AUTHORIZATION_PENDING`, `EMERGENCY_HALT`
- Verified latch semantics: once the decision emergency is latched, it remains latched

## Verification Entry Points

- PowerShell: `powershell -File .\scripts\verify.ps1`
- Bash / WSL: `bash scripts/verify.sh`

## Design Notes

- The spec models both the decision-layer state and the control-plane emergency overlay.
- The current properties focus on silent-bypass prevention, breach progression, and emergency-latch persistence.
- TLC toolchain download is handled lazily by the provided scripts.
