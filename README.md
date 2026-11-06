# FIT1061 H2 — Food Rescue Decision Planner

This project studies a food-rescue coordinator working after a disruption. A single vehicle starts at a depot on a 16×16 disaster-zone grid and must visit four pickup points before their deadlines. Each pickup has a value, so arriving late does not merely take longer—it can make that pickup worthless.

The coordinator does not initially know which roads are blocked. A hidden world contains six possible blocked roads, giving 64 possible road configurations. The coordinator learns a road’s state only when the vehicle observes it while travelling. At a decision point, the coordinator can either continue towards a pickup or ask a human advisor about one road. The advisor is usually correct but can be wrong, every question consumes part of a limited budget, and consultation can introduce delay.

The central question is:

> When information is uncertain and questions are limited, is it better to ask now, move first and learn from the route, or save the question for a later decision?

## What the agent can and cannot know

The policy never receives the true hidden road world. Its state contains the current position, collected pickups, remaining question budget, observed road evidence, and the current belief over possible worlds. Beliefs are updated from two evidence sources:

- **Driving observations:** a road encountered on the route reveals whether it is blocked.
- **Advisor answers:** the answer updates the probability of each hidden world according to the assumed advisor accuracy.

The true world is used only at the moment a road is observed or an advisor answer is generated. This keeps the experiment honest: the planner cannot secretly use information that would be unavailable to a real coordinator.

## Implementation

### Belief-state world model

The notebook enumerates the 64 hidden road configurations and stores evidence in a canonical form, so equivalent information produces the same cached state. This makes it possible to reason about uncertainty explicitly rather than pretending that the map is fully known.

### Route planning

A* searches the grid using the current evidence and a route threshold derived from the belief. Confirmed blocked roads are avoided. Unseen roads can be approached when their posterior blockage probability is low enough, allowing the vehicle to trade route efficiency against the chance of learning new information.

The pickup-order evaluator scores all 65 ordered subsets, but the live policy commits only to its next pickup and replans after each arrival. This avoids treating the whole tour as fixed when new evidence can change the best route.

### Policies compared

The experiment evaluates five decision styles:

- **Never-ask:** never consults the advisor and replans after each pickup.
- **Always-ask:** follows a simple baseline that asks about the first unknown road on the current route.
- **h=1:** one-step lookahead; compares asking and acting at the current decision.
- **h=2:** finite-horizon lookahead; considers the value of preserving a question for a later decision.
- **Omniscient:** knows every hidden road from the start and acts as an upper-bound diagnostic, not as a realistic policy.

## Evaluation and results

The primary holdout uses seeds 200–249, separate from the ten calibration seeds 100–109. It uses blockage probability 0.5, advisor accuracy 0.8, no consultation delay, and a question budget of two. Results are saved after each completed scene and analysed with paired bootstrap intervals.

Mean rescued value across the 50 holdout scenes:

| Policy | Mean value |
|---|---:|
| Never-ask | 37.61 |
| Always-ask | 38.07 |
| h=1 | 39.61 |
| h=2 | 39.69 |
| Omniscient upper bound | 42.37 |

The h=2 policy improves on h=1 by about **0.08 value units** on average, with a paired 95% bootstrap interval of approximately **[0.03, 0.14]**. The gain is intentionally small: the experiment shows that deeper lookahead has measurable value, but it does not magically remove the uncertainty created by hidden roads and imperfect advice. In three of the 50 scenes, h=2 moves to a pickup before asking, using the route itself to obtain information for free.

## Secondary analyses

The notebook also tests how the result changes when the assumptions change:

- **Consultation delay:** delays of 0, 1, 2, and 4 steps test whether asking later becomes more useful when questions cost time.
- **Accuracy mismatch:** the agent plans as if the advisor is 80% accurate while the real advisor is only 60% accurate, measuring the cost of over-trusting advice.
- **Pickup fairness:** the two remote pickups are much more likely to be late than the central pickups under the never-ask policy. The analysis separates structural difficulty from the improvement available when the roads are known.
- **Correctness checks:** reproducibility against saved calibration results, policy-property checks, belief normalisation, A* versus BFS shortest paths, a positive h=2 witness, and boundary cases such as blocked roads and contradictory evidence.

## Visualisations

- `figures/figure1_strategy_values.png` — value rescued above the never-ask baseline, with bootstrap intervals
- `figures/figure2_delay_effects.png` — how consultation delay changes h=2’s advantage and asking behaviour
- `figures/figure3_seed201_trace.png` — a decision trace showing h=2 moving first, observing a road, and changing its next question

## Running the notebook

Open `H2_comprehensive_project_implementation.ipynb` in Jupyter or Google Colab. The notebook contains the scene generator, state model, belief updates, A* planner, policies, correctness checks, experiments, visualisations, and the approximately 1500-word project report.

The full holdout run is controlled by `RUN_FULL`. It can take several minutes, so the saved JSON files in `results/` are included for inspection and for reproducing the figures without rerunning every scene.

## Context

Student project for FIT1061 Introduction to Artificial Intelligence. The notebook is shared as a portfolio and learning project, with a focus on honest evaluation, reproducibility, uncertainty, and the difference between a policy that is theoretically informed and one that can actually act with limited information.
