# PRD.md:  "The Why"; Product requirements, benchmark metadata table, high-level requirements, etc.

Product Requirements Document

"The Why"; Product requirements, benchmark metadata table, high-level requirements, etc. Defines the benchmark goal, validation objective, test name, benchmark number, category, and high-level success criteria.

## Benchmark Matrix Document Metadata (via benchmark_specification.json)

This PRD.md section is populated from benchmark_specification.json, which is the structured source of benchmark-specific product requirements.

## Workload Number
404

## Workload Name
Silent Data Corruption (SDC)

## Execution Summary (Run and Measure)
Compile src/ecc_walk.cu with nvcc and run bin/ecc_walk over a cudaMalloc buffer with two fixed fills (0x00000001 and 0xFFFFFFFF) until yaml num_iterations or duration, then count Correctable/Uncorrectable tokens from nvidia-smi -q -d ECC, to measure SDC pass rate and ECC text hits. DCGM is not called

## Main Goal
Analyze ECC memory integrity via a CUDA fixed-pattern verifier

## Validation Objective
Validates that ecc_walk completes with a loops= line and reports ECC text counts plus pass rate. DCGM memory diagnostics are not run

## Workload Category
System Validation & Reliability

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
