# PRD.md:  "The Why"; Product requirements, benchmark metadata table, high-level requirements, etc.

Product Requirements Document

"The Why"; Product requirements, benchmark metadata table, high-level requirements, etc. Defines the benchmark goal, validation objective, test name, benchmark number, category, and high-level success criteria.

## Benchmark Matrix Document Metadata (via benchmark_specification.json)

This PRD.md section is populated from benchmark_specification.json, which is the structured source of benchmark-specific product requirements.

## Workload Number
220

## Workload Name
NVIDIA HPL FP64 (High Performance Linpack)

## Execution Summary (Run and Measure)
Build and run bin/nvhpl (cuSOLVER FP64 dense solve) for yaml warmup plus timed repeats, parse GFLOPS, time-to-solution, and Residual Check, and abort if that check fails, to measure sustained FP64 Linpack. N at or above 65536 is clamped to 32768. Not mpirun, HPL.dat, or an NVIDIA HPC container

## Main Goal
Measure sustained FP64 Linpack performance

## Validation Objective
Validates FP64 Linpack GFLOPS, time-to-solution, and a passing residual check

## Workload Category
Compute & Math Kernels

## Validation Requirement

The benchmark must include an automated SQLite-integrated validation layer that verifies persisted results from `results/benchmark.db`. Validation must confirm:

1. The benchmark run completed successfully with no tool errors.
2. Required samples and aggregate metrics were persisted for every swept shape.
3. Metrics are finite and physically sensible (positive, within plausible bounds).
4. Measured values satisfy configured thresholds when the workload defines pass/fail gates.
5. The benchmark fails validation when required data is missing, invalid, or outside bounds.

## Non-Functional Requirements

| Requirement | Target |
|---|---|
| Automation | Runs to completion without manual intervention after `bash run_benchmark.sh` |
| Idempotency | Re-running `run_benchmark.sh` appends a new run; never corrupts existing rows |
| Persistence | All metrics survive script exit; `results/benchmark.db` is the durable record |
