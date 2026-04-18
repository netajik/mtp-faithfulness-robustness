# A Controlled Study of Explanation Faithfulness and Robustness in Large Language Models

**Student:** Kancharapu Netaji (M25AI2007)  
**Supervisor:** Dr. Deeksha Varshney  
**Institution:** IIT Jodhpur — School of AIDE  
**Status:** 🔬 Experiments in progress  

---

## Overview

This study measures the impact of faithfulness-oriented fine-tuning on adversarial robustness in large language models — whether the two axes co-move, dissociate, or remain independent under a controlled matched training design.

## Study Design

| Arm | Training Objective | Purpose |
|-----|--------------------|---------|
| A — Baseline | CE(answer) only | Control |
| B — Rationale | CE(answer + rationale) | Rationale text effect |
| C — Faithfulness | CE(answer) + contrastive faithfulness loss | Key arm |

**3 arms × 3 seeds = 9 checkpoints**, each evaluated on:
- Behavioral consistency / faithfulness (GSM8K test split)
- Adversarial robustness (AdvBench 200 prompts, fixed snapshot)
- Helpfulness (MT-Bench 80 prompts)
- Internal probing — Phase 1b (refusal direction, residual stream)

## Repository Structure
```
├── train/              Training scripts and LoRA config
├── eval/               Evaluation scripts + committed prompt files
│   └── private/        Local-only eval assets (gitignored; not on remote)
├── scripts/            Rationale generation, scoring, pilot selection
├── results/            Per-seed result tables (added after experiments)
│   ├── arm_a/          Results for Arm A (baseline)
│   ├── arm_b/          Results for Arm B (rationale)
│   ├── arm_c/          Results for Arm C (faithfulness)
│   └── pilot/          Pilot runs and scratch outputs
├── notebooks/          Colab notebooks
├── configs/            Model and eval configuration
└── docs/               Proposal and study design
```

## Artifact Policy

Raw AdvBench harmful prompt texts are **not** in this repository.  
SHA256 hashes of all 200 evaluation prompts are committed to  
`/eval/advbench_hashes.csv` for independent verification.  
Raw texts available to verified researchers on request.

## Reproducibility

All evaluation scripts, prompt templates, judge configs, and  
analysis rules are committed to this repository **before training begins**.  
No post-hoc metric selection.

## Citation

```bibtex
@mastersthesis{netaji2025faithfulness,
  title   = {A Controlled Study of Explanation Faithfulness and 
             Robustness in Large Language Models},
  author  = {Kancharapu Netaji},
  school  = {Indian Institute of Technology Jodhpur},
  year    = {2025}
}
```