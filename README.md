# Smart Production Planner: Optimization and Sensitivity Analysis

A small production-planning optimization project that allocates limited machine time and raw materials across multiple products to maximize net profit while accounting for fixed setup costs, minimum batch sizes, and production limits.

## Overview

Manufacturing decisions involve trade-offs between profitability, resource availability, and setup costs. Producing every product is not necessarily optimal: each product consumes resources, may incur a fixed setup cost, and must satisfy its production constraints.

This project formulates a production-planning problem using continuous production quantities and binary setup decisions. It explores how the optimal production plan changes when machine capacity and material availability vary.

## Problem formulation

### Decision variables

For each product \(i\):

- \(x_i\): quantity produced.
- \(y_i \in \{0,1\}\): whether the product is selected for production.

### Objective function

Maximize net profit:

\[
\max \sum_{i=1}^{n} p_i x_i
-\sum_{i=1}^{n} f_i y_i
\]

where \(p_i\) is the unit profit and \(f_i\) is the fixed setup cost.

### Constraints

**Machine capacity**

\[
\sum_{i=1}^{n} a_i x_i \leq M
\]

**Material availability**

\[
\sum_{i=1}^{n} b_i x_i \leq R
\]

**Production limits and setup logic**

\[
m_i y_i \leq x_i \leq u_i y_i
\]

\[
x_i \geq 0,\qquad y_i\in\{0,1\}
\]

Here, \(a_i\) and \(b_i\) denote machine hours and material consumption per unit; \(M\) and \(R\) are available resources; and \(m_i\) and \(u_i\) are the minimum batch size and maximum production quantity.

The setup constraints ensure that a product cannot be manufactured unless its setup is selected, and that selected products meet their minimum batch sizes.

## Implementation

The project was developed in Python in a JupyterLite notebook.

- **Synthetic data generation:** Defined product-level profits, setup costs, resource requirements, and production bounds.
- **Optimization:** Enumerated binary setup combinations and solved the resulting continuous linear subproblems using LP vertex enumeration.
- **Sensitivity analysis:** Varied machine capacity and material availability independently to examine changes in optimal profit and production decisions.
- **Model validation:** Checked resource feasibility, production bounds, binary setup decisions, objective recomputation, and boundary cases.
- **Visualization:** Plotted profit against resource availability and inspected resource utilization.

### Implementation note

The solver is a custom educational implementation intended for a small problem with three products. It is not a general-purpose MILP solver. The continuous subproblems are solved by enumerating candidate LP vertices, which becomes computationally expensive as the problem grows. The implementation does not use Pyomo, HiGHS, Gurobi, or CPLEX.

## Results

For the baseline scenario with 240 machine hours and 360 material units, the model produced the following result:

| Metric | Result |
|---|---:|
| Optimal net profit | 4,830 |
| Machine utilization | 98.33% |
| Material utilization | 100% |
| Product A quantity | 100 |
| Product B quantity | 24 |
| Product C quantity | 0 |

The profit values are expressed in synthetic monetary units.

### Sensitivity analysis

**Machine capacity:** Increasing available machine time from 160 to 240 hours increased the optimal net profit from approximately 3,643.33 to 4,830. Increasing capacity beyond 240 hours did not improve profit in the tested scenarios.

**Material availability:** Increasing material availability from 240 to 420 units raised the optimal net profit from 3,270 to 5,250. Additional material beyond 420 units did not improve profit in the tested scenarios.

The experiments show diminishing incremental returns to resource expansion and demonstrate why production planning should consider multiple constraints together.

## Validation

The notebook includes checks for:

- Nonnegative production quantities.
- Binary setup decisions.
- Machine and material capacity limits.
- Minimum batch and maximum production constraints.
- Independent recomputation of net profit.
- Zero-capacity and insufficient-capacity scenarios.
- Rejection of an intentionally invalid production plan.

These checks passed for the tested scenarios. They establish feasibility and consistency for the tested cases; they do not constitute a formal proof of correctness for all possible inputs.

## Technologies

- Python
- NumPy
- Pandas
- Matplotlib
- JupyterLite

## Future improvements

- Replace vertex enumeration with a dedicated optimization solver such as HiGHS or a solver accessed through Pyomo, where the environment supports it.
- Benchmark runtime over larger instances and varying numbers of products.
- Compare optimal solutions against an independent solver.
- Explore additional constraints such as labor availability, demand limits, overtime, and inventory.
- Extend the analysis to multiple production periods.

## Key learning outcomes

This project explores mathematical optimization, binary decision variables, resource allocation, fixed setup costs, sensitivity analysis, feasibility testing, and the practical limitations of custom optimization algorithms.
