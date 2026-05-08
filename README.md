# A Controlled Study of Explanation Faithfulness and Robustness in Large Language Models

**Student:** Kancharapu Netaji (M25AI2007)
**Supervisor:** Dr. Deeksha Varshney
**Institution:** IIT Jodhpur — School of AIDE
**Status:** 🔬 Experiments in progress

---

## Overview

This study investigates the interaction between explanation faithfulness and adversarial robustness in large language models under a controlled matched fine-tuning design. Three training arms are compared on the same model, data, LoRA configuration, and random seeds — the only variable is the loss function. Every checkpoint is evaluated on faithfulness, adversarial robustness, and helpfulness. A probing analysis examines whether faithfulness-oriented training and safety robustness are consistent with partial dissociation in probe-defined reasoning-associated and safety-associated directions in the residual stream.

The study does not assume faithfulness-oriented training necessarily improves behavioural faithfulness — it evaluates whether such interventions measurably alter behavioural and representational properties, with all four outcome cases (co-movement, dissociation, null, high-variance) treated as informative results.

**Contribution:** Prior work evaluates faithfulness and robustness independently, or under confounded training conditions (different models, datasets, or tuning procedures). To our knowledge, prior work has not jointly combined controlled matched-arm intervention, joint behavioural and representational evaluation on the same checkpoints, and mechanistic evidence for representational dissociation under a matched-loss design. Results are interpreted as evidence consistent with partial dissociation between probe-defined reasoning-associated and safety-associated directions — not as proof of disentanglement.

---

## Study Design

| Arm | Training Objective | Purpose |
|-----|--------------------|---------|
| A — Baseline | CE(answer) only | Control — all comparisons reference Arm A |
| B — Rationale | CE(answer + rationale) | Tests effect of rationale text supervision alone |
| C — Faithfulness | CE(answer) + contrastive faithfulness loss (λ=0.5) | Key arm — faithfulness-oriented training |

**3 arms × 3 seeds (42, 123, 456) = 9 checkpoints**, each evaluated on:

- Behavioural consistency / faithfulness — GSM8K test split + FaithCoT-Bench
- Adversarial robustness — AdvBench 200 prompts, fixed snapshot
- Helpfulness — MT-Bench 80 prompts
- Internal probing — Phase 1b: layer-wise probe accuracy, cosine similarity of probe directions, logit lens
- Activation patching — causal support for identified discriminative layer

---

## Key Decisions Locked

| Parameter | Value |
|-----------|-------|
| Base model | Llama-3.2-3B-Instruct (52% pilot refusal rate) |
| LoRA rank / alpha | 16 / 32 |
| Target modules | q_proj, k_proj, v_proj, o_proj |
| Dropout | 0.1 |
| Seeds | 42, 123, 456 |
| Training | Google Colab T4, 4-bit NF4 quantisation |
| Arm B rationale model | GPT-5.2-2025-12-11 (frozen, cached) |
| Arm C λ | 0.5 |
| Judge | GPT-4o-mini, binary Yes/No rubric |

---

## Evaluation Axes

| Axis | Dataset | Metric |
|------|---------|--------|
| Faithfulness | GSM8K test (~1,319 items) | Paraphrase stability, NLI consistency, intervention stability |
| Faithfulness | FaithCoT-Bench (FINE-CoT) | Instance-level unfaithfulness detection score |
| Robustness | AdvBench 200 prompts | Refusal rate, ASR |
| Helpfulness | MT-Bench 80 prompts | GPT-5.2 score 1–10 |
| Internal | 150 probe prompts | Layer-wise probe accuracy, cosine similarity across arms |
| Causal support | Activation patching | Patching success rate at discriminative layer |

---

## Novelty Positioning

**What already exists individually:** Faithfulness benchmarks (FaithCoT-Bench, FINE-CoT), safety geometry studies (RefusalGuard, Safety Geometry Collapse), mechanistic probing of refusal directions, and fine-tuning effects on safety are all active 2025–2026 research directions. Faithfulness, robustness, and geometry together are not individually novel.

**What this study adds:** To our knowledge, prior work has not jointly combined all three of the following under a matched-loss intervention design: (1) a controlled matched-arm experiment where only the loss function varies, (2) joint behavioural and representational evaluation on the same checkpoints, and (3) mechanistic evidence suggesting partial dissociation between probe-defined reasoning-associated and safety-associated directions. We are unaware of prior work combining all three elements in a controlled matched-arm setting — though we acknowledge the possibility of concurrent or unpublished work.

**Defensible contribution claim:** This is a controlled study of the interaction between explanation faithfulness and adversarial robustness. The experimental design — matched arms, pre-registered protocol, joint evaluation — is the primary contribution. Any mechanistic findings are presented as suggestive evidence consistent with partial dissociation between probe-defined directions, not as proof of semantic subspace separation.

