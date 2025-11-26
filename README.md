# Thompson Sampling for classification

This repository contains an implementation of the Thompson Sampling (TS) algorithm, applied to the problem of combinatorial molecular design (e.g., screening virtual chemical libraries based on building blocks). The method functions as an Active Learning/Reinforcement Learning strategy, prioritizing the most promising compounds for virtual screening to rapidly discover novel hit molecules.

## Local development

### Prerequisites

- [Conda](https://docs.conda.io/projects/conda/en/latest/user-guide/install/download.html)

### Create Environment

The following commands will setup an environment where you can run and test the application locally:

```bash
git clone git@github.com:jonswain/TS-for-classification.git
cd TS-for-classification
conda env create -f environment.ml
conda activate TS-for-classification
code .
```

## Core Functionality

The experiment simulates a closed-loop drug discovery process:

1. Selection (Exploration/Exploitation): Thompson Sampling draws samples from the Beta distribution associated with each synthon (building block) to estimate its expected activity. It then selects the combination of synthons that maximizes the sampled expected activity.
2. Synthesis: The selected synthons are virtually reacted to form a novel Compound molecule using RDKit chemical reaction functionality. Checks are included to handle synthesis failures (e.g., RDKit errors or impossible reactions).
3. Scoring (Oracle): The new compound is scored using a pre-trained Machine Learning Classifier (the oracle), which predicts the probability of activity ($P_{\text{pred}} \in [0, 1]$).
4. Update: The $P_{\text{pred}}$ is used as the "soft reward" to update the Beta distributions ($\alpha$ and $\beta$ parameters) of the three contributing synthons, refining their posterior beliefs for the next sampling round.

## Key Performance Features

* Batch Scoring: Thompson Sampling is performed in batches to maximize the efficiency of the ML prediction step, as models are faster when scoring multiple compounds simultaneously.
* Pre-calculated Fingerprints (FP): Morgan fingerprints are calculated only once upon successful compound synthesis and are then cached for all future scoring, eliminating redundant molecular calculation overhead.
* Robustness: Synthesis and scoring failures are handled by setting the score and molecular representation to None, ensuring invalid compounds do not bias the synthon distribution updates.
* Bayesian Prior: Synthon distributions are initialized using a $\text{Beta}(1, 1)$ uniform prior, which is updated based on initial random screening results, leading to a sound starting point for the optimization.

## Data

* The SMILES data was borrowed from [Thompson Sampling by Pat Walters](https://github.com/PatWalters/TS).
* The hERG activity data comes from [Cai et al](https://pubs.acs.org/doi/10.1021/acs.jcim.8b00769).