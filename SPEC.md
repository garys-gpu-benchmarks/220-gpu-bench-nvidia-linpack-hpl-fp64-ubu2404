# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Builds and runs local bin/nvhpl (cuSOLVER FP64 dense solve), not an NVIDIA HPC Benchmarks container. It runs on one GPU; cuSOLVER picks its own block size and factorization, so there is no num_gpus, process grid, block_size or panel_factorization parameter. precision is FP64. problem_size_N is clamped from >=65536 down to 32768. Sweep dimensions: problem_size_N, warmup_iters, num_iterations.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| problem_size_N | `--problem-size-n` | smoke=4096, baseline=32768, extended=32768 | 32768 | From Parameter list; see Execution Description With Parameters. |
| warmup_iters | `--warmup-iters` | smoke=0, baseline=0, extended=0 | 0 | From Parameter list; see Execution Description With Parameters. |
| num_iterations | `--num-iterations` | smoke=1, baseline=3, extended=10 | 3 | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Build src/nvhpl.cu (cuSOLVER LU) with scripts/build.sh, then run bin/nvhpl --N
```

## Raw Output Format

Local bin/nvhpl stdout plus a normalized CSV row. This is not mpirun, HPL.dat, or an HPC container log

sample_index,status,solver_implementation,requested_problem_size_n,actual_problem_size_n,timed_repeats,sustained_fp64_performance_across_problem_size_n_tflops,percent_of_fp64_tensor_peak,time_to_solution_across_problem_size_n_s,error_message
0,ok,cusolver,65536,32768,6,8.0,20,12,

## Metrics

- **#1: Mean cuSOLVER FP64 dense-LU throughput, TFLOPS** — stored as `sustained_fp64_performance_across_problem_size_n_tflops`.
- **#2: Mean cuSOLVER dense-LU time, s** — stored as `time_to_solution_across_problem_size_n_s`.
- **#3: Mean percent of FP64 tensor-core peak** — stored as `percent_of_fp64_tensor_peak`.

## Framework

Builds and runs local bin/nvhpl (cuSOLVER FP64 dense solve), not an NVIDIA HPC Benchmarks container. It runs on one GPU; cuSOLVER picks its own block size and factorization, so there is no num_gpus, process grid, block_size or panel_factorization parameter. precision is FP64.

## Installation and Execution Summary

Build and run bin/nvhpl (cuSOLVER FP64 dense solve) for yaml warmup plus timed repeats, parse GFLOPS, time-to-solution, and Residual Check, and abort if that check fails, to measure sustained FP64 Linpack. N at or above 65536 is clamped to 32768. Not mpirun, HPL.dat, or an NVIDIA HPC container

## Platform Portability

- **AMD (primary):** ```bash
Build src/nvhpl.cu (cuSOLVER LU) with scripts/build.sh, then run bin/nvhpl --N
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

Local bin/nvhpl stdout plus a normalized CSV row. This is not mpirun, HPL.dat, or an HPC container log

sample_index,status,solver_implementation,requested_problem_size_n,actual_problem_size_n,timed_repeats,sustained_fp64_performance_across_problem_size_n_tflops,percent_of_fp64_tensor_peak,time_to_solution_across_problem_size_n_s,error_message
0,ok,cusolver,65536,32768,6,8.0,20,12,

```bash
bash run_benchmark.sh --help
bash run_benchmark.sh --smoke
bash run_benchmark.sh --baseline
bash run_benchmark.sh --extended
```
`run_benchmark.sh --help` prints usage and exits. The harness calls `scripts/ensure_setup.sh` when `.setup_state` is absent.

### Required integrity checks (built into `validate_results.py`)

1. Latest run exists and `runs.status = 'ok'`.
2. `run.error_message` is NULL.
3. `started_at` and `finished_at` are valid ISO-8601 UTC strings.
4. All required aggregate metrics in `runs` are non-NULL and finite.
5. All required aggregate metrics are physically sensible (positive values). Builds and runs local bin/nvhpl (cuSOLVER FP64 dense solve), not an NVIDIA HPC Benchmarks container. It runs on one GPU; cuSOLVER picks its own block size and factorization, so there is no num_gpus, process grid, block_size or panel_factorization parameter. precision is FP64.
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Builds and runs local bin/nvhpl (cuSOLVER FP64 dense solve), not an NVIDIA HPC Benchmarks container. It runs on one GPU; cuSOLVER picks its own block size and factorization, so there is no num_gpus, process grid, block_size or panel_factorization parameter. precision is FP64.

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
