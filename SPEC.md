# SPEC — VramJanitor

## User story

As a local LLM user on a Linux desktop, I want a one-command preflight check that shows reclaimable desktop VRAM, so that I can fit the intended model/context without blind app closing, rebooting, or unnecessary model downgrades.

## Core flow

1. User runs `vram-janitor scan` before starting a model.
2. The CLI reads GPU process memory from `nvidia-smi` and maps PIDs to process metadata.
3. It classifies processes into likely desktop/browser/chat/game launchers, model/runtime processes, unknown processes, and system/display overhead.
4. The user optionally passes `--target-free-mib` or `--model-budget-mib`.
5. VramJanitor prints a ranked reclaim plan with safe suggestions and caveats.
6. User closes/reconfigures apps manually and reruns the scan.
7. Optional `--report report.md` exports a redacted support artifact.

## Data model

```text
GpuSnapshot
- collected_at
- driver_version
- gpu_name
- total_mib
- used_mib
- free_mib
- processes[]

GpuProcess
- pid
- process_name
- command_basename
- used_mib
- classification: model_runtime | desktop_app | browser | game_launcher | system_display | unknown
- confidence: low | medium | high
- suggested_action

ReclaimPlan
- current_free_mib
- target_free_mib
- reclaimable_mib
- recommended_steps[]
- warnings[]
```

## Technical approach

- Python CLI using `typer` or `argparse` for the first slice.
- `subprocess.run(["nvidia-smi", "--query-compute-apps=pid,process_name,used_memory", "--format=csv,noheader,nounits"])` for process rows.
- `/proc/<pid>/cmdline` and `/proc/<pid>/comm` for better names when readable.
- Rule table for common desktop VRAM hogs, kept in a plain YAML/JSON file.
- No privileged operations required for v0.1.
- Tests use fixture outputs instead of requiring a real NVIDIA GPU in CI.

## Validation plan

- Unit tests for parsing `nvidia-smi` CSV and malformed/no-GPU outputs.
- Fixture scenarios: clean desktop, Discord/Steam/browser hogs, active model process, unknown CUDA job.
- Manual test on one NVIDIA Linux host before v0.1.0-alpha.1.
- Wedge validation: publish a README example against the Reddit pain point and check whether r/LocalLLaMA users find the report clearer than raw `nvidia-smi`.

## Milestones

- v0.1.0-alpha.0 — repo scaffold.
- v0.1.0-alpha.1 — runnable `scan` with fixture tests and markdown report.
- v0.2.0-alpha.1 — model-budget estimates for Ollama/llama.cpp common quant sizes.
- v0.3.0-alpha.1 — optional desktop-entry hints for disabling hardware acceleration.
