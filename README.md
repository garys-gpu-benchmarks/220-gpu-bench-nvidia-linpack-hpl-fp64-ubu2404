# NVIDIA HPL FP64 (High Performance Linpack) Benchmark

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![CI](https://github.com/garys-gpu-benchmarks/220-gpu-bench-nvidia-linpack-hpl-fp64-ubu2404/actions/workflows/ci.yml/badge.svg)](https://github.com/garys-gpu-benchmarks/220-gpu-bench-nvidia-linpack-hpl-fp64-ubu2404/actions/workflows/ci.yml)

Target: Ubuntu 24.04 · NVIDIA · see Hardware Requirements. This is a host benchmark, not a laptop `pip install` project.

## Quick Start

```bash
git clone https://github.com/garys-gpu-benchmarks/220-gpu-bench-nvidia-linpack-hpl-fp64-ubu2404.git
cd 220-gpu-bench-nvidia-linpack-hpl-fp64-ubu2404
sudo bash setup.sh --assume-yes
bash run_benchmark.sh --profile smoke --validate
```
Results are written to `results/benchmark.db` and `results/summary.json`.

This workload is executed on the validation host after the repository is copied there. `setup.sh` and `run_benchmark.sh` do not open an outbound SSH session.

Prerequisites: Ubuntu 24.04; NVIDIA; Python 3.12.3; root or sudo for `setup.sh`. Framework: Bash, SQLite, Python, PyYAML, CUDA Runtime, NVCC, cuSOLVER, cuBLAS (GEMM). This is a host benchmark, not a laptop `pip install` project.

```mermaid
flowchart LR
  setup.sh --> run_benchmark.sh --> parse_results.py --> results/benchmark.db
```

## 1. Overview

Builds and runs local bin/nvhpl (cuSOLVER FP64 dense solve), not an NVIDIA HPC Benchmarks container. num_gpus=1 and process_grid P×Q=1×. precision is FP64. problem_size_N is clamped from >=65536 down to 32768. Sweep dimensions: num_gpus, process_grid_P, process_grid_Q, precision, problem_size_N, block_size, panel_factorization, warmup_iters.

## 2. What It Validates

- Validates FP64 Linpack GFLOPS, time-to-solution, and a passing residual check
- #1: Mean cuSOLVER FP64 dense-LU throughput, TFLOPS (sustained_fp64_performance_across_problem_size_n_tflops); is present and physically sensible.
- #2: Mean cuSOLVER dense-LU time, s (time_to_solution_across_problem_size_n_s); is present and physically sensible.
- #3: Mean percent of FP64 vector peak (percent_of_fp64_vector_peak) is present and physically sensible.

## 3. Metrics Captured

- **#1: Mean cuSOLVER FP64 dense-LU throughput, TFLOPS** — stored as `sustained_fp64_performance_across_problem_size_n_tflops`.
- **#2: Mean cuSOLVER dense-LU time, s** — stored as `time_to_solution_across_problem_size_n_s`.
- **#3: Mean percent of FP64 vector peak** — stored as `percent_of_fp64_vector_peak`.

## 4. Hardware Requirements

### Supported environment

- OS: Ubuntu 24.04
- GPU vendor: NVIDIA
- Framework family: Bash, SQLite, Python, PyYAML, CUDA Runtime, NVCC, cuSOLVER, cuBLAS (GEMM)
- Python: Python 3.12.3

### Reference validation environment

The tables below describe the machine used to generate the reference results. They are not a requirement that every user buy that exact cloud instance.

### System

Builds and runs local bin/nvhpl (cuSOLVER FP64 dense solve), not an NVIDIA HPC Benchmarks container. num_gpus=1 and process_grid P×Q=1×. precision is FP64.

### GPU

Ubuntu 24.04 / NVIDIA / Bash, SQLite, Python, PyYAML, CUDA Runtime, NVCC, cuSOLVER, cuBLAS (GEMM)

## 5. Software Requirements

| Component | Version |
|---|---|
| OS | Ubuntu 24.04 |
| Kernel | kernel 6.8.0 |
| Python | Python 3.12.3 |
| ROCm | CUDA 12.8 |
| rocBLAS | cuBLAS (bundled with CUDA 12.8) |

Builds and runs local bin/nvhpl (cuSOLVER FP64 dense solve), not an NVIDIA HPC Benchmarks container. num_gpus=1 and process_grid P×Q=1×. precision is FP64.

## 6. Installation

```bash
Build nvhpl via scripts/build.sh; run bin/nvhpl for warmup plus timed repeats
```

## 7. Running the Benchmark

```bash
Build nvhpl via scripts/build.sh; run bin/nvhpl for warmup plus timed repeats
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

Local bin/nvhpl stdout plus a normalized CSV row. This is not mpirun, HPL.dat, or an HPC container log

sample_index,status,solver_implementation,requested_problem_size_n,actual_problem_size_n,timed_repeats,sustained_fp64_performance_across_problem_size_n_tflops,percent_of_fp64_vector_peak,time_to_solution_across_problem_size_n_s,error_message
0,ok,cusolver,65536,32768,6,8.0,20,12,

```bash
Build nvhpl via scripts/build.sh; run bin/nvhpl for warmup plus timed repeats
```

### `results/summary.json`

Consolidated metrics from the most recent run — suitable for CI artifact upload or dashboard ingestion.

### `results/raw/<timestamp>.txt`

Local bin/nvhpl stdout plus a normalized CSV row. This is not mpirun, HPL.dat, or an HPC container log

sample_index,status,solver_implementation,requested_problem_size_n,actual_problem_size_n,timed_repeats,sustained_fp64_performance_across_problem_size_n_tflops,percent_of_fp64_vector_peak,time_to_solution_across_problem_size_n_s,error_message
0,ok,cusolver,65536,32768,6,8.0,20,12,

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
| [GPU Smoke Benchmark](.github/workflows/gpu-smoke.yml) | self-hosted runner labeled `gpu`, `nvidia`, `ubu2404` | only when started by hand: **Actions → GPU Smoke Benchmark → Run workflow** (choose `smoke`, `baseline` or `extended`) | Verifies the pre-provisioned GPU stack, records `results/environment.json` (driver, runtime, kernel, GPU), runs the profile with `--validate`, shows headline metrics on the run page, and uploads the results. |

Both files are short callers. The steps themselves live once, for every workload in the suite, in [`garys-gpu-benchmarks/shared-workflows`](https://github.com/garys-gpu-benchmarks/shared-workflows), pinned at `@v1`. The GPU workflow is never triggered by pull requests, so code from a fork cannot run on the GPU host.

### Running it as part of the NVIDIA Ubuntu 24.04 bundle

This repository is one of the 32 workloads in [`bundle-nvidia-ubuntu-2404`](https://github.com/garys-gpu-benchmarks/bundle-nvidia-ubuntu-2404), which holds them as git submodules. To put the whole bundle on a GPU host and run this workload from it:

```bash
git clone --recurse-submodules https://github.com/garys-gpu-benchmarks/bundle-nvidia-ubuntu-2404 /opt/benchmarks
cd /opt/benchmarks/220-gpu-bench-nvidia-linpack-hpl-fp64-ubu2404
bash run_benchmark.sh --profile smoke --validate
```
