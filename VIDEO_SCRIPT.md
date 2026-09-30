# Video script, Intermediate track (target 2:45)

**0:00 - 0:20 Problem.** Five banks want to catch the same intrusions but cannot pool traffic data, so they use federated learning. The catch: each bank sees very different traffic. Show the cover image.

**0:20 - 0:55 Why FedAvg struggles.** Show the client table: client 4 is 4% attack, client 1 is 86% attack, client 2 holds nearly half the data. Naive FedAvg weights by data size, so every update is pulled toward its own class balance and the biggest client dominates.

**0:55 - 1:50 Our method.** Three parts. First, a logit-adjusted local loss: each client adds the log-odds of its own label prior during training, so the network learns class evidence instead of the local balance. Second, square-root size weights so no client dominates. Third, server momentum to smooth the round-to-round oscillation. Show the F1-over-rounds chart.

**1:50 - 2:20 Results.** Held-out F1 rises from 0.9843 to 0.9880 across three seeds; the error falls 24% and about 59% of the gap to the IID reference is closed. Show the ablation table and be candid: the logit adjustment alone did nothing, and square-root weights plus momentum did most of the work.

**2:20 - 2:45 Verification and limits.** The exported model was reloaded from disk and re-scored at 0.9885 F1. The gain is modest and comes from five simulated clients on CPU. Everything reproduces from the notebook. Repo link, thanks.
