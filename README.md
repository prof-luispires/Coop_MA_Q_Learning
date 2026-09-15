# CF-MAQL: Cooperative Flow Multi-Agent Contextual Q-Learning for Robust Intrusion Detection in Heterogeneous IoT Networks

This repository contains the implementation and experimental outputs used to evaluate **CF-MAQL**, a cooperative multi-agent Q-learning framework for intrusion detection in heterogeneous IoT network flows.

CF-MAQL decomposes each network flow into four functional views—**Flow, Temporal, Protocol, and Payload**—processed by specialized linear Q-learning agents. A learned interaction-aware coordinator combines four bounded local Q-values, six pairwise interaction terms, and four confidence-derived weights for each candidate action.

## Repository structure

```text
CF_MAQL_v1_3/                 Core implementation used in the experiments
results/
  E1_E2/                      IID/cooperative-learning outputs and threshold calibration
  E1_ML_baselines/            Supervised baseline benchmark outputs
  E3_LOAFO/                   Leave-one-attack-family-out results
  E5_ablation/                Structural ablation outputs
  consolidated/               E1–E5 master results and final statistics
figures/                      Publication figures generated from experimental CSV files
scripts/                      Reproducible plotting scripts
```

The internal implementation files retain the development identifier `v1.3` to preserve exact experimental provenance. In the manuscript, the final method is referred to simply as **CF-MAQL**.

## Experimental protocol

- Dataset: RT-IoT2022.
- Five seeds: 11, 22, 33, 44, and 55.
- Outer split: 70% training, 15% validation, 15% test, stratified by `Attack_type`.
- Within the outer training partition: 60% is used for local-agent learning and a disjoint 10% for coordinator learning.
- Preprocessing is fitted only on the outer training partition and then frozen.
- Four actions: `ALLOW`, `MONITOR`, `INSPECT`, and `BLOCK`.
- Contextual one-step Q-learning with `gamma = 0`.
- Final validation-calibrated attack-score threshold: `0.5375` for the standard E1/E2 setting.

The five evaluation scenarios cover IID performance, cooperative coordination, leave-one-attack-family-out generalization, partial-view robustness, and structural ablation.

## Running the CF-MAQL benchmark

Install the dependencies:

```bash
pip install -r CF_MAQL_v1_3/requirements.txt
```

Then run:

```bash
python -m CF_MAQL_v1_3.benchmark_v13 --csv /path/to/RT_IOT2022.csv --out /path/to/results
```

The RT-IoT2022 dataset is **not redistributed** in this repository. Obtain it from the UCI Machine Learning Repository and provide the local CSV path to the benchmark command.

## Reproducing the results figures

From the repository root:

```bash
python scripts/generate_results_figures.py
```

The script regenerates the main Results figures from the stored CSV outputs. No values are hard-coded from the manuscript text.

## Main experimental observations

Under IID evaluation, Random Forest and XGBoost achieve higher MCC than CF-MAQL. The advantage of the cooperative architecture becomes more apparent under distributional and information changes: CF-MAQL obtains the highest macro-average unseen-family recall in the LOAFO evaluation and degrades more gradually under severe functional-view loss. Ablation results identify the learned coordinator and the Temporal agent as the most influential components of the evaluated architecture.

These results should not be interpreted as evidence of online concept-drift adaptation or universal superiority over supervised classifiers.

## Dataset reference

Sharmila, B.S.; Nagapadma, R. **RT-IoT2022**. UCI Machine Learning Repository, 2023. DOI: 10.24432/C5P338.

## Reproducibility notes

The repository preserves the internal source-code version names used when the experiments were executed. Generated caches, compiled Python/Numba files, local environments, and the RT-IoT2022 dataset are excluded from version control.

## License

No software license is assigned in this package. Add the license selected by the repository owner before public release if redistribution or reuse permissions are to be granted.
