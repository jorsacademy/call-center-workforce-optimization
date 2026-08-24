# Call Center Workforce Optimization

This project demonstrates how mixed-integer linear programming can be used to improve workforce allocation in a 24-hour customer support operation.

The scenario, data, optimization structure, and implementation in this repository are original and were created specifically for this project. The model is not a reproduction of any published scheduling example.

## Problem Overview

A customer support center operates continuously and must assign a limited number of agents to predefined shifts. Demand varies substantially across the day: overnight demand is low, morning demand increases, and the highest workload appears during the afternoon and early evening.

The optimization model determines which shift each agent should work while balancing two operational goals:

1. Minimize staffing shortages.
2. Control total staffing cost.

Shortage is treated as the dominant objective by assigning it a large penalty relative to labor cost.

## Scenario

The synthetic scenario contains:

- 12 customer support agents
- 8 predefined 6-hour shifts
- 24 hourly demand periods
- Different labor costs by shift
- Agent-specific shift availability restrictions
- At most one shift per agent per day

## Mathematical Formulation

### Sets

- `I`: agents
- `K`: shifts
- `T`: hourly time periods

### Parameters

- `A[k,t]`: 1 if shift `k` covers hour `t`, otherwise 0
- `D[t]`: required number of agents at hour `t`
- `C[k]`: labor cost of shift `k`
- `E[i,k]`: 1 if agent `i` is eligible for shift `k`, otherwise 0

### Decision Variables

- `x[i,k]`: 1 if agent `i` is assigned to shift `k`, otherwise 0
- `y[t]`: staffing shortage at hour `t`

### Objective

The model minimizes a weighted combination of shortage and labor cost:

`Minimize shortage_penalty * sum(y[t]) + sum(C[k] * x[i,k])`

### Constraints

Hourly shortage:

`y[t] >= D[t] - sum(A[k,t] * x[i,k])`

One shift per agent:

`sum(x[i,k]) <= 1`

Eligibility:

`x[i,k] <= E[i,k]`

Non-negativity:

`y[t] >= 0`

## Project Structure

```text
call-center-workforce-optimization/
├── README.md
├── LICENSE.md
├── requirements.txt
├── .gitignore
├── data/
│   └── hourly_demand.csv
└── src/
    └── workforce_optimization.py
```

## Installation

Python 3.10 or newer is recommended.

```bash
pip install -r requirements.txt
```

This project uses Pyomo with the HiGHS solver through `highspy`, so no separate GLPK installation is required.

## Run

```bash
python src/workforce_optimization.py
```

The script prints:

- Solver status
- Objective value
- Agent-to-shift assignments
- Hourly demand
- Hourly scheduled staffing
- Hourly shortage
- Total shortage
- Total staffing cost

## Interpretation

A zero-shortage solution indicates that the available workforce and shift structure are sufficient to cover all modeled demand. If shortage remains after optimization, the result identifies exactly when capacity is insufficient. Management can then evaluate alternatives such as hiring, overtime, revised shift definitions, or demand-management measures.

## Data

All data in this repository are synthetic and intended solely for educational, academic, and non-commercial research use.

## License

This repository is distributed under a custom non-commercial license. Commercial use is prohibited without prior written permission. See `LICENSE.md` for details.
