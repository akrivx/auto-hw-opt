# Architecture

## Motivation

Traditional autotuners optimise within a fixed transformation space.

For example:
- Block size
- Tile size
- Unroll factor
- Vector width

The optimisation problem is therefore:

> Which known transformation should be tried next?

Human performance engineers do more than search. They also discover new transformations.

For example:
- Introduce shared-memory caching
- Introduce thread coarsening
- Fuse kernels
- Change data layout
- Replace the algorithm

The goal of this architecture is to combine both capabilities in a single system.

---

# Design Principles

## Separation of Search and Discovery

Search and discovery are different problems:
- Search chooses among known actions.
- Discovery creates new actions.

They should therefore be represented by independent components.

---

## Hardware Agnostic

The architecture should support:
- CPUs
- GPUs
- FPGAs
- ML accelerators
- Future architectures

through backend-specific perception and actuation layers.

---

## Validation First

Optimisation without correctness is failure.

All candidate optimisations must pass deterministic validation before being accepted.

---

## Pluggable Reasoning

The architecture should not rely on pure LLM reasoning. Instead, it should rely on _reasoning backends_, including:
- Bayesian optimisation
- Evolutionary search
- Reinforcement learning
- Rule systems
- LLMs

---

# High-Level Workflow

```text
Perceive -> Analyse & Reason -> Decide -> Actuate -> Validate -> Learn & Update
```

---

# Architecture Diagram

![Architecture](../diagrams/architecture.png)

---

# Subsystems

## 1. Perception

Builds a structured understanding of the workload and target hardware.

### Code & IR Understanding

Responsibilities:
- Parse source code
- Parse compiler IR
- Extract program structure
- Build workload representation

Outputs:
- Loop structure
- Dependency graphs
- Memory access patterns
- Kernel metadata

### Hardware Telemetry Collection

Responsibilities:
- Collect profiler outputs
- Normalise metrics

Examples:
- Occupancy
- Memory bandwidth
- Cache hit rates
- Stall reasons
- Throughput

Outputs:

A unified hardware telemetry representation.

---

## 2. Reasoning & Cognition

Determines what optimisation actions should be taken.

### Context Builder

Combines:
- Workload representation
- Telemetry
- Run history
- Constraints
- Objectives

into a structured optimisation context.

---

### Specialist Analysis Agents

#### Profiler Agent

Analyses hardware bottlenecks.

Examples:
- Memory bound
- Compute bound
- Occupancy limited

#### Kernel Tuning Agent

Analyses execution structure.

Examples:
- Tiling
- Unrolling
- Fusion
- Vectorisation

#### Memory Agent

Analyses memory hierarchy usage.

Examples:
- Shared memory
- Cache locality
- Memory layout

#### Constraint Agent

Enforces:
- Hardware constraints
- Resource constraints
- Correctness requirements

#### Report Agent

Produces human-readable explanations and summaries.

---

### Search Engine

Selects experiments from the current action space.

Examples:
- Bayesian optimisation
- Evolutionary search
- RL
- Heuristics
- LLM planning

The Search Engine operates only on _known_ actions.

---

### Transformation Discovery Engine

Expands the action space.

Examples:
- Create a shared-memory variant
- Create a thread-coarsened variant
- Introduce a fused implementation
- Propose an alternative algorithm

The Discovery Engine creates new actions.

---

### Action-Space Manager

Maintains the optimisation space.

Stores:
- Known transformations
- Parameters
- Constraints
- Historical effectiveness

Acts as the boundary between search and discovery.

---

## 3. Actuation

Converts optimisation decisions into executable artefacts.

### Transformation Engine

Materialises optimisation actions.

Examples:
- Source rewrites
- Compiler options
- Template instantiation
- Runtime configuration

### Compilation & Execution Sandbox

Responsibilities:
- Build generated variants
- Execute workloads
- Capture artefacts
- Capture diagnostics

Properties:
- Reproducible
- Deterministic
- Isolated

---

## 4. Validation & Reward

Ensures optimisation does not sacrifice correctness.

### Verification Engine

Checks:
- Functional correctness
- Numerical correctness
- Resource constraints

Examples:
- Reference comparison
- Tolerance checks
- NaN detection

### Benchmark & Reward Engine

Measures:
- Latency
- Throughput
- Bandwidth
- Power
- Cost

Produces:
- Success scores
- Performance metrics

---

# Shared Infrastructure

## Experiment Database

Stores:
- Experiments
- Metrics
- Artefacts
- Results

## Transformation Knowledge Base

Stores:
- Known transformations
- Historical effectiveness
- Transformation metadata

## Context & History Manager

Stores:
- Optimisation history
- Experiment summaries
- Search state

## Hardware Knowledge Base

Stores:
- Architectural limits
- Occupancy rules
- Performance models
- Best practices

## Safety & Policy Layer

Enforces:
- Execution limits
- Resource limits
- Organisational policies

## Observability

Provides:
- Logging
- Tracing
- Auditing
- Reproducibility

---

# Example Walkthrough

Consider a CUDA matrix-vector multiplication kernel.

## Step 1

The Perception subsystem observes:
- Source code
- PTX
- Nsight Compute telemetry

The workload is identified as memory-bound.

---

## Step 2

The Profiler Agent reports:
- High memory dependency stalls
- Low compute utilisation
- Low cache effectiveness

---

## Step 3

The Memory Agent analyses the telemetry and workload representation.

It produces a hypothesis:

> The kernel repeatedly accesses the input vector. A locality-improving transformation may reduce memory traffic or improve cache behaviour.

The Transformation Discovery Engine evaluates this hypothesis against the current action space and hardware knowledge base.

If the action does not already exist, it proposes a new transformation family:

```text
SharedMemoryCaching
```

---

## Step 4

The Search Engine explores parameters within the new transformation family.

Examples:
- Tile size
- Block size
- Unroll factor

---

## Step 5

The Actuation subsystem generates variants.

The Validation subsystem verifies correctness and benchmarks performance.

---

## Step 6

Results are recorded.

The Action-Space Manager and Knowledge Base are updated.

Future optimisations can leverage this accumulated knowledge.

---
