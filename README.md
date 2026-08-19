# OpenVaccine: mRNA Degradation Prediction

Predicting per-base degradation rates of mRNA molecules for the Stanford
[OpenVaccine](https://www.kaggle.com/c/stanford-covid-vaccine) Kaggle competition.
mRNA vaccines are limited in practice by how quickly the molecule degrades, and
degradation is not uniform along the strand, so the task is to predict a set of
reactivity and degradation values **at every base** from the sequence, its predicted
secondary structure, and its predicted loop type.

## Approach

The model treats each RNA molecule as a **graph** rather than a flat sequence.
Nucleotides are nodes; edges come from both the backbone and the base-pairing
probability matrix, which is what makes secondary structure visible to the network.

- **Graph neural network** over that structure, producing a per-base embedding.
- **Denoising autoencoder pre-training.** The competition's test set is much larger
  than its labelled training set, so the base network is pre-trained to reconstruct
  corrupted inputs across all available sequences, labelled or not, before the
  supervised head is attached. This is the main way the unlabelled data is used.
- **Regression head** on top of the pre-trained base, trained on the labelled
  positions.
- **5-fold cross-validation.** Each fold's weights, out-of-fold predictions and
  submission are archived, so every reported number can be traced back to the run
  that produced it.

Scoring is column-wise mean RMSE, so lower is better.

## Iterations

Each version is a self-contained notebook under `versions/`, with its weights,
out-of-fold predictions and submission alongside it.

| Version | Private | Public | Rank | Change from previous version |
|---|---|---|---|---|
| `v1` | 0.36438 | 0.25232 | 841 | Baseline: GNN with autoencoder pre-training and standard features |
| `v2` | **0.36280** | 0.25380 | **816** | Wider `get_base`, higher dropout, `ReduceLROnPlateau` and `EarlyStopping` |
| `v3` | 0.36824 | 0.25537 | 899 | Deeper `get_base`, aggressive dropout (`SpatialDropout1D` 0.5, final 0.6), `bpp_paired_count` node feature at threshold 0.9 |
| `v4` | 0.36600 | 0.25457 | 858 | `v2` plus the `bpp_paired_count` node feature at threshold 0.8 |
| `v5` | 0.36668 | **0.25228** | 865 | `v2` plus a bidirectional GRU layer and `LayerNormalization` after the base model |

**The honest reading of that table is that none of the four variations beat the
baseline.** The whole private-score range is 0.363 to 0.368, roughly one percent,
and the ranking of the variants flips between the public and private splits: `v5` has
the best public score and a middling private one. Extra depth (`v3`) actively hurt.
Capacity and regularisation tuning (`v2`) bought a small, and possibly incidental,
improvement.

That is a useful negative result. On this dataset the pre-trained graph
representation carries essentially all of the signal the architecture is going to
extract, and further head-level architecture search does not move it.

## Exploratory analysis

The `latest` notebook rebuilds the `v2` setup and adds the exploratory analysis:
sequence-length distributions, nucleotide frequencies, the distribution of secondary
structure symbols and predicted loop types, mean reactivity by position, and
reactivity broken down by loop type. Per-fold loss curves, error distributions and
positional error plots are in `versions/latest/images/`.

## Layout

```
versions/
  v1 .. v5/          notebook, weights per fold, oof.pkl, results.txt, submission.csv
  latest/            v2 architecture plus full EDA, with figures under images/
report.pdf           write-up
```

## License

MIT.
