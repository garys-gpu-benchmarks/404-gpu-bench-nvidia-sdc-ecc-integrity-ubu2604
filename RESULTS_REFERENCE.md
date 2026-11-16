# Results: 404 Silent Data Corruption (SDC)

**Main goal:** Analyze ECC memory integrity via a CUDA fixed-pattern verifier  
**Tools:** Compile src/ecc_walk.cu with nvcc; run bin/ecc_walk; query nvidia-smi -q -d ECC

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_143 · recorded 2026-11-16). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× NVIDIA H100 NVL (95830 MiB) · AMD EPYC 9374F 32-Core Processor · Ubuntu 26.04.1 LTS · kernel 6.8.0-138-generic · driver 595.91.07
- **Run:** 2026-11-16T19:04:04Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 00:27 · benchmark 00:22 · whole run 00:54
- **Source:** run 404_20261116_190404
- **Command:** `bash run_benchmark.sh --smoke --device-id 0 --memory-size 1 --pattern-type walking-1s --num-iterations 2 --duration 20000`

| # | Metric | Result |
|---|---|---|
| 1 | Uncorrected ECC errors (uncorrected_ecc_errors_count) | 0.0 |
| 2 | Corrected ECC errors (corrected_ecc_errors_count) | 0.0 |
| 3 | Wall-clock time, s (test_wall_clock_completion_time_s) | 20.93001079559326 |
| 4 | Device buffer size, GiB (device_buffer_gib) | 1.0 |
| 5 | Fixed-pattern fill/verify pass rate (fixed_pattern_fill_verify_pass_rate) | 100.0 |
<!-- /profile:smoke -->
