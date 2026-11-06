# FIT1061 H2 — Food Rescue Decision Planner

A comprehensive artificial intelligence project for FIT1061 Task 2. The notebook models a food-rescue coordinator navigating a disaster-zone grid after a disruption.

## Problem

One vehicle must visit four pickup points before their deadlines. Some roads may be blocked, but their states are hidden initially. At decision points, the coordinator can move or ask a human advisor about a road. Advice may be wrong, and questions are limited and may introduce delay.

## What is implemented

- Belief-state reasoning over hidden road worlds
- A* route planning with observed and probabilistically blocked roads
- Pickup-order evaluation and deadline-aware travel
- Never-ask, always-ask, finite-horizon lookahead, and omniscient comparison policies
- Noisy-advisor updates with bounded consultation budgets
- Reproducible seeded experiments and holdout evaluation
- Correctness checks for reproducibility, policy properties, A*, witness cases, and boundary cases
- Bootstrap uncertainty intervals and exploratory delay, calibration-mismatch, and pickup-fairness analyses
- Result charts and a decision-trace map

## Files

- `H2_comprehensive_project_implementation.ipynb` — implementation, experiments, visualisations, and project report
- `figures/` — generated result charts
- `results/` — saved experiment outputs

## Running

Open the notebook in Jupyter or Google Colab and run the setup and experiment sections. The notebook uses Python, NumPy, Matplotlib, and standard-library modules.

The full holdout experiment is controlled by the `RUN_FULL` flag and can take several minutes to run. Saved JSON results are included so the analysis and figures can be reproduced without rerunning every experiment.

## Context

Student project for FIT1061 Introduction to Artificial Intelligence. Shared as a portfolio and learning project.
