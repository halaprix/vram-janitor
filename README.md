# VramJanitor

A local-first CLI that finds reclaimable desktop VRAM before you launch a local LLM.

## Problem

Local LLM users on Linux often squeeze models into limited VRAM. A few minimized hardware-accelerated apps — Discord, Steam, Telegram, browsers, Electron apps — can quietly reserve hundreds of MiB each. The current fix is manual: run `nvidia-smi`, guess which PIDs are desktop apps, close things, retry the model, and repeat.

That wastes time, makes benchmark results noisy, and pushes people toward smaller models even when the real issue is reclaimable desktop VRAM.

## Evidence

| Source | Link | Signal |
|---|---|---|
| Reddit / r/LocalLLaMA | https://www.reddit.com/r/LocalLLaMA/comments/1um5ik2/pay_attention_a_few_chats_waiting_in_tray_reserve/ | Fresh post says minimized hardware-accelerated apps reserved 1 GB+ VRAM and suggests using `nvidia-smi` plus disabling acceleration or closing apps. |
| DigitalOcean tutorial | https://www.digitalocean.com/community/tutorials/monitoring-gpu-utilization-in-real-time | `nvidia-smi`, `pmon`, `nvtop`, and `gpustat` are standard GPU monitoring primitives, but they are general-purpose, not local-LLM preflight advisors. |
| nvitop | https://github.com/XuehaiPan/nvitop | Mature interactive NVIDIA process viewer; strong direct substitute for seeing processes, not a narrow reclaim-before-inference workflow. |
| LACT | https://github.com/ilya-zlobintsev/LACT | Mature Linux GPU configuration and monitoring GUI; broader than a one-command local LLM VRAM cleanup check. |
| LLM Configurator | https://llmconfigurator.com/ | Shows demand for GPU/VRAM fit checks for local LLMs, but focuses on model/hardware compatibility rather than currently reclaimable desktop VRAM. |

## Competitor / Substitute Check

| Type | Name / Substitute | Notes |
|---|---|---|
| Direct competitor | `nvidia-smi`, `nvidia-smi pmon`, `nvtop`, `nvitop`, `gpustat` | Excellent visibility tools. They still leave users to identify which PIDs are safe-to-close desktop apps and estimate whether reclaiming them changes the model fit. |
| Direct competitor | LACT and broader GPU control dashboards | Good for monitoring/configuration, but not scoped to preflight local LLM launch decisions. |
| Indirect substitute | Shell one-liners, process killing, turning off app hardware acceleration manually | Works for experts but is brittle, vendor-specific, and easy to forget. |
| Status quo | Close random apps, lower context/model size, reboot, or accept slower CPU/RAM offload | Wastes >30 minutes across setup/debug sessions and can make users buy hardware or pick worse models unnecessarily. |

## Wedge

VramJanitor wins by being narrower than GPU dashboards: one command before inference, one red/yellow/green answer, and one safe reclaim plan. It does not try to replace `nvitop`; it wraps the existing primitives into a local-LLM-specific decision:

> Can I fit this model/context if I close or reconfigure these obvious desktop VRAM hogs?

The distribution path is concrete: r/LocalLLaMA troubleshooting posts, GitHub README demos, and searchable guides for "Discord Steam Telegram VRAM local LLM" / "nvidia-smi desktop app VRAM".

## Target user

Linux desktop users running Ollama, llama.cpp, LM Studio, vLLM, or similar local inference stacks on NVIDIA GPUs, especially 8–24 GB cards where 0.5–2 GB of reclaimable VRAM changes which model/context fits.

## MVP

- `vram-janitor scan`: collect GPU memory usage via `nvidia-smi` and map PIDs to process names/desktop entries.
- Known-app rules for common hardware-accelerated apps: browsers, Discord, Steam, Telegram, Slack, Electron shells.
- `--target-vram` and `--model-budget` modes that estimate whether reclaiming selected processes changes fit.
- Safe action plan: close app, disable hardware acceleration, use a non-accelerated browser profile, or retry after freeing memory.
- Markdown report export for forum/GitHub support threads with private command output redacted.

## Non-goals

- No automatic process killing in v0.1.
- No GPU overclocking, fan curves, or power tuning.
- No cloud telemetry or hosted dashboard.
- No vendor account integration.

## Status

v0.1.0-alpha.0 — scaffold/spec only.
