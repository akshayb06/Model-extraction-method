# Model-extraction-method
securing the models duplication

Web tool that watermarks AI models to detect theft via extraction attacks — upload a model, embed a secret trigger, and test suspect models for stolen-copy evidence. Runs entirely client-side.

# Model Extraction Attacks & Watermark Defense in ML APIs

**Theme:** Artificial Intelligence & Cybersecurity
**Mentor:** Dr. Neetu S, Assistant Professor, Department of CSE
**Phase:** EL Phase 1

---

## Overview

AI models are increasingly deployed as paid, black-box APIs: a user sends an input and receives only a prediction, while the owner keeps the architecture, weights and training data hidden. That secrecy protects the cost of data collection, compute and engineering behind the model.

A **Model Extraction Attack** exploits exactly this API-only access. An attacker queries the API with a large number of inputs, collects the resulting (input, output) pairs, and trains a new *substitute* model that imitates the original, without ever seeing its code, weights or training data. It is the same mechanism as knowledge distillation, applied without the owner's permission.

This project builds and studies both sides of the problem:

1. **The attack:** extracting a victim classifier through its prediction API.
2. **The defense:** a **watermark** secretly embedded in the model so that a stolen copy can later be identified, along with a measurement of how reliably it does so.

## Problem Statement

Once a model is exposed through a public API, an attacker can rebuild an equivalent model just by querying it. Rate-limiting raises the cost of extraction but cannot stop a patient attacker, especially as query-efficient (active-learning) extraction keeps improving. Without a reliable way to prove theft after the fact, companies cannot act even when they suspect it.

## Objectives

**Main objective:** implement a model-extraction attack pipeline and a watermark-based theft-detection defense, and precisely measure how reliably the defense identifies a stolen copy.

**Specific objectives:**

- Build a victim classifier exposed as a black-box API.
- Implement random and active-learning extraction attacks.
- Measure substitute-model accuracy and fidelity against query budget.
- Implement a watermarking defense and measure its detection success rate on a stolen copy.

## How It Works

The project is delivered as two linked components:

| Component | Purpose | Stack |
|---|---|---|
| **Research pipeline** | Full attack and defense study: victim model, extraction attacks, watermark evaluation | Python, PyTorch, Flask/FastAPI |
| **Web tool** | Self-contained, in-browser tool: upload a trained model, embed a watermark, test another model for theft | Plain HTML/CSS/JavaScript, hand-written neural-network engine with Adam optimiser, ONNX Runtime Web |

### End-to-end pipeline

```
1. Train Victim Model (CNN)
        |
        v
2. Deploy as Black-Box API
        |
        v
3. Attacker Sends Bulk Queries
        |
        v
4. Train Substitute Model on Responses
        |
        v
5. Query Secret Watermark Trigger
        |
        v
6. Final Verdict: Stolen? Yes / No
```

### Watermarking approach

The victim model is trained to give one specific, attacker-unknown answer on a small hidden **trigger set**. Because extraction attacks imitate the victim's behaviour, this planted behaviour often transfers into a stolen copy, giving the owner a way to demonstrate theft.

The web tool embeds the watermark by fine-tuning with **anchor / self-distillation data**. This keeps the secret trigger tied to the model's real behaviour and avoids catastrophic forgetting. The idea follows the entangled-watermark approach of Jia et al. (2021).

## Data and Evaluation

- **Datasets:** public benchmark image datasets (CIFAR-10 / GTSRB) for the victim model; synthetic out-of-distribution query sets for the extraction attack.
- **Metrics:**
  - Substitute accuracy and fidelity curves versus query budget
  - Watermark Success Rate, compared between the original victim and a stolen copy
  - Anchor-set accuracy retention after watermark embedding

## Tools and Resources

Python, PyTorch, Flask/FastAPI, HTML/CSS/JavaScript, ONNX Runtime Web, Google Colab / local GPU.

## Expected Outcomes

- A working extraction attack that reaches high substitute-model fidelity within a defined query budget.
- A working web tool where a user can upload a model, embed a watermark via fine-tuning with anchor/self-distillation data, and test a second model for theft with live match statistics.
- A quantified, independently reproduced comparison of extraction-attack efficiency and watermark-detection reliability.

## Current Limitations

The web tool currently works with a simplified JSON model format rather than arbitrary production model files.

## Future Work

- Extend the detection tab to accept real-world `.onnx` model files via ONNX Runtime Web.
- Build a backend service (Flask/PyTorch on a GPU) so the embedding step can support production model formats such as PyTorch and `.h5`.

## Getting Started

> Fill in the commands below to match your repository layout.

```bash
# Clone the repository
git clone <your-repo-url>
cd <your-repo-folder>

# Research pipeline
pip install -r requirements.txt
python <your-pipeline-entry-script>.py

# Web tool
# Open the HTML file in a browser (no build step required)
```

## References

1. F. Tramèr, F. Zhang, A. Juels, M. K. Reiter, T. Ristenpart. *Stealing Machine Learning Models via Prediction APIs.* USENIX Security, 2016.
2. Tang et al. Model watermarking against extraction attacks. ACM CIKM, 2023.
3. H. Jia et al. *Entangled Watermarks as a Defense against Model Extraction.* USENIX Security, 2021.
4. *A Systematic Survey of Model Extraction Attacks and Defenses.* arXiv:2508.15031, 2025.

## Acknowledgements

Guided by **Dr. Neetu S**, Department of CSE.
