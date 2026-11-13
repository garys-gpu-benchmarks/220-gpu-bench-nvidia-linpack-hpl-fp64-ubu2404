# Results: 220 cuSOLVER FP64 Dense LU Solve (single GPU)

**Main goal:** Measure sustained FP64 dense LU solve (getrf + getrs) throughput with cuSOLVER on one GPU  
**Tools:** Build src/nvhpl.cu (cuSOLVER LU) with scripts/build.sh, then run bin/nvhpl --N

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_138 · recorded 2026-11-12). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× NVIDIA H100 NVL (95830 MiB) · AMD EPYC 9374F 32-Core Processor · Ubuntu 24.04.5 LTS · kernel 6.8.0-138-generic · driver 595.91.07
- **Run:** 2026-11-12T23:10:15Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 00:30 · benchmark 00:05 · whole run 00:42
- **Source:** run 220_20261112_231015
- **Command:** `bash run_benchmark.sh --smoke --problem-size-N 4096 --warmup-iters 0 --num-iterations 1`

| # | Metric | Result |
|---|---|---|
| 1 | Mean cuSOLVER FP64 dense-LU throughput, TFLOPS (sustained_fp64_performance_across_problem_size_n_tflops) | 0.703935 |
| 2 | Mean cuSOLVER dense-LU time, s (time_to_solution_across_problem_size_n_s) | 0.06509999999999999 |
| 3 | Mean percent of FP64 tensor-core peak (percent_of_fp64_tensor_peak) | 1.1670261461205231 |
<!-- /profile:smoke -->
