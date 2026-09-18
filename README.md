<div align="center">

# DRTrack: Decoupled Modality Low-Rank Adaptation for RGB-T Tracking

**Feng Xuanling, Zhu Bin, Chen Yi, Yu Xiaotong, He Gaoxiang**

*National University of Defense Technology, Changsha, China*

---

> **Repository under construction.**
> The full training code, model weights, and evaluation scripts will be released **upon paper acceptance**.
> Please stay tuned.

</div>

## News

- **[2026-04]** The manuscript has been received by *IEEE Transactions on Circuits and Systems for Video Technology (TCSVT)*.
- **Code release**: planned upon acceptance of the paper.

## Introduction

DRTrack is a parameter-efficient RGB-thermal (RGB-T) tracker designed for edge deployment in counter-drone and surveillance systems. Instead of fine-tuning the entire backbone or stacking cross-modal fusion modules, DRTrack:

- freezes an 86.6M-parameter ViT-B backbone and tunes only **0.15M** adaptation parameters through **D**ecoupled **M**odality **L**ow-**R**ank **A**daptation (DM-LoRA) — a rank-2 low-rank branch per modality at every Transformer block's qkv projection;
- runs a **fusion-free dual-stream** forward: each modality traverses the frozen backbone independently, and the two search representations merge by parameter-free element-wise addition;
- shows via post-training subspace analysis that the RGB and TIR adapters occupy **nearly orthogonal subspaces** (mean principal angle **86.2°**), i.e., the two modalities adapt along non-interfering directions.

On the Anti-UAV benchmark, DRTrack attains **0.668** state accuracy with 0.15M adaptation parameters at **58.4 FPS**, matching prompt-tuned ViPT (0.661) at one-seventh of its adaptation budget and surpassing SUTrack by 4.5 points. The same checkpoint transfers to LasHeR and RGBT234 without re-training, where it outperforms in-benchmark-trained RGB-T specialists on LasHeR.

## Abstract

Small unmanned aircraft threaten airspace security. Vision-based tracking forms the core of counter-drone systems deployed on power- and latency-constrained platforms. Visible cameras capture dense texture in daylight but fail in darkness or glare; thermal imaging operates regardless of illumination but offers coarser resolution and suffers thermal crossover. Pairing the two modalities provides sensor redundancy critical for operational robustness. Current RGB-T trackers either fine-tune entire backbones or cascade cross-modal fusion modules, limiting their scalability to edge deployment. We present DRTrack, which freezes an 86.6M ViT-B backbone and tunes only 0.15M parameters via DM-LoRA: a rank-2 low-rank adapter per modality at each Transformer block's qkv projection. RGB and thermal tokens traverse the frozen backbone in two passes, each activating its own adapter, and search features merge through parameter-free element-wise addition. Post-training subspace analysis shows the two adapters occupy nearly orthogonal directions (mean principal angle 86.2°), statistically distinct from random initialization yet preserving modality-specific separation. On Anti-UAV, DRTrack scores 0.668 state accuracy with 0.15M tunable parameters, matching ViPT (0.661) at one-seventh the adaptation cost and exceeding SUTrack by 4.5 points, while running at 58.4 FPS. Single-modality ablations expose a design trade-off inherent to RGB-T tracking: RGB-only attains 0.689 SA on Anti-UAV's predominantly daylight test set (81% well-lit frames), but the dual design prevents catastrophic failure when visible imaging degrades; per-attribute analysis shows DRTrack gains +0.06 SA in out-of-view scenarios and stays robust across illumination conditions where single-stream methods collapse. For deployments where sensor fault tolerance outweighs benchmark peaks, DRTrack offers parameter-efficient multimodal tracking.

## Method Overview

<div align="center">
  <img src="docs/fig_architecture.png" width="90%" alt="DRTrack architecture">
</div>

Each modality traverses the frozen ViT-B with its own rank-2 DM-LoRA branch; the two search sequences are summed and passed, together with the RGB template, to a compact center-prediction head. No learned fusion module exists.

## Key Results

### State-of-the-art comparison on Anti-UAV (state accuracy, benchmark coordinates)

| Tracker | Modality | SA<sub>IR</sub> | SA<sub>Vis</sub> | Speed (FPS) | Adaptation params |
|---|---|---|---|---|---|
| **DRTrack (ours)** | RGB-T | — | **0.668** | **58.4** | **0.15M** |
| ViPT (locally fine-tuned) | RGB-T | — | 0.661 | 56.0 | 1.09M |
| SUTrack | RGB-T | — | 0.623 | — | — |
| DFSC (full fine-tuning) | RGB-T | 0.660 | 0.698 | — | full |

### Ablation highlights on Anti-UAV (SA)

| Variant | SA | Takeaway |
|---|---|---|
| RGB-only (rank-2) | **0.689** | RGB dominates Anti-UAV's daylight distribution |
| **DRTrack (dual rank-2, no fusion)** | **0.668** | the adopted design |
| Shared rank-4 adapter (equal capacity) | 0.617 | decoupling is worth +0.051 |
| Fusion-only (5.0M fusion modules, no LoRA) | 0.604 | adaptation beats fusion machinery by +0.064 |
| Dual LoRA + 5.0M fusion modules | 0.667 | fusion is redundant atop decoupled adaptation |
| TIR-only (rank-2) | 0.216 | the thermal stream earns its keep in failure cases |

### Cross-dataset transfer (per-sequence-mean protocol, zero-shot)

| Tracker | LasHeR SR<sub>AUC</sub> | RGBT234 SR<sub>AUC</sub> |
|---|---|---|
| **DRTrack (ours)** | 0.461 | 0.537 |
| ViPT | 0.540 | 0.616 |
| Best in-benchmark RGB-T specialist | 0.377 (APFNet) | 0.585 (DMCNet) |

## Citation

If you find this work useful in your research, please cite:

```bibtex
@article{feng2026drtrack,
  title   = {DRTrack: Decoupled Modality Low-Rank Adaptation for RGB-T Tracking},
  author  = {Feng, Xuanling and Zhu, Bin and Chen, Yi and Yu, Xiaotong and He, Gaoxiang},
  journal = {IEEE Transactions on Circuits and Systems for Video Technology},
  note    = {under review},
  year    = {2026}
}
```

## Contact

- Feng Xuanling (corresponding author): fengxuanling22@nudt.edu.cn
- Open an issue in this repository for questions and discussions.

## License

The code and model weights will be released under a permissive open-source license upon paper acceptance.
