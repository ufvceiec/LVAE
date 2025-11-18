# LVAE: Learned-Correlation Variational Autoencoder for Single-Cell RNA-seq Data Simulation

## Overview

LVAE (Learned-Correlation Variational Autoencoder) is a prototype implementation for simulating single-cell RNA-seq data. It is designed to preserve cell-type–specific structure by learning the full latent covariance matrix via a Cholesky factorization.

![LVAE architecture](assets/lvae_architecture.png)

This project has been refactored into a modular and configurable architecture to facilitate experimentation, reproducibility, and maintainability.

## How It Works

1.  **Preprocessing**: The pipeline starts by preprocessing raw scRNA-seq data using Scanpy, including quality control, normalization, and selection of highly variable genes.
2.  **Per-Cell-Type Training**: A separate LVAE model is trained for each cell type. This allows the model to learn the specific nuances of each cell population.
3.  **Latent Representation**: Each model learns a per-cell latent representation consisting of:
    -   `μ`: The latent mean vector.
    -   `L`: The lower-triangular Cholesky factor of the covariance matrix (`Σ = L * L^T`).
4.  **Custom Loss Function**: The training uses a custom VAE loss that combines a reconstruction error (MSE) with a KL divergence term adapted for a full covariance matrix.
5.  **Synthetic Data Generation**: After training, various latent sampling strategies are used to generate new latent vectors (`z`), which are then decoded to produce synthetic gene expression profiles.

## Key Features

-   **Full Covariance Modeling**: Improves latent space expressivity over standard diagonal VAEs.
-   **Modular Architecture**: Code is organized into logical modules (`config`, `data`, `core`, `analysis`).
-   **Centralized Configuration**: All experiment parameters are managed through YAML files.
-   **Per-Cell-Type Training**: Preserves intra-type heterogeneity.
-   **Flexible Latent Sampling**: Supports multiple strategies for generating synthetic data.
-   **AnnData Integration**: Uses `AnnData` for data manipulation and saves outputs as `.h5ad` files.

## Latent Sampling Strategies

The model supports four different strategies to generate synthetic data from the learned latent space:

1.  **Direct Sampling**: Generates one synthetic cell from each real cell's learned Gaussian distribution `N(μ, LL^T)`.
2.  **Per-Cell Sampling**: Generates `k` synthetic samples from each cell's distribution to increase diversity.
3.  **Global-Mean Sampling**: Samples from a single Gaussian representing the population's average distribution.
4.  **GMM Sampling**: Fits a Gaussian Mixture Model where each cell's learned distribution is a component, then samples from this mixture.

## Project Structure

```
.
├── README.md
├── RELEASE_NOTES.md
├── requirements.txt
├── configs/
│   ├── local.yaml
│   └── server.yaml
├── data/
│   └── adata_raw_reanotado_peng_octubre.h5ad
├── output/
│   ├── adatas/
│   ├── models/
│   └── plots/
└── src/
    ├── __init__.py
    ├── main.py
    ├── analysis/
    ├── core/
    ├── data/
    └── model/
```

## Installation

1.  Clone the repository:

    ```bash
    git clone https://github.com/ufvceiec/LVAE.git
    cd LVAE
    ```

2.  Install the required dependencies:
    ```bash
    pip install -r requirements.txt
    ```

## Usage

The main training pipeline is orchestrated by `src/main.py`. Run it from the project root, specifying a configuration file using the `--config` argument.

-   **Local Development (Debug Mode)**:

    ```bash
    python src/main.py --config configs/local.yaml
    ```

-   **Full Training Run**:
    ```bash
    python src/main.py --config configs/server.yaml
    ```

If no config file is specified, `configs/local.yaml` is used by default.

## Configuration

Experiment parameters are defined in YAML files within the `configs/` directory. This allows you to modify hyperparameters, paths, and model settings without changing the code.

## Artifacts Produced

All outputs are saved in the `output/` directory:

-   **Model Checkpoints**: `output/models/{cell_type}_best_model.pth`
-   **Synthetic Datasets**: `output/adatas/{cell_type}_syn.h5ad` or `output/synthetic/{category}_synthetic.csv`
-   **Figures and Plots**: `output/plots/`

## Contributing & Contact

For feedback, bug reports, or feature requests, please open an issue in the repository. Pull requests are welcome.
