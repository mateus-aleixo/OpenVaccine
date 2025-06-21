# OpenVaccine: COVID-19 mRNA Vaccine Degradation Prediction

This repository chronicles my efforts in the Stanford COVID-19 Vaccine Degradation Prediction Kaggle competition. The objective is to accurately predict the degradation rates of RNA molecules, leveraging their sequence, secondary structure, and chemical probing data.

My approach primarily involves a Graph Neural Network (GNN) architecture, enhanced with a denoising autoencoder for pre-training, followed by a regression head for final predictions.

## Iteration Results

The table below summarizes the performance of each model iteration, highlighting the key changes introduced in each version.

| Version | Private Score | Public Score | Rank | Key Differences (from previous version)                                                                                                                                             |
| ------- | ------------- | ------------ | ---- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `v1`    | 0.36438       | 0.25232      | 841  | Baseline GNN model with autoencoder pre-training and standard features.                                                                                                             |
| `v2`    | 0.36280       | 0.25380      | 816  | Increased model capacity (more units in `get_base`), higher dropout rates, implemented `ReduceLROnPlateau` and `EarlyStopping` callbacks.                                           |
| `v3`    | 0.36824       | 0.25537      | 899  | Deeper `get_base` network (more iterations), more aggressive dropout (`SpatialDropout1D` to 0.5, final dropout to 0.6), introduced `bpp_paired_count` node feature (threshold 0.9). |
| `v4`    | 0.36600       | 0.25457      | 858  | `v2` model with `bpp_paired_count` node feature (0.8 threshold).                                                                                                                    |
| `v5`    | 0.36668       | 0.25228      | 865  | `v2` model with added Bidirectional GRU layer and `LayerNormalization` after base model.                                                                                            |

## Latest Version (`latest`)

The `latest` notebook represents a completed iteration of this project. It builds upon the successful `v2` model architecture and training setup. This notebook's primary focus was comprehensive Exploratory Data Analysis (EDA), delving into various aspects such as sequence length distributions, nucleotide frequencies, analysis of RNA structure symbols, and predicted loop types.
