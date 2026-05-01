# MMML-project: The "Teacher-Student" Gap in Foundation Vision Models

---

## Overview

This project investigates the mathematical gap between teacher and student models in **knowledge distillation** applied to Foundation Vision Models (specifically DINOv2). The central question: is the well-known attention-map blurriness of distilled models a consequence of the **KL divergence direction** in the loss function, or merely a capacity limitation?

We train two student models (identical ViT-S/14 architectures) under **Forward-KL** and **Reverse-KL** objectives against a frozen DINOv2-ViT-S/14 teacher, isolating divergence direction as the sole variable.

---

## Key Findings

| Hypothesis | Prediction | Result |
|---|---|---|
| **H1** (Entropy Gap) | Forward-KL - diffuse maps, Reverse-KL - sharp maps | Confirmed (ΔH̄ = +0.1643 vs −0.1182) |
| **H2** (Geometric Fidelity) | Sharpness ≠ higher cosine similarity to teacher | Confirmed (FKL: 0.9051 vs RKL: 0.8734) |
| **H3** (Mode Collapse) | Reverse-KL - higher attention collapse rate | Confirmed (0.0531 vs 0.0082) |
| **H4** (Causal Attribution) | Entropy gap is caused by loss geometry, not capacity | Confirmed (identical architectures) |

---

## Methods

- **Forward KL distillation:** `KL(T‖S)` — mean-seeking, produces diffuse attention maps
- **Reverse KL distillation:** `KL(S‖T)` — mode-seeking, produces sharp but geometry-losing maps
- **Attention entropy** `H̄` as sharpness metric
- **Cosine similarity** between teacher/student attention maps as a geometric fidelity metric
- **5-NN accuracy** for downstream embedding quality evaluation
- **Toy 2D Gaussian Mixture** experiment to validate KL asymmetry before the vision experiment

---

## Experimental Setup

| Parameter | Value |
|---|---|
| Teacher | DINOv2-ViT-S/14 (frozen) |
| Student architectures | ViT-S/14 × 2 (identical to teacher) |
| Dataset | STL-10 (N=2,000 balanced subset) |
| Training steps | 3,500 |
| Optimizer | AdamW (lr=3×10⁻⁴) |
| Scheduler | Cosine annealing |
| Batch size | 16 |
| Temperature τ | 3.0 |
| Random seed | 42 |

Both students are initialised from a deep copy of the pretrained teacher weights, ensuring the divergence direction — not convergence speed — is the variable under study.

---

## Results Summary

| Model | H̄ | ΔH̄ | Cosine Sim. | Collapse Rate | 5-NN Acc. |
|---|---|---|---|---|---|
| Teacher (DINOv2-ViT-S/14) | H̄_T | — | 0.9893 | — | 0.8546 |
| Student (Forward KL) | H̄_T + 0.1643 | +0.1643 | 0.9051 | 0.0082 | 0.6942 |
| Student (Reverse KL) | H̄_T − 0.1182 | −0.1182 | 0.8734 | 0.0531 | 0.6687 |

More details in `project.pdf`
