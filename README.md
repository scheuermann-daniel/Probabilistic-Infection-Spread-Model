# Stochastic Predator–Prey Dynamics and Spatial Spread Modeling

## Overview

This project implements a **stochastic predator–prey model** using **master equations** and probability distributions rather than deterministic population trajectories. The system models interactions between prey (bunnies) and predators (wolves) on discrete population states and examines how predator presence can **spread spatially between adjacent plots over time**.

This approach captures uncertainty, extinction risk, and nonlinear effects that are not represented in classical Lotka–Volterra ODE models.

---

## Model Structure

### 1. Predator–Prey Master Equation

The joint probability distribution

P(n_prey, n_pred, t)

evolves according to a master equation incorporating:

- Prey birth with density dependence (logistic growth)
- Predator death
- Predation events
- Predator reproduction from predation

The system is discretized on a finite state space:

- n_prey ∈ [0, K)
- n_pred ∈ [0, K)

The master equation is implemented in the function:

```python
J(x, t, alpha, gamma, beta, delta)
```

and accelerated using **Numba** for performance.

---

### 2. Numerical Integration

The probability distribution is integrated forward in time using:

* `scipy.integrate.odeint`
* `odeintw` to support 2D state arrays

Each simulation produces a 3D tensor:

(time × prey states × predator states)

Multiple initial prey conditions are simulated to represent different spatial plots.

---

### 3. Marginal Distributions

Helper functions extract marginal probabilities:

* `get_prey_probability(x, t, path, t_vec)`
* `get_predator_probability(x, t, path, t_vec)`

These return the probability of observing exactly `x` prey or predators at a given time by marginalizing over the joint distribution.

---

## Spatial Spread Model

### Predator Movement Between Plots

Predator movement between neighboring plots is modeled probabilistically using:

* A **predator departure probability** that increases with predator density (logistic form)
* A **plot acceptance probability** that increases with prey availability (logarithmic form)
* A **transmission rate** defined as the product of departure and acceptance probabilities

The probability of predator spread at each time step is computed as an expectation over the predator population distribution.

---

## Multi-Plot Propagation

To model spread across multiple plots:

* Pairwise spread probabilities are computed for adjacent plots
* Temporal probability distributions are shifted and accumulated
* Resulting distributions are normalized to form valid probability density functions (PDFs)

Cumulative distribution functions (CDFs) are then computed to describe the probability that predators reach a plot by a given time.

---

## Outputs and Visualizations

The notebook produces:

* Time-evolving prey probability distributions
* Predator and prey marginal distributions
* Probability of predator spread over time for each plot
* PDFs and CDFs of spread timing
* Peak spread times highlighted on probability curves

These outputs allow analysis of invasion timing, uncertainty, and differences driven by prey availability.

---

## Dependencies

Required Python packages:

* numpy
* scipy
* matplotlib
* numba
* odeintw

---

## Running the Project

All code, analysis, and generated figures are contained in the **Jupyter notebook (`.ipynb`) included in this repository**.

---

## Applications

This framework is applicable to:

* Stochastic ecological modeling
* Predator–prey systems with demographic uncertainty
* Invasive species spread
* Disease transmission analogs
* Metapopulation and spatial dynamics modeling

---

## Notes

* Increasing the state-space size (`nb_of_states`) increases computational cost
* Probability normalization is explicitly enforced to prevent numerical drift
* The model structure is easily extensible to additional spatial plots or alternative movement rules

---

## Future Work
I would like to get a second pair of eyes to make sure the math checks out and this is truly a way to combine master equations.
