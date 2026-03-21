# Formal Logic & Proofs
## Verification of the Edge Orchestrator

The TLA+ specifications defined in this module provide bounded execution invariants. The system proves that the orchestrator will never suffer from a livelock when interacting with NVMM pointers mapped by the Vision processing engine. 

To execute the TLC Model Checker manually:
```bash
java -cp tla2tools.jar tlc2.TLC specs/StateAutomata.tla
```
