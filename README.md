# Silent Data Corruption (SDC) Benchmark

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![CI](https://github.com/garys-gpu-benchmarks/404-gpu-bench-nvidia-sdc-ecc-integrity-ubu2604/actions/workflows/ci.yml/badge.svg)](https://github.com/garys-gpu-benchmarks/404-gpu-bench-nvidia-sdc-ecc-integrity-ubu2604/actions/workflows/ci.yml)

Target: Ubuntu 26.04 · NVIDIA · see Hardware Requirements. This is a host benchmark, not a laptop `pip install` project.

## Quick Start

```bash
git clone https://github.com/garys-gpu-benchmarks/404-gpu-bench-nvidia-sdc-ecc-integrity-ubu2604.git
cd 404-gpu-bench-nvidia-sdc-ecc-integrity-ubu2604
sudo bash setup.sh --assume-yes
bash run_benchmark.sh --profile smoke --validate
```
Results are written to `results/benchmark.db` and `results/summary.json`.

This workload is executed on the validation host after the repository is copied there. `setup.sh` and `run_benchmark.sh` do not open an outbound SSH session.

Prerequisites: Ubuntu 26.04; NVIDIA; Python 3.14.4; root or sudo for `setup.sh`. Framework: Bash, SQLite, Python, PyYAML, CUDA Runtime, NVCC, nvidia-smi. This is a host benchmark, not a laptop `pip install` project.

```mermaid
flowchart LR
  setup.sh --> run_benchmark.sh --> parse_results.py --> results/benchmark.db
```

## 1. Overview

Builds and runs bin/ecc_walk from src/ecc_walk.cu to walk a cudaMalloc buffer and query nvidia-smi ECC text. device_id: CUDA device index. memory_size: allocation size in GB. num_iterations: verify loops. Sweep dimensions: device_id, memory_size, pattern_type, ecc_check, num_iterations, output_format, duration, count.

## 2. What It Validates

- Validates that ecc_walk completes with a loops= line and reports ECC text counts plus pass rate. DCGM memory diagnostics are not run
- #1: Uncorrected ECC errors (uncorrected_ecc_errors_count); is present and physically sensible.
- #2: Corrected ECC errors (corrected_ecc_errors_count); is present and physically sensible.
- #3: Wall-clock time, s (test_wall_clock_completion_time_s); is present and physically sensible.
- #4: Device buffer size, GiB (hbm3_coverage_gb); is present and physically sensible.
- #5: Fixed-pattern fill/verify pass rate (walking_1s_moving_inversion_pass_rate) is present and physically sensible.

## 3. Metrics Captured

- **#1: Uncorrected ECC errors** — stored as `uncorrected_ecc_errors_count`.
- **#2: Corrected ECC errors** — stored as `corrected_ecc_errors_count`.
- **#3: Wall-clock time, s** — stored as `test_wall_clock_completion_time_s`.
- **#4: Device buffer size, GiB** — stored as `hbm3_coverage_gb`.
- **#5: Fixed-pattern fill/verify pass rate** — stored as `walking_1s_moving_inversion_pass_rate`.

## 4. Hardware Requirements

### Supported environment

- OS: Ubuntu 26.04
- GPU vendor: NVIDIA
- Framework family: Bash, SQLite, Python, PyYAML, CUDA Runtime, NVCC, nvidia-smi
- Python: Python 3.14.4

### Reference validation environment

The tables below describe the machine used to generate the reference results. They are not a requirement that every user buy that exact cloud instance.

### System

Builds and runs bin/ecc_walk from src/ecc_walk.cu to walk a cudaMalloc buffer and query nvidia-smi ECC text. device_id: CUDA device index. memory_size: allocation size in GB.

### GPU

Ubuntu 26.04 / NVIDIA / Bash, SQLite, Python, PyYAML, CUDA Runtime, NVCC, nvidia-smi

## 5. Software Requirements

| Component | Version |
|---|---|
| OS | Ubuntu 26.04 |
| Kernel | kernel 7.0.0 |
| Python | Python 3.14.4 |
| ROCm | CUDA 13.3 |
| rocBLAS | N/A - rocBLAS not used |

Builds and runs bin/ecc_walk from src/ecc_walk.cu to walk a cudaMalloc buffer and query nvidia-smi ECC text. device_id: CUDA device index. memory_size: allocation size in GB.

## 6. Installation

```bash
Compile src/ecc_walk.cu with nvcc; run bin/ecc_walk; query nvidia-smi -q -d ECC
```

## 7. Running the Benchmark

```bash
Compile src/ecc_walk.cu with nvcc; run bin/ecc_walk; query nvidia-smi -q -d ECC
```

**Validating results separately:**

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional
python3 -m venv .venv
source ".venv/bin/activate"
".venv/bin/python" scripts/validate_results.py
```

## 8. Output

### `results/benchmark.db` (SQLite)

CSV aggregate of the ECC walk, written as sample_index 0 and 1

sample_index,status,uncorrected_ecc_errors_count,corrected_ecc_errors_count,uncorrected_ecc_aggregate_count,corrected_ecc_aggregate_count,uncorrected_ecc_nonzero_field_count,corrected_ecc_nonzero_field_count,hbm3_coverage_gb,walking_1s_moving_inversion_pass_rate,test_wall_clock_completion_time_s,error_message
0,ok,0,0,0,0,0,0,1.0,100,2.0,

```bash
Compile src/ecc_walk.cu with nvcc; run bin/ecc_walk; query nvidia-smi -q -d ECC
```

### `results/summary.json`

Consolidated metrics from the most recent run — suitable for CI artifact upload or dashboard ingestion.

### `results/raw/<timestamp>.txt`

CSV aggregate of the ECC walk, written as sample_index 0 and 1

sample_index,status,uncorrected_ecc_errors_count,corrected_ecc_errors_count,uncorrected_ecc_aggregate_count,corrected_ecc_aggregate_count,uncorrected_ecc_nonzero_field_count,corrected_ecc_nonzero_field_count,hbm3_coverage_gb,walking_1s_moving_inversion_pass_rate,test_wall_clock_completion_time_s,error_message
0,ok,0,0,0,0,0,0,1.0,100,2.0,

## 9. Baselines / Thresholds

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

## 10. Troubleshooting

**`setup.sh` missing collector**
Create cannot finish without `scripts/collect_workload.py`.

**`self_check` overlay rewritten**
Do not overwrite files listed in `results/overlay_lock.json`.

**Remote SSH drop during setup**
Reconnect and resume `bash setup.sh --assume-yes`. Do not wipe `.venv` or `.cache`.

## 11. NVIDIA H100 Coding Differences

Native NVIDIA CUDA workload. Execute on the stated Ubuntu release with the host NVIDIA driver and CUDA userspace. ROCm porting notes do not apply.

## Repository layout

```text
.
├── setup.sh
├── run_benchmark.sh
├── benchmark_specification.json
├── .github/workflows/      # thin CI callers (see Continuous Integration)
├── config/
├── scripts/
├── src/
├── tests/
├── docs/
├── results/
└── LICENSE
```

## Continuous Integration

| Workflow | Runs on | When | What it does |
|---|---|---|---|
| [CI](.github/workflows/ci.yml) | GitHub-hosted runner | every pull request, and every push to `main` | shellcheck, ruff, `bash -n`, `compileall`, `run_benchmark.sh --help`, specification schema, the results validator on a seeded fixture, required files, and actionlint. No GPU and no benchmark run. |
| [GPU Smoke Benchmark](.github/workflows/gpu-smoke.yml) | self-hosted runner labeled `gpu`, `nvidia`, `ubu2604` | only when started by hand: **Actions → GPU Smoke Benchmark → Run workflow** (choose `smoke`, `baseline` or `extended`) | Verifies the pre-provisioned GPU stack, records `results/environment.json` (driver, runtime, kernel, GPU), runs the profile with `--validate`, shows headline metrics on the run page, and uploads the results. |

Both files are short callers. The steps themselves live once, for every workload in the suite, in [`garys-gpu-benchmarks/shared-workflows`](https://github.com/garys-gpu-benchmarks/shared-workflows), pinned at `@v1`. The GPU workflow is never triggered by pull requests, so code from a fork cannot run on the GPU host.

### Running it as part of the NVIDIA Ubuntu 26.04 bundle

This repository is one of the 32 workloads in [`bundle-nvidia-ubuntu-2604`](https://github.com/garys-gpu-benchmarks/bundle-nvidia-ubuntu-2604), which holds them as git submodules. To put the whole bundle on a GPU host and run this workload from it:

```bash
git clone --recurse-submodules https://github.com/garys-gpu-benchmarks/bundle-nvidia-ubuntu-2604 /opt/benchmarks
cd /opt/benchmarks/404-gpu-bench-nvidia-sdc-ecc-integrity-ubu2604
bash run_benchmark.sh --profile smoke --validate
```
