# SecureAI Hackath🔐n Day 2

# Skew-Aware Aggregation for Federated Intrusion Detection

![Cover: skew-aware aggregation versus naive FedAvg on skewed clients](figures/cover_image.png)

CAIRLab hackathon, Day 2, **Intermediate track** (non-IID aggregation). Five simulated banks train a shared NSL-KDD intrusion detector without pooling data.

## Results (F1 on the organizers' 15,000-row held-out set, mean of 3 seeds)

| Method | Held-out F1 |
|---|---|
| Naive FedAvg on skewed clients (before) | 0.9843 |
| Skew-aware aggregation (after) | 0.9880 |
| IID reference (upper bound) | 0.9907 |

Exported model: held-out F1 0.9885 (seed 0). Full analysis, ablation and limitations are in [`WRITEUP.md`](WRITEUP.md).

### F1 over communication rounds

Mean of 3 seeds, band = 1 standard deviation.

![Intermediate track: held-out F1 over rounds for IID reference, naive FedAvg and skew-aware aggregation](figures/intermediate_f1_over_rounds.png)

### Confusion matrix of the exported model (held-out set)

<img src="figures/confusion_matrix.png" alt="Held-out confusion matrix of the exported model" width="380">

## Contents

| File | Purpose |
|---|---|
| `federated_ids_intermediate.ipynb` | Final notebook with all outputs visible (runs top to bottom, no errors) |
| `model_scripted.pt`, `submission.json` | Exported TorchScript model and metrics file for organizer verification |
| `predictions.csv` | Predictions in `sample_submission.csv` format (`Id`, `Expected`), same row order as `test_public.csv` |
| `results.json` | Every number reported in the write-up |
| `figures/` | Cover image, F1-over-rounds chart, confusion matrix |
| `WRITEUP.md`, `VIDEO_SCRIPT.md` | Kaggle writeup text and a 3-minute video script |

## Reproduce

```
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
jupyter nbconvert --to notebook --execute --inplace federated_ids_intermediate.ipynb
```

Needs internet access to download NSL-KDD from GitHub. About 6 minutes on one CPU thread; no GPU. The data split uses the starter's fixed seed (42) and federated runs use seeds 0, 1 and 2. Results can differ in the last digit across hardware and library versions.

## Use the exported model

```python
import torch
model = torch.jit.load("model_scripted.pt").eval()
# x: float32 tensor of shape (n, 41), standardized exactly as in Section 1 of the notebook
prediction = (torch.sigmoid(model(x)) > 0.5).int()   # 1 = attack, 0 = normal
```

## Method

`SkewAwareAgg`: a logit-adjusted local loss (each client adds the log-odds of its own label prior during local training), square-root client weights, and server momentum of 0.5.
