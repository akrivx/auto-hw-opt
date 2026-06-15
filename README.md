# Autonomous Hardware Optimisation System
An architecture for autonomous optimisation of heterogeneous compute workloads.

The system combines:
- Source and IR analysis
- Search-based optimisation
- Transformation discovery
- Deterministic validation

Unlike traditional autotuners, which operate within a fixed optimisation space, this architecture explicitly separates:
- Search over known actions
- Discovery of new actions

allowing the optimisation space itself to evolve over time.

---

## Motivation

Most autotuning systems assume a predefined search space:
- Block size
- Tile size
- Unroll factor
- Vector width

A search algorithm explores combinations of these parameters to find an optimal configuration.

This project extends that idea by introducing transformation discovery.

Instead of only asking:

> Which known optimisations should be tried next?

the system can also ask:

> What optimisations are missing from the current search space?

Examples include:
- Shared-memory caching
- Thread coarsening
- Persistent kernels
- Loop fusion
- Kernel fusion
- Alternative memory layouts
- Algorithmic variants

---

## Architecture

---

## Core Concepts

### Perception

Build a structured undestanding of the workload and target hardware.

### Reasoning

Analyse bottlenecks, select actions, and discover new transformations.

### Actuation

Materialise transformations and execute experiments.

### Validation

Varify correctness and measure optimisation quality.

















