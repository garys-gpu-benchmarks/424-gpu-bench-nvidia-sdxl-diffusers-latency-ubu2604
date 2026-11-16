# Results: 424 Stable Diffusion XL Inference Baseline

**Main goal:** Measure SDXL image-generation performance  
**Tools:** Run scripts/collect_sdxl.py --backend sdxl with real SDXL in every profile; smoke uses reduced resolution and steps

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_142 · recorded 2026-11-16). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× NVIDIA H100 NVL (95830 MiB) · AMD EPYC 9374F 32-Core Processor · Ubuntu 26.04.1 LTS · kernel 6.8.0-138-generic · driver 595.91.07
- **Run:** 2026-11-16T11:29:06Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 01:25 · benchmark 00:01 · whole run 01:37
- **Source:** run 424_20261116_112906
- **Command:** `bash run_benchmark.sh --smoke --model-name sdxl-base-1.0 --precision fp16 --scheduler euler --prompt-source synthetic --image-resolution 64 --num-images 1 --num-inference-steps 2 --guidance-scale 0 --seed 42 --prompt-len 32 --batch-size 1`

| # | Metric | Result |
|---|---|---|
| 1 | Per-image latency (per_image_latency_sec) | 0.118299 |
| 2 | Aggregate throughput (images_per_sec) | 8.453156831418694 |
| 3 | Peak VRAM, GB (peak_vram_gb) | 8.172601 |
| 4 | Denoising step time per batch, ms (step_time_ms) | 43.051248 |
| 5 | First-image latency, cold (first_image_latency_s) | 4.486358 |
<!-- /profile:smoke -->
