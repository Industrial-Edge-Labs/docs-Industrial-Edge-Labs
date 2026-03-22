# Symbolic To Numeric Computation Pipeline

This node generates deterministic numerical solver artifacts from canonical symbolic models. Its downstream contract is not the internal SymPy tree; it is the generated C++ header plus the manifest describing how the downstream simulator should consume it.

## Upstream And Downstream Neighbors

- Upstream: symbolic mathematical source expressions
- Downstream: [Multi-Physics Simulation & Control System](https://github.com/Industrial-Edge-Labs/multi-physics-simulation-and-control-system)
- Downstream: [Edge AI System Orchestrator](https://github.com/Industrial-Edge-Labs/edge-ai-system-orchestrator)

## Generated Artifact Contract

- Header: `FastRK4.hpp`
- Manifest: `kernel_manifest.json`
- Model: `mass_spring_damper`
- State size: `2`
- Parameter order: `mass`, `stiffness`, `damping`, `force`

## Runtime Modes

- Python generation: `python src/transpiler.py --output-dir out`
- CMake validation build: `cmake -S . -B build-default && cmake --build build-default --config Release`
- Demo execution: `./build-default/Release/symbolic_kernel_demo 24 0.01`
- Python tests: `python -m unittest discover -s tests -p "test_*.py"`
- C++ tests: `ctest --test-dir build-default --output-on-failure -C Release`

## Design Notes

- The emitted solver is deterministic and allocation-free.
- The manifest exists so downstream repositories can validate artifact shape before inclusion.
- The build graph regenerates the header automatically through CMake before compiling the demo consumer.
- The transpiler is split into CLI, model, artifact, and generator modules so the emitted contract can stay stable while internals evolve.
- The demo consumer is split into runtime parsing and simulation modules so the generated kernel path is locally testable.
