# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Builds and runs bin/ecc_walk from src/ecc_walk.cu to walk a cudaMalloc buffer and query nvidia-smi ECC text. device_id: CUDA device index. memory_size: allocation size in GB. num_iterations: verify loops. Sweep dimensions: device_id, memory_size, pattern_type, ecc_check, num_iterations, output_format, duration, count.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| device_id | `--device-id` | smoke=0, baseline=0, extended=0 | 0 | From Parameter list; see Execution Description With Parameters. |
| memory_size | `--memory-size` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| pattern_type | `--pattern-type` | smoke=walking-1s, baseline=walking-1s, extended=walking-1s | walking-1s | From Parameter list; see Execution Description With Parameters. |
| ecc_check | `--ecc-check` | smoke=true, baseline=true, extended=true | true | From Parameter list; see Execution Description With Parameters. |
| num_iterations | `--num-iterations` | smoke=2, baseline=3, extended=9 | 3 | From Parameter list; see Execution Description With Parameters. |
| output_format | `--output-format` | smoke=csv, baseline=csv, extended=csv | csv | From Parameter list; see Execution Description With Parameters. |
| duration | `--duration` | smoke=20000, baseline=183000, extended=730000 | 183000 | From Parameter list; see Execution Description With Parameters. |
| count | `--count` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| seed | `--seed` | smoke=0, baseline=0, extended=0 | 0 | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Compile src/ecc_walk.cu with nvcc; run bin/ecc_walk; query nvidia-smi -q -d ECC
```

## Raw Output Format

CSV aggregate of the ECC walk, written as sample_index 0 and 1

sample_index,status,uncorrected_ecc_errors_count,corrected_ecc_errors_count,uncorrected_ecc_aggregate_count,corrected_ecc_aggregate_count,uncorrected_ecc_nonzero_field_count,corrected_ecc_nonzero_field_count,hbm3_coverage_gb,walking_1s_moving_inversion_pass_rate,test_wall_clock_completion_time_s,error_message
0,ok,0,0,0,0,0,0,1.0,100,2.0,

## Metrics

- **#1: Uncorrected ECC errors** — stored as `uncorrected_ecc_errors_count`.
- **#2: Corrected ECC errors** — stored as `corrected_ecc_errors_count`.
- **#3: Wall-clock time, s** — stored as `test_wall_clock_completion_time_s`.
- **#4: Device buffer size, GiB** — stored as `hbm3_coverage_gb`.
- **#5: Fixed-pattern fill/verify pass rate** — stored as `walking_1s_moving_inversion_pass_rate`.

## Framework

Builds and runs bin/ecc_walk from src/ecc_walk.cu to walk a cudaMalloc buffer and query nvidia-smi ECC text. device_id: CUDA device index. memory_size: allocation size in GB.

## Installation and Execution Summary

Compile src/ecc_walk.cu with nvcc and run bin/ecc_walk over a cudaMalloc buffer with two fixed fills (0x00000001 and 0xFFFFFFFF) until yaml num_iterations or duration, then count Correctable/Uncorrectable tokens from nvidia-smi -q -d ECC, to measure SDC pass rate and ECC text hits. DCGM is not called

## Platform Portability

- **AMD (primary):** ```bash
Compile src/ecc_walk.cu with nvcc; run bin/ecc_walk; query nvidia-smi -q -d ECC
```
- **NVIDIA:** Native NVIDIA CUDA workload. Execute on the stated Ubuntu release with the host NVIDIA driver and CUDA userspace. ROCm porting notes do not apply.

## Model Context Protocols

- **Active:** None

## Execution-Loop Validation Contract

EXECUTION CHAIN: `run_benchmark.sh` ➔ raw output ➔ `scripts/parse_results.py` ➔ `results/benchmark.db` ➔ `scripts/validate_results.py`

This benchmark uses a lightweight, SQLite-integrated execution loop for result validation. All validation is performed by `scripts/validate_results.py`.

### Validation script usage

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional; select the installed interpreter

# After a live run:
".venv/bin/python" scripts/validate_results.py --db results/benchmark.db

# CI / no-GPU path (seeds fixture and validates it):
".venv/bin/python" scripts/validate_results.py --seed-fixture --quiet

# Override DB path via environment variable:
BENCHMARK_DB=tests/fixtures/benchmark.db \
  ".venv/bin/python" scripts/validate_results.py
```

### Run artifact contract

CSV aggregate of the ECC walk, written as sample_index 0 and 1

sample_index,status,uncorrected_ecc_errors_count,corrected_ecc_errors_count,uncorrected_ecc_aggregate_count,corrected_ecc_aggregate_count,uncorrected_ecc_nonzero_field_count,corrected_ecc_nonzero_field_count,hbm3_coverage_gb,walking_1s_moving_inversion_pass_rate,test_wall_clock_completion_time_s,error_message
0,ok,0,0,0,0,0,0,1.0,100,2.0,

```bash
bash run_benchmark.sh --help
bash run_benchmark.sh --profile smoke --validate
bash run_benchmark.sh --profile baseline --validate
bash run_benchmark.sh --profile extended --validate
```
`run_benchmark.sh --help` prints usage and exits. The harness calls `scripts/ensure_setup.sh` when `.setup_state` is absent.

### Required integrity checks (built into `validate_results.py`)

1. Latest run exists and `runs.status = 'ok'`.
2. `run.error_message` is NULL.
3. `started_at` and `finished_at` are valid ISO-8601 UTC strings.
4. All required aggregate metrics in `runs` are non-NULL and finite.
5. All required aggregate metrics are physically sensible (positive values). Builds and runs bin/ecc_walk from src/ecc_walk.cu to walk a cudaMalloc buffer and query nvidia-smi ECC text. device_id: CUDA device index. memory_size: allocation size in GB.
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Builds and runs bin/ecc_walk from src/ecc_walk.cu to walk a cudaMalloc buffer and query nvidia-smi ECC text. device_id: CUDA device index. memory_size: allocation size in GB.

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
