# Computationally Efficient Stain Agnostic Nuclei Segmentation

This repository contains the code implementation for our lab project, combining **ChannelNet** and **CellViT** architectures for instance-level nuclei segmentation in multiplexed histological images.

## Project Overview

We present a hybrid segmentation pipeline tailored for high-dimensional biomedical inputs, particularly focusing on domain-variant datasets such as **Vectra**, **Zeiss**, and **CODEX**. Our contributions include:

- A custom **Channel Adapter module** to map 32-channel inputs to 3-channel architectures
- A dual-architecture pipeline combining **ChannelNet** (for feature adaptation) and **CellViT** (for transformer-based segmentation)
- Integration of **Lovász-Hinge Loss** and **Cross-Entropy Loss** for robust training under class imbalance
- Post-processing pipeline for instance separation using connected component labeling and morphological operations

## Dataset

The model is trained and evaluated on the **CPDMI 2023 Dataset**, which includes:

- **Vectra & Zeiss**: Used for training and validation 
- **CODEX**: Held out for final testing 

Preprocessing includes resizing to 256×256, percentile intensity scaling, and channel expansion to 32

## Architecture

- **Channel Adapter**: Two 3×3 conv layers + batch norm + ReLU + 1×1 projection
- **ChannelNet**: Adapted to process 3-channel inputs
- **CellViT**: Transformer-based nucleus segmenter with 8 layers and 8 heads

Training setup:
- Optimizer: Adam (`lr=0.001`)
- Epochs: 130
- Batch size: 3 (with gradient accumulation steps = 4)
- AMP (Automatic Mixed Precision) enabled for faster training
- Loss function: Combined Lovász-Hinge + Cross-Entropy Loss

## Post-Processing

To refine segmentation outputs:
1. Apply a **lower probability threshold** (0.3) to increase recall
2. Use `remove_small_objects` and `remove_small_holes` (min size = 32)
3. Apply **connected component labeling** for instance IDs

## Results (on CODEX Test Set)

| Metric                  | Score     |
|-------------------------|-----------|
| F1 Score                | 0.7147    |
| Precision               | 0.8179    |
| Recall                  | 0.6368    |
| mAP                     | 0.7712    |
| Pixel Accuracy          | 0.6548    |
| Panoptic Quality (PQ)   | 0.0363    |
| Segmentation Quality    | 0.5897    |
| F₀.₅₁ Score             | 0.0342    |
| Fμ₁ Score               | 0.0162    |

## Future Work

- Integrate **boundary-aware losses** (e.g., StarDist, Boundary Loss)
- Use **domain adaptation** for better cross-platform generalization
- Apply **SAM** for post-processing refinement
- Benchmark against datasets like **PanNuke**, **TissueNet**
- Replace encoder with pretrained **MedSAM ViTs**
