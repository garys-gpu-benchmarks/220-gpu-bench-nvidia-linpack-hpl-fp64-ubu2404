# Results: 220 cuSOLVER FP64 Dense LU Solve (single GPU)

**Main goal:** Measure sustained FP64 dense LU solve (getrf + getrs) throughput with cuSOLVER on one GPU  
**Tools:** Build src/nvhpl.cu (cuSOLVER LU) with scripts/build.sh, then run bin/nvhpl --N

Reference results from the validation run on the system shown in each section (recorded 2026-11-22). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× NVIDIA H100 80GB HBM3 (81559 MiB) · Intel(R) Xeon(R) Platinum 8470 · Ubuntu 24.04.5 LTS · kernel 6.8.0-149-generic · driver 595.112.07
- **Run:** 2026-11-22T11:07:02Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 00:55 · benchmark 00:04 · whole run 01:05
- **Source:** run 220_20261122_110702
- **Command:** `bash run_benchmark.sh --smoke --problem-size-N 4096 --warmup-iters 0 --num-iterations 1`

| # | Metric | Result |
|---|---|---|
| 1 | Mean cuSOLVER FP64 dense-LU throughput, TFLOPS (sustained_fp64_performance_across_problem_size_n_tflops) | 0.710105 |
| 2 | Mean cuSOLVER dense-LU time, s (time_to_solution_across_problem_size_n_s) | 0.0645 |
| 3 | Mean percent of FP64 tensor-core peak (percent_of_fp64_tensor_peak) | 1.0613123653005165 |
<!-- /profile:smoke -->