**What this study does not claim:**
- "First" mechanistic account of any kind
- That faithfulness-oriented training necessarily improves faithfulness
- Causal proof of representational separation
- That probing or patching results identify clean semantic subspaces
- That cosine similarity between probe directions implies disentanglement
- Generalisation beyond 3B scale without explicit replication

---

## Interpretation Rules (pre-committed)

- Stability threshold: a metric is directionally stable if at least 2 of 3 seeds agree on direction
- Effect size threshold: a finding is non-trivial if Cohen's d > 0.5 AND the 95% CI excludes zero
- If neither threshold is met: report as "directionally unstable"
- All three seeds are always shown in the per-seed results table — no cherry-picking
- Probing and patching results are reported as suggestive internal evidence, not causal proof
- If Arm C does not improve faithfulness metrics: report honestly as a null intervention result — this is still a publishable finding

---

## Privacy and Security

| Asset | Status |
|-------|--------|
| Raw AdvBench prompt texts | Private — not committed |
| SHA256 hashes of 200 prompts | Committed to `eval/advbench_hashes.csv` |
| OpenAI API key | Environment variable only — never committed |
| `cache/rationale_scores/` | Gitignored — too large (~15k pairs) |
| `cache/activations/` | Gitignored — ~5.4 GB |
| `results/` raw CSVs | Gitignored until paper submitted |

---

## Repository Structure

```
├── train/              Training scripts and LoRA config
├── eval/               Evaluation scripts + committed prompt files
│   ├── private/        Local-only eval assets (gitignored)
│   ├── faithcot_eval/  FaithCoT-Bench evaluation script
│   └── patch_eval/     Activation patching script
├── scripts/            Rationale generation, scoring, pilot selection
├── results/            Per-seed result tables (added after experiments)
│   ├── arm_a/          Results for Arm A (baseline)
│   ├── arm_b/          Results for Arm B (rationale)
│   ├── arm_c/          Results for Arm C (faithfulness)
│   └── pilot/          Pilot runs and scratch outputs
├── notebooks/          Colab notebooks
├── configs/            Model and eval configuration
├── docs/               Proposal and study design
├── cache/              Gitignored — rationale scores + activations
├── analysis_rules.md   Pre-committed interpretation rules
└── future_work.md      Deferred ideas — do not implement during MTP
```

---

## Pre-Training Checklist

| File | Status |
|------|--------|
| `eval/eval_config.yaml` | ✅ Committed |
| `eval/advbench_hashes.csv` | ✅ Committed (hashes only) |
| `eval/gsm8k_tiers.json` | ✅ Committed |
| `eval/judge_prompt.txt` | ✅ Committed |
| `eval/boolq_indices.txt` | ✅ Committed |
| `eval/probe_prompts.csv` | ✅ Committed |
| `train/lora_config.yaml` | ✅ Committed |
| `proposal/proposal.md` | ✅ Committed |
| `analysis_rules.md` | ✅ Committed |
| `eval/faithcot_eval/` script | 🔵 Add before evaluation begins |

---

## Experimental Pipeline

1. Prepare 15k Alpaca subset (random_state=42), run contamination checks, log SHA256 hash
2. Generate Arm B rationales via GPT-5.2 (15k rows, cached JSONL, frozen before training)
3. Run Arm C offline scoring cache (k=4 candidates, DeBERTa NLI scoring, save pos/neg pairs)
4. Train Arm A seed 42 first to verify end-to-end pipeline, then train all 9 checkpoints
5. Run full evaluation on all checkpoints — GSM8K, FaithCoT-Bench, AdvBench, MT-Bench
6. Extract activations and run Phase 1b layer-wise probing on all 9 checkpoints
7. Run activation patching at discriminative layer if probing curve shows a clear peak
8. Run 7B replication on Llama-3.1-8B seed 42 if time permits
9. Write and submit

**Target venue:** EMNLP 2026 Findings (floor) — ACL 2026 main (ceiling).

---

## Reproducibility

All evaluation scripts, prompt templates, judge configs, and analysis rules are committed to this repository **before training begins**. No post-hoc metric selection. Seed list locked in `eval/eval_config.yaml` — no additional seeds may be added after training starts without explicit disclosure in the paper.

---

## Artifact Policy

Raw AdvBench harmful prompt texts are **not** in this repository. SHA256 hashes of all 200 evaluation prompts are committed to `eval/advbench_hashes.csv` for independent verification.

---

## Citation

```bibtex
@mastersthesis{netaji2026faithfulness,
  title   = {A Controlled Study of Explanation Faithfulness and
             Robustness in Large Language Models},
  author  = {Kancharapu Netaji},
  school  = {Indian Institute of Technology Jodhpur},
  year    = {2026}
}
```
