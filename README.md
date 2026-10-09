# Stable Diffusion XL Inference Baseline Benchmark

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![CI](https://github.com/garys-gpu-benchmarks/424-gpu-bench-nvidia-sdxl-diffusers-latency-ubu2604/actions/workflows/ci.yml/badge.svg)](https://github.com/garys-gpu-benchmarks/424-gpu-bench-nvidia-sdxl-diffusers-latency-ubu2604/actions/workflows/ci.yml)

Target: Ubuntu 26.04 · NVIDIA · see Hardware Requirements. This is a host benchmark, not a laptop `pip install` project.

## Quick Start

```bash
git clone https://github.com/garys-gpu-benchmarks/424-gpu-bench-nvidia-sdxl-diffusers-latency-ubu2604.git
cd 424-gpu-bench-nvidia-sdxl-diffusers-latency-ubu2604
sudo bash setup.sh --assume-yes
bash run_benchmark.sh --profile smoke --validate
```
Results are written to `results/benchmark.db` and `results/summary.json`.

This workload is executed on the validation host after the repository is copied there. `setup.sh` and `run_benchmark.sh` do not open an outbound SSH session.

Prerequisites: Ubuntu 26.04; NVIDIA; Python 3.14.4; root or sudo for `setup.sh`. Framework: Bash, SQLite, Python, PyYAML, CUDA Runtime, PyTorch-CUDA, diffusers, Stable Diffusion XL (baseline/extended). Set HF_TOKEN when the model license requires a Hugging Face token. This is a host benchmark, not a laptop `pip install` project.

```mermaid
flowchart LR
  setup.sh --> run_benchmark.sh --> parse_results.py --> results/benchmark.db
```

## 1. Overview

Runs scripts/collect_sdxl.py, which launches scripts/infer_sdxl.py --backend sdxl twice. Every profile loads real SDXL; smoke uses reduced resolution and steps. precision, scheduler, image_resolution, num_images, num_inference_steps, guidance_scale, and seed come from yaml. Writes pass1, pass2, and summary rows. Sweep dimensions: model_name, precision, scheduler, prompt_source, prompt_len, image_resolution, batch_size, num_images.

## 2. What It Validates

- Validates SDXL image-generation latency, images/s, and peak VRAM. Every profile loads the real SDXL pipeline; smoke uses reduced resolution and steps
- #1: Per-image latency (per_image_latency_sec); is present and physically sensible.
- #2: Aggregate throughput (images_per_sec); is present and physically sensible.
- #3: Peak VRAM, GB (peak_vram_gb); is present and physically sensible.
- #4: Denoising step time per batch, ms (step_time_ms); is present and physically sensible.
- #5: First-image latency, cold (first_image_latency_s) is present and physically sensible.

## 3. Metrics Captured

- **#1: Per-image latency** — stored as `per_image_latency_sec`.
- **#2: Aggregate throughput** — stored as `images_per_sec`.
- **#3: Peak VRAM, GB** — stored as `peak_vram_gb`.
- **#4: Denoising step time per batch, ms** — stored as `step_time_ms`.
- **#5: First-image latency, cold** — stored as `first_image_latency_s`.

## 4. Hardware Requirements

### Supported environment

- OS: Ubuntu 26.04
- GPU vendor: NVIDIA
- Framework family: Bash, SQLite, Python, PyYAML, CUDA Runtime, PyTorch-CUDA, diffusers, Stable Diffusion XL (baseline/extended)
- Python: Python 3.14.4

### Reference validation environment

The tables below describe the machine used to generate the reference results. They are not a requirement that every user buy that exact cloud instance.

### System

Runs scripts/collect_sdxl.py, which launches scripts/infer_sdxl.py --backend sdxl twice. Every profile loads real SDXL; smoke uses reduced resolution and steps. precision, scheduler, image_resolution, num_images, num_inference_steps, guidance_scale, and seed come from yaml.

### GPU

Ubuntu 26.04 / NVIDIA / Bash, SQLite, Python, PyYAML, CUDA Runtime, PyTorch-CUDA, diffusers, Stable Diffusion XL (baseline/extended)

## 5. Software Requirements

| Component | Version |
|---|---|
| OS | Ubuntu 26.04 |
| Kernel | kernel 7.0.0 |
| Python | Python 3.14.4 |
| ROCm | CUDA 13.3 |
| rocBLAS | cuBLAS (bundled with CUDA 13.3) |

Runs scripts/collect_sdxl.py, which launches scripts/infer_sdxl.py --backend sdxl twice. Every profile loads real SDXL; smoke uses reduced resolution and steps. precision, scheduler, image_resolution, num_images, num_inference_steps, guidance_scale, and seed come from yaml.

## 6. Installation

```bash
Run scripts/collect_sdxl.py --backend sdxl. Every profile loads real SDXL; smoke uses reduced resolution and steps
```

## 7. Running the Benchmark

```bash
Run scripts/collect_sdxl.py --backend sdxl. Every profile loads real SDXL; smoke uses reduced resolution and steps
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

raw_results.csv with pass1, pass2, and summary. collect_sdxl.py launches infer_sdxl.py twice

check,status,model_name,precision,scheduler,image_resolution,num_images,num_inference_steps,per_image_latency_sec,images_per_sec,peak_vram_gb,step_time_ms,first_image_latency_s
pass1,ok,stabilityai/stable-diffusion-xl-base-1.0,fp16,euler,1024,1,20,2.5,0.4,10,80,4.0

```bash
Run scripts/collect_sdxl.py --backend sdxl. Every profile loads real SDXL; smoke uses reduced resolution and steps
```

### `results/summary.json`

Consolidated metrics from the most recent run — suitable for CI artifact upload or dashboard ingestion.

### `results/raw/<timestamp>.txt`

raw_results.csv with pass1, pass2, and summary. collect_sdxl.py launches infer_sdxl.py twice

check,status,model_name,precision,scheduler,image_resolution,num_images,num_inference_steps,per_image_latency_sec,images_per_sec,peak_vram_gb,step_time_ms,first_image_latency_s
pass1,ok,stabilityai/stable-diffusion-xl-base-1.0,fp16,euler,1024,1,20,2.5,0.4,10,80,4.0

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
cd /opt/benchmarks/424-gpu-bench-nvidia-sdxl-diffusers-latency-ubu2604
bash run_benchmark.sh --profile smoke --validate
```
